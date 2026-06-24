
Separating the partitionining of data from where it actually resides. Suppose we have a sharding technique where we shard by user id. This sharding would be great, but what if there is a hot partition ? What happens to the application code if a new shard is added. The solution again is indirection, we create an indirection layer from the number of nodes to the number of shards. We create thousands of logical shards and then in a separate db table we map these shards to processes. When one node gets overwritten, we start migrating some of the shards from that machine to other machine using streaming replication. Once this is done we just need to update the mapping table.

With sharding setup the auto increment id solution quickly starts breaking because we have multiple db hosts. 
Creating UUID's are huge and tough store and order by and contains no idea of recency
We can create a Postgres ID function with 41 bits of system clock, 13 bits of shard id and 10 bits of sequence number. There is no external dependency


#### Problems with Write architecture of Postgres
1. Table and Index bloat due to MVCC
2. Autovaccum autotuning complexities
3. Version churn from tuple copying
4. Increased index maintenance overload


Sometimes running the join the backend server is better than the db.