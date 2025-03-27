#### Learnings
1. Apart from lazy deleting the entries whenever they are accessed for TTL, we can also have a background job for other entries which are not accessed that frequently. 
2. Implementation of Gossip protocol among replicas to have a leaderless kind of replication
3. How to handle hot keys?
	1. Read Hot Keys
		1. Read replicas and doing random addition to the key to distribute in consistent hashing is the only option.
	2. Write Hot Keys
		1. Batching writes. Delay in sending updates and works best for counters.
		2. Another approach is just sharding the key at a more granular level, for example we have a key xyz having too many writes, we shared it to xyz:1, xyz:2 etc and write to any one randomly, this makes the writes distributed, at the read part, we need to accumulate all.
	