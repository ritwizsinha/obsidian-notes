[[System Design]]

1. Cloud Native Data Warehouse, implemented to be run on AWS, GCP etc.
2. Separate Storage and Compute
3. Support for semi structured data.
4. No user tuning.



#### Storage vs Compute
Hadoop allows us to run computations where data lives
- Achieves very nice data locality
- "Shared Nothing Architecture" multiple nodes with separate CPU and storage.
- Tables are horizontally partitioned across nodes.

##### Problems with shared nothing
1. Scaling cluster size
	1. Table data lives on nodes doing query computation
	2. If we add new nodes we need to rebalance new data.
	3. This degrades performance as it uses resources on nodes
2. Online upgrades are more challenging
	1. If each nodes takes care of data, taking down one of them makes the data unavailable, which might be somewhat replication

##### Scaling Resources and running Hadoop in the Cloud
We need to scale our compute and storage independently, because if we store a lot of data and need a machine with sufficient compute, we need to provision huge expensive machines which would be under used most of the time. 
Thus Snowflake uses Amazon S3 for object storage
They provision separate set of servers to read from S3.
![[Pasted image 20250406155408.png]]

#### Architecture
The data stored in S3 is very similar to Parquet format where we have a bunch of columns stored together and after every chunk of columns we store rows again.

##### Virtual Warehouse
Collection of worker nodes that run query computations.
- Reads S3 data which is the expensive part
- Caches the S3 data on local disk and replaces using the LRU policy

To maximize cache hit rate, the same worker works with the same part of the table, this is done using consistent hashing.


These virtual warehouses are stateless apart from caching which irrevelant for consistency.

##### Data File Format
Snowflake uses a file format similar to Parquet 
![[Pasted image 20250406160429.png]]

##### File Mutability
All inserts and deletes to snowflake creates new files
- Incorporated atomically using metadata database.
- We can also implement MVCC because each version change is a new file


##### Indexes
Does not support indexes
 - Goes against the philosphy of Performance Tuning
 - Requires duplicate storage, makes updates more expensive.
 - Relies on random data access.

##### Predicate / Projection Pushdown
For each row group, we store the min and max values in that row group for pruning. If we have values not in range then we don't read the row group


##### Dynamic Pruning
While we are performing some sort of join
```sql
select t1.id from t1 inner join t2 using id
```

Let's say we do a hash joing, building a hash table over t2. If the hashtable for t2 is built over 2 nodes, we can have some metadata regarding the properties of data in the node 1 and 2 and even have some sort of bloom filter over it. Thus doing further pruning



##### Push vs Pull based execution
Traditional dbs us pull based execution, this is just a bunch of functions calling other functions.
![[Pasted image 20250406162246.png]]
Kind of like recursion or doing DFS until we get to the base. Push based execution is like BFS
###### Drawbacks
1. Bad handling of reused data
2. We might be doing scans twice in joins 
3. Tight Loops
4. Hard to do multithreading
5. Harder to schedule



##### Push Based Execution
![[Pasted image 20250406162708.png]]

###### Drawbacks
- When the nodes need to necessarily compute computation and then only they can pass on the data, for example sorting,  somethnig like sort merge join
- The bottom operators might do more work than necessary, for example select *  with limit might return a fixed amount of data instead of the data with limit



##### Query Optimization
