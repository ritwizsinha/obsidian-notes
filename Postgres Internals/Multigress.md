The main problem I see with multi gateway is its RPC request to topo server for configurations, I think there should be hot reloading of configurations and extensive caching to avoid that call.
Moreover we need many multigateways to avoid single point of failure.

![[Pasted image 20260624024832.png]]
It is not clear how the configurations in the global topo server would be provided on each topo cell.  Where does this global topo server even live?

Also what is not understood in this architecture is why would leader election ever be required if there is only one primary, wouldn't that be the leader always?

The answer is that multiorch practice consensus, not the primary themselves, the mulitorchs are responsible for maintaining state about WAL replication and stuff and that information needs to be present elsewhere, so that when a primary goes down, multi orchs are able to ask the leader to perform the failover

By durability policy you mean the full sync replication condition across replicas around different cell.


From the documentation 

"""
The consensus protocol will ensure safety even if the MultiOrchs are not able to communicate with each other.
"""
This doesn't seem realistic, for if their are let's say 5 cells and 5 multi orchs, one is already appointed as the leader and the primary is present there (let's say cell 2), now if the cell 2 becomes network partitioned what happens ? A new election begins among 4 remaining nodes, 1 becomes the leader after it gets 3 votes and then it tries to run a failover and create a new primary while the network partitioned cell still is serving writes, then there would be an update in the global topo cell and in the local topo cell for the partitioned cell which would finally make multi gateway in that cell abandon the cell as primary and issue cross cell writes. That too happens if the multi gateway has not cached the primary beforehand



The thing with multigres is that they are trying to achieve HA with full sync replication, but there are certain edge cases which we can't really take care of during full sync replication. So they aim to have different durability policies controllingt the full sync replication but the edge cases would still exist




### Generalized Consensus
Main use case of consensus is durability across distributed systems and it solves availibility
Another important use case is halt in case of unrecoverable failure

##### Definition
Consensus systems should guarantee that the every request is saved elsewhere before it is completed and acknowledged
If there is a failure after the acknowledgement we should be successfully be able to recover from it and resume operations from that point


##### Elsewhere
This is very vague and is user defined constraints
For example the requirement might be Cross-AZ for 8 nodes in 4 AZs, how do we formalize this requirement in Raft such that the availablity zones are covered. One solution is to have 2n + 1 availabliity zones and consensus would only be reached when we have commit to n  + 1 nodes where n + 1 = 4 so total AZs required. = 7. But we still can't say that the given AZs are following the leader accurately. This is what generalized consensus aims to solve. We want custom durability guarantees instead of a durability hardcoding like majority quorum
Flex Paxos said that you don't need majority quorum for things to work

#### What does a consensus system do
1. A consensus system executes a series of consistent distributed decisions made by multiple agents. For example in Raft a leader would be an agent. As an agent it receives request and makes them durable

##### Rules
1. Every decision is a distributed and durable decision
	1. A distributed decision must be applied after durable first 
	2. A decision that has been durable can be applied
2. Decisions must be applied sequentially
	1. Every agent must revoke the abiility of all previous agents to make further progress before taking any action
		1. Every agent must provide a way for future agents to revoke its claim to leadership
	2. Every agent must discover decisions that were previously made durable and not applied and honor them

![[Pasted image 20260624152233.png]]

In this case if N1 has to be stepped down, we can tell N1 that it is not the leader anymore from an external agent and then ask N2 to start the election. This is the same thing as timeout but from an external agent. But sometimes you will not be able to reach N1 over the network partition, or if N1 is dead. Thus timeouts are useful in the node itself. Another idea is to tell N2 and N3 to stop accepting from N1. This indirectly makes N1 revoke leadership. This is called revocation


##### Coordination
Inside Raft we have generally 3-5 nodes because increase in the number of nodes means more acknowledgements needed from the quorum taking more time. With 3-5 nodes, its easy for the nodes themselves to handle the leader election and health checks, but with multiple nodes it gets increasingly unwieldy. This logically separate task is handed over to coordinators. Thus it has the following roles:
1. Perform Health Checks on the nodes
2. In case of failure perform failover by appointing a new leader and ensuring that the existing leader durablitiy operations are honored.
3. Coordinator could also apply a planned leader change.
The coordinator doesn't directly kill the old leader.
Instead it recruits enough nodes into a newer term so that the old leader loses authority.


##### Recruitment by Coordinator
Coordinator can send a higher term number to a node to recruit it.

##### Revoking leadership 
Each node claims leadership in its reign or term, if a coordinator recruites majority under a new term or recruites the leader under a new term, it revokes that leader's leadership
##### Postgres Full Sync Problem
Postgres full sync commits locally first and then sends to the active standby nodes, breaking rule number one of persisting first before applying decision. But this is not a big of a problem where we can apply rewind on the new leader to persist these changes later

Questions
1. How does the client know of what multigateway to hit everytime, how would we make that visible to developers?
2. If we are implementing a generalized consensus over full sync replication what are we doing about the edge cases?