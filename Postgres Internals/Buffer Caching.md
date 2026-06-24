Postgres has a buffer cache along with the Os cache. To avoid the double caching some databases do a O_DIRECT io to the file system to get the data instead of going through the cache.
Postgres reads and writes all data in buffered operations. using O_DIRECT causes the OS to store the data directly in the destination instead of copying it in the OS address space thus allowing DMA.

Using O_DIRECT would need a significant rewrite in Postgres because with O_DIRECT, postgres would need to implement its own pre fetching, and that pre fetching is not going to be wait and fetch but a number of parallel requests at a time, thus the need for async IO is necessary. Thus Postgres uses buffered IO ie from cache + disk

All buffer Ids are stored in a hash table. Its in server's shared memory making it available to all backend processes.
Pinned buffers are not evicted.
All writes are done in the buffer manager itself.
There are no immediate writes to disk, we first write to a buffer, and store it after some time.
Buffer eviction using clock algorithm.

Bulk operations use smaller buffer rings 


#### Bulk Reads Cache and Eviction Strategy
Let's say we have whole table read and filter, we would create a cache ring, in the cache ring each of the buffer doesn't wait for write to happen before being evicted, instead they evict a buffer from the ring that already has just read data. This is done to isolate a sequential scan to few buffers and not pollute everything. Only sequential reads use this buffer ring, not writes.
Toast values read bypass the buffer rings

A typical recommendation is to start with 1/4th of the RAM and then adjust this setting as required.
For multiple selects going through the same ring buffer pool, the other queries are provided the current values and as the first query is done, we load up the remaining pages for query 2 which are still left.