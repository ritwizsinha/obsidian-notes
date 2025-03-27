1. High consistency in all activities per clients.



#### Limit Order Book
Exchanges keep tracks of bids and asks.
We will see more trades executed when there exists both bid and ask where bid price >= ask price. Atomicity in trades.


#### Matching Engine
Needs to have as little IO as possible, everything in local memory.
Matching should be deterministic
##### Networking in matching engine.
A bunch of systems might be interested in what matching engine is doing and what trades are being executed
	- Clients sending orders
	- Banks/Clearing companies
	- Data market vendors
	Connecting to them using TCP might use a lot of compute thus UDP multicast is preferred.
	All of these exchanges should be provided data fairly. TCP requires a bunch of one-one connections thus can't publish all at once, thus we use UDP multicast. 
	But with UDP we loose flow control and reliability.
	For dealing with out of order messages we use "retransmitters". There are retransmitter nodes that cache messages sent from UDP multicast. If we miss a particular message we can send a request to the retransmitter nodes and get the missed messages.


##### Matching Engine Fault Tolerance
The matching engine runs completely in memory thus we need backups of the data . We need some sort of Zookeper/heartbeating to swap on failure. We have multiple options here 
1. Recreate state in a replica by sending it messages along with the active, but this might mess with the order of messages because of network latencies.
2. **State Machine Replication**: Backup listens to the output of the primary and updates its state accordingly 
3. Partitioning: We have a single leader here (the matching engine). We can partition matching engines on symbols but if there are multiple symbol orders that might take a distributed transaction which would be slow.



## Robinhood
#### Functional Requirements
1. Users see the realtime value of financial products
2. Minimize the total number of servers that listen to the exchange.



#### Race conditions
Robinhood is not an exchange, thus if we cancel orders in Robinhood, and it has already forwarded that order to the exchange which has been filled then we might encounter the problem where the robinhood accepts the cancellation but exchange has already executed


#### Realtime pricing information
We need a bunch of servers to connect to the exchange's UDP multicast stream. This can be in active-active or active-passive configuration. With active-active we have zero downtime but it is more expensive than active passive. Active passive only one server listens to the updates and other only starts listening once the other is down. These servers can be called the exchange layer and store the realtime prices. We can have another set of pricing servers which listen to this exchange layer and takes care of communicating the realtime data with the user and sending realtime prices.


#### Fault Tolerance.
When everything is kept in memory for a particular server, for fault tolerance we need to do state machine replication with failover using Zookeeper


#### Pricing Layer Load
The pricing server can get overloaded with popular assets
For Exchange Publishers: If there are a lot trades and open orders we can throttle how often publishers publish the data.
For Pricing Servers if their are too many users listening to the updates of a particular security: Pricing Forwarding Servers, a single server pushes updates to a bunch of servers which the clients connect to. This can be called re sharding