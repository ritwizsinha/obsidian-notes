[[Youtube Video Notes]]
[[System Design]]


https://www.youtube.com/playlist?list=PLa7VYi0yPIH14oEOfwbcE9_gM5lOZ4ICN

### Overview
Import Data using Kafka Connectors or Kafka Producer API
Export data with Kafka Connect and Consumer API
Compute Layer: Create, Query and Process the Streaming data using KSQLdb
Kafka Record: [timestamp, key, value, Headers]
Keys and Values are just byte arrays, thus we can use any encoder or decoder we like.
Partitions need to be processed serially.

Kafka = Compute Layer + Control Plane + Data Plane
Control Plane: Responsible for handling metadata of cluster
Data Plane: Actual Data.



### Broker
Broker handles requests from producers and readers for records. Kafka Producer Client hashes the key or chooses topic partition randomly if the key is not specified. 
Producer API batches the records in a producer batch before sending it to the broker, which compresses those records. Producer has control when the batch needs to be drained and sent to the Broker by knobs of size and time.

![[Pasted image 20250330152925.png]]

A single broker handles a bunch of clients. All of the requests accumulate in the socket receive buffer and is picked up by a network thread. The network thread is sticky and handles one client once it picks up one.  This network thread pulls data from the socket receive buffer and pushes that to the shared request queue. IO thread picks up the request and adds it to the commit log which is on disk. Each log file also has an index file which provides mapping from offset to position of record. Broker only returns response when data is replicated across brokers fully. While we are replicating the data we store the records in the purgatory map and IO threads picks up other useful work. Once data is replicated we push response to per network thread response queue. Network thread ensures ordering of requests for a single client. In the network thread we do **zero copy transfer** from the file directly to the socket send buffer


#### Replication in Data Plane
Follows the Leader, follower model.
ISR: In sync replica, set of replicas that have the most caught up data. Producer sends data to the leader node and consumer can be configured to read only from the leader or from the consumer 
##### Leader Epoch
For each leader, it needs to persist the duration of time for which it was the leader so each of the record which is appended has the leader id inside of it which gets replicated to other nodes.
	We also need to ensure that all the new records in the leader is saved in nodes in ISR. Leader keeps track of min offset(High Watermark) from the followers marks all records previous to it as committed. After tracking the high matermark the leader sends it to the followers in the FetchRequest response


##### Leader Failure
New leader elected from the ISR and propogated through the control plane


#### Kafka Control Plane
![[Pasted image 20250401014013.png]]

##### Legacy
Control plane managed using Zookeeper. One broker is picked as a special controller and it is responsible for changing the metadata for the cluster. The metadata is persisted in the consensus service which is part of zookeeper.


##### KRaft Mode
Consensus service within the kafka cluster.
These controllers maintain an in memory metadata cache.
For internal metadata we have a separate topic with a single partition
1. Active controller writes a metadata record to the __cluster_metadata topic.
2. Controllers and brokers replicate this data.
3. Controllers then update its metadata cache.
4. Metadata replication depends on Quorom instead of ISR
5. Records are fsync to disk prior to commit