
### Non Functional requirements
Non functional requirements span the following requirements and could be learnt as
```
CCS FLEDS
```

Compliance, CAP, Scalability, Fault Tolerance, Latency, Environment, Durability and Spike in Load.


### Low Latency Requirement
Always specify time in low latency systems


### Databases
Database type
Schema
Indexes
Partitioning
Replication
Sharding


### Cache
Type of cache (Write Back, Write Around, Write through)
Eviction Policy
Replication
Distribution

### Type of cache
Write around cache, seems useful when we have no useful information to preopopulate  the cache before actual queries thus we populate the cache after data has been fetched from database from the first time.


### Eviction Policy
If we don't have an intelligent measure of which items would be popular, LRU is the best policy


### API Endpoints
While adding API endpoints always remember to add pagination wherever necessary in the response results


### Partitioning
One way of handling partitioning of hot shards would be to add a random suffix to the partition id which we are partitioning by either in the database or in message brokers. We can have a bunch of preset suffixes and we can choose random ones among those to ensure equal distribution is made across partitions


### Tips
Sometimes the  Core API's supported by the system, need to apis other than the functional requirements


### Accounting for Scalability
Move left to right in the design and make changes in the design to make more stable.


### Handling spikes
Adding a message queue between the service and the producer to reduce the load on the service,.


### Job Leasing
To ensure at least once guarantee for execution of some path, we can use job leasing. This basically makes the application acquire a lock on a particular job for some timeout. If it doesn't execute the job in that time then the job lock is released and another process can pick up this job.


### Dynamodb Trick
For time sensitive data where we need to query the latest data, one solution is to partition on the bucket of time for example one hour and sort on the exact time. Thus we could get the latest events without querying various partitions