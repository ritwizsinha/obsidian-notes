Wal is a constant record maintained to keep record of changes done to the db that have not yet been persisted to disk.
1. Page Modifications in Buffer Cache
2. Transaction Commits and Rollbacks
3. File operations (Creation and deletion of files when tables get added or removed)


#### Wal Storage
The insert position in the WAL log is called the Log Sequence Number(LSN). Wal logs are stored under the data directory as a set of segment files, each 16 MB in size.  Each segment contains several pages. All the segment files are stored under a single directory. We should ensure that a write to disk is an actual write and not something the OS has buffered, as that can cause loss of logs. We have only one WAL for the whole database server

#### Wal Structure

Header
1. Transaction Id
2. Resource Manager 
3. Checksum
4. Entry Length
5. Reference to previous WAL entry

Wal data content itself can have different formats and meaning. It could be a page fragment, index type, for tables. That is why resource managers exist to identify the data and make the changes.



##### Wal Durability
While a transaction is going on, the WAL entries get appended to a local cache, as soon as the transaction is done, the done is marked only after storing to a durable medium.


##### Checkpointer
For recovery purposes, we can't store the WAL logs since the beginning of time, thus we need to write all dirty pages to disk periodically to create a new point from which WAL's could be read. But all dirty pages to disk at once would be slow and would hang. Thus we have an interval for checkpointing. Checkpointing takes a certain amount of time and after checkpointing is done, we write a new WAL entry representing that. pg_control refers to the point for the last checkpointing. 
For background writing we have a separate process called bgwriter which uses the same clock algorithm for writing to disk


##### Data Recovery
Startup process takes care of data recovery



#### Performance

Since WAL writes are sequential it is worth it to write it to a separate storage symlinked to pg_wal in data directory.
Wal writes can be SYNC ie persisted to disk when transaction commits or ASYNC ie WAL writes happening later than transaction commits. Because Disk writes are still slow, we have a commit_delay parameter which intentionally delays commits so that more transactions can finish and then can also get persisted. This is really useful in short lived transaction workloads

Asynchronous commits are faster than synchronous ones since they do not have to
wait for physical writes to disk. But reliability suffers: you can lose the data commit-
ted within the 3 × wal_writer_delay timeframe before a failure (which is 0.6 seconds
by default).



#### Corruption
To catch issues in good time, Postgres always protects WAL entries by checksums.

Database pages are usually 8KB but writes to Disk are smaller in terms of around 4Kb, thus during a failure a page might have been written partially


#### Other Usecases
WAL can be used for other purposes too. Postgres
provides minimal, replica, and logical logging levels. Each level includes everything that is
logged on the previous one and adds some more information.

