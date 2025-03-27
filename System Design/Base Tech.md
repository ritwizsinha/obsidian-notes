# Redis

Single Threaded in memory data store.
Redis is a data structure server, that means that redis values can be
	1. Lists
	2. Json
	3. Sorted sets
	4. Bloom filters


#### Redis Slot
Hash of a key, CRC(value). This slot is distributed across various nodes in redis in a bucketed fashion, and the client is aware about all those instances.



##### Nodes
Nodes in cluster communicate using gossip protocol and the client when starting up is made aware about all hosts available.


##### Usage
1. As a low latency way of checking whether we need to go to the database for a particular value.
2. Rate Limiter
3. Publisher Subscriber


##### Hot key problem
A single key is read a lot or written a lot
Solution: Add a random suffix to the key and distribute it well among the nodes.


##### Redis Streams
Ordered list of items that are given timestamps as id. To create a queue of items to be processed, we can use streams. Then we can create a consumer group just like kafka, which points to the stream. The consumer group can have a bunch of workers and at a given moment only one worker can claim the latest stream item, if the consumer fails the item is reclaimed by the consumer group and passed on to another consumer. The consumer group only offers at least once guarantee and item can be processed twice in some settings

##### Sorted sets

```
ZADD tiger_tweets 400 "id1"
ZADD tiger_tweets 3 "id2"
ZREMRANGEBYRANK tiger_tweets 0 -5 Remove all but the top 5 tweets. 
```

This would work in case we redirect all keys to a single node but if we are trying to do load balancing of heavy reads and writes, this might be problematic as we would need to find across all nodes and get the top 5

###### Sorted Sets with Gespatial Index
Sorted by longitude and latitude and search using anchor point and redis.
```
GEOADD BikeRentals longitude latitude identifier

GEOSEARCH BikeRentals FROMLONLAT longitude latitude BYRADIUS 5km WITHDIST
```

Again sharding this is a problem


##### Pub Sub
Servers connect to redis an announce publications that they are going to make, and other servers connect and subscribe to the publications. Redis pubsub is at most once delivery. If we want to guarantee that messages do arrive, we need to try something else.


# Kafka
Partition key
Consumer Groups
Bunch of brokers(server) -> Have a bunch of partitions.
Each partition ordered
Topic: Bunch of partitions(Topic is logical grouping partition is physical grouping)
Stream: Take messages but don't acknowledge
Message Queue: We need to acknowledge messages as well.
Message Format: key, timestamp, value
Kafka Controller
Offsets denote location of append only data.
Kafka ensures durability and availability using replication(leader follower replication)
Controller ensures that leader of a partition is distributed across machines to ensure load balancing
messages < 1Mb
Kafka is always available, sometimes consistent


# API Gateway
Request Validation
Request Routing
Auth
Terminate SSL Conditions


# Cassandra
It implements a partitioned wide-column storage model with eventually consistent semantics. It is a distributed database that runs in a cluster and can horizontally scale via commodity hardware.
Keyspace: Contain a bunch of tables
Table: Container of Rows
Row: List of data with primary keys
Column: Wide column database so all records in a table need not have the same data. Last write wins for conflicting writes.
Partition Key: Same as in dynamodb
Clustering key: sort_key in dynamodb
Consistent hashing among nodes containing data partitions.
Virtual Nodes. 
Replication among vnodes not pointing to the same node.
No ACID only atomicity provided
Depends on quoroms where r + w > n
No central node, communication using gossip protocol
LSM tree storage

Ticketmaster Popular booking problem
Cassandra is a great choice in systems that prioritize availability over consistency and have high scalability need


# Dynamodb
Partition Key
Sort Key
Consistent Hashing for partition keys
B+ tree for sort keys
GSI 
1. **Global Secondary Index (GSI)** - An index with a partition key and optional sort key that differs from the table's partition key. GSIs allow you to query items based on attributes other than the table's partition key. Since GSIs use a different partition key, the data is stored on entirely different physical partitions from the base table and is replicated separately.
2. LSI: Secondary BTree index.
3. Understanding the physical storage difference between GSIs and LSIs is important. GSIs maintain their own separate partitions and replicas, which allows for greater query flexibility but requires additional storage and processing overhead. LSIs, on the other hand, are stored locally with the base table items, making them more efficient for queries within a partition but limiting their flexibility.

Use case for GSI, chatting application with Partition key as chat_id and sort_key as message_id and GSI on user_id and message_id.
**Eventual Consistency** - This is the default consistency model in DynamoDB. It provides the highest availability and lowest latency, but it can result in stale reads. This means that if you write data and then immediately read it, you might not see the updated data right away. With this configuration, Dynamo is a AP system displaying BASE properties.

**Strong Consistency** - This model ensures that all reads reflect the most recent write. This comes at the cost of higher latency and potentially lower availability. With this configuration, Dynamo is an CP system displaying ACID properties.


# Postgres
Full Text Search
Geospatial searching
The index type used here (GIST) is specifically optimized for geometric data, using R-tree indexing under the hood. This means queries like "find all posts within X kilometers" or "find posts inside this boundary" can be executed efficiently without having to check every single row.
Covering Index: An index over the entire row, faster to read, because two access not required, makes writes slow
Each index needs to be updated to reflect the changes. Like table data, index changes also go through the WAL for durability. This is why having many indexes can significantly slow down writes - each index requires additional WAL entries and memory updates
- Simple inserts: ~5,000 per second per core
    
- Updates with index modifications: ~1,000-2,000 per second per core
    
- Complex transactions (multiple tables/indexes): Hundreds per second
    
- Bulk operations: Tens of thousands of rows per second

To optimize writes in Postgres we can offload that to kafka 
Partitioning improves writes because different database sessions can write to different partitions simulateneously.
Read Committed
Repeatable Read
Phantom Reads
Serializable


# ElasticSearch
Document: A json object
Index: Collection of documents, unique id and fields