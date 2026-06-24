
A transaction is a set of operations that takes the database from one correct
state to another correct state (consistency), provided that it is executed in full (atomicity)
and without being affected by other transactions (isolation)


**Dirty Read**: Transactions are able to see the changes made by other uncommited transactions. These are allowed below READ UNCOMMITED
**Lost Update**: Two transaction acting on a single row and updating it at once leading to one of the updates being missed. These are not allowed at any isolation level.
**Non Repeatable Reads**: Seeing the same row two times in a transaction with different values.
Permitted at READ UNCOMMITED and READ COMMITTED

Phantom Read: New rows get discovered as a transaction runs the same query. Permitted on READ UNCOMMITTED, READ COMMITTED and REPEATABLE READ


Locks don't solve the problem where new rows get added and phantom reads can always occur thus Snapshot Isolation was needed

### Snapshot Isolation
Multi version concurrency control is another flavor for Snapshot Isolation where multiple rows for the same row exist for different transactions. MVCC solves all the traditional issues present and categroizes them into different isolation levels. But introduces issues of its own


**Read Skew**: With Read committed isolation level there is the issue of running multiple queries in a transaction showing different results as background transactions started before start committing.



#### Pages and Tuples

##### Page
Header + Item Pointers + Space + Items + Special Space (used mainly by index pages)
##### Page Header
Checksum,sizes of other parts


##### Items
Rows are stored as row versions instead of rows because of MVCC but index pages don't have row versions
Indirect Addressing
Postgres follows a two layer indirection, the address of a tuple is a 6byte tuple id, it consists the page number of the main fork and the item pointer offset, the item pointer then points to the actual tuple containing the data. The pointer is updated not the TID when the tuple location is changed. This pointer contains tuple offset from page start, tuple length and bits defining tuple status. All free space available in a page is aggregated in one chunk, thus there is no free space search ever

Row header
Contains xmin, xmax determining the range of this row version, null bitmaps to show which columns have null values, infomask -> info bits, ctid -> pointer to the next row version
Tuple padding is the same problem we see with struct padding

Time flows by in postgres not in terms of units of time but by transaction ids. When the row is created the txid of the insert is added as an xmin id, when it is deleted the txid is added as the xmax id.
Thus updates are DELETES + INSERTS where xmax is set to the txid of the UPDATE transaction and xmin of the next row version is also of the UPDATE


##### CLOG
Two bits information persisted in files about transactions. Stored under pg_xact. We store these separately and don't update the tupels themsleves, because there might be hundreds of tuples to update


If after DELETES I do an abort the the XMAX_ABORTED is set to true and we don't pay attention to the xmax field now.

Index row headers don't contain Xmin and Xmax values.
An index entry page can contain many version of the same tuple, this is resolved at vaccumming time.


##### Saving TXIDs
To use these ids sparingly read only queries get virtual XIDs = Backend Process ID + Sequential Number. Virtual XIDs exist only in ram. Creating a new transaction is ID is surprisingly involved because if in a connection pool where multiple backend processses are running we need to create a transaction ID, we need to make sure there is no race with some transaction being created in another backend. We acquire a lock in shared memory on the next_txid and that requires synchronization among various other backends.




#### Snapshots
Visible versions of all the different rows constitute a snapshot. Each transaction uses its own snapshots.
At READ COMMITTED isolation level a snapshot is taken at the beginning of each statement, remaining active only for the duration of the statement.
At REPEATABLE READ and SERIALIZABLE levels a snapshot is taken at the start of the first statement in a txn


System doesn't know when a transaction ended, although we do have a tracker in ProcArray in the shared memory for the actual status of the transaction, once completed, we can't find if it was active at the time of snapshot creation. 
Postgres can't seem to create temporal queries because of MVCC, mainly because when we say what would the query look like if I queried at time T or with a txid of A, the problem is all the queries before txid A aren't clear when they finished, so whether to include them in the snapshot or not.

A snapshot consists boundary

xmin: the id of the lowest active transaction, before this everything is committed,
xmax: the id of latest committed transaction + 1
xip_list: all transactions between xmin and xmax that are running in the ProcArray are included here

