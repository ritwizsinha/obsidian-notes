[[Batch processing]]

### Tips
1. Depending on the size of data storing data in a hashmap or sorting it are both viable. If the working set of a job is small enough to fit in memory we can use a hashmap but if  instead the working set is huge merge sort works well because it plays well with sequential access in disks.
2. Star Schema is the way of partitioning data to allow for reduced query latency. It has a central fact table and a bunch of de normalized data or dimension tables.



### Joins in Map Reduce

#### Reduce Side Joins
1. Sort Merge Join
	1. Lay out the sets of data in two map and reduce workflows. The map auto sorts the data
	2. The reduce workflow takes that data and merges all the ones having the same key, thus merging the data.
2. Skew Join
	1. Pass the hot keys to a bunch of reducers randomly and then have another map reduce session to bunch all of the keys together.

#### Map Side Joins
1. Brodcast Hash Joins
	1. If one dataset is small enough to bring in memory then it is brought to main memory in a hash table and then joined in the mapper
	2. If the size of the dataset is slightly bigger we can create a read only index for which indexing would be managed by OS page cache and thus we can have an effect similar to hashmap in the memory.
2. Partitioned hash joins
	1. If the mapper is running on partitioned data we can make sure we only bring keys in the partitioned data to memory for joining thus further reducing the dataset we being to memory


### Drawbacks of map reduce
1. Materialization of state after every workflow
2. Reducer has to wait for all mappers to complete before starting


### Streaming
Th tail -f functionality works in a producer consumer manner.