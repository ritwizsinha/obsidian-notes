

Table spaces: The actual storage name space or file path under which a table's data would be stored. All files under the same tablespace and database are located in the same directory. Too many files under a single directory might be a cause of bottleneck for some file systems.
Main reasons for this is as follows
1. Many older file systems do a full iteration for directory contents
2. For smaller directories the inode pointer to the index for all files inside might fit in memory giving a major boost in speed
3. 

#### Forks
A single relation without indexes needs about 3 files to maintain.

**Initialization Fork**
Fork created for tables and indexes not logged in WAL.

**Free Space Map**
Tracking available space between pages. For Indexes since entries need to be added in a particular page and cannot be randomly assigned it only tracks empty pages.

**Visibility Map**
For understanding which pages contain only frozen rows and which do not.



##### Toasts
Provision for storing huge values in pages, as pages cannot partially store a huge row, thus we compress it and divide a single row into various pages.  Postgres aims at having at least 4 rows in a page. 
One reason we should not use select *  is otherwise all the toast table data would be accessed as well and would be decompressed consuming CPU cycles.



### Processes
1. Postmaster: Orchestrator, manages all other processes, listen to oncoming connections
2. startup: Restoration after failure
3. Autovaccum: Cleanup old and stale data from tables and indexes
4. Wal Writer: Writes WAL entries to disk
5. Checkpointer: Executes checkpoints
6. Writer: Flush dirty page to disk.
7. Stats Collector
8. Wal Sender: Sending over the network for replication
9. Wal Reciever: Receiving WAL logs over the network


Shared memory allocated by postmaster for communication
Buffer cache is created over the OS cache does it lead to double caching.

Each connection leads to spawning a new process.
We have a very frequent operation of scanning how many query processes are running thus too many processes makes this slow as well. Connection pooling is the only sane option where a backend serves many queries 


If from the server we keep creating new connections for long running requests, the server itself might become out of ephemeral ports which are provided to the client to get the requests.
Connection pooling solves both the ephemeral port exhaustion problems and the postgres server OOM problem

Process creation per query creates crash isolation.
It also provides memory isolation, where a segfault in one process doesn't crash another


