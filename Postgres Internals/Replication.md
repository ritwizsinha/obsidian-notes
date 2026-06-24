1. Log Shipping Replication: Trigerred from the primary by the ARCHIVE command and RESTORE command on the replica
2. Streaming Replication: 
	1. Physical Replication
	2. Logical Replication


For physical replication, a wal sender sends the WAL logs to the walreceiver on the replica standby node


Synchronous Replication
1. Primary waits until Standby until they receive confirmation about write/flushed/applied commit WAL record

Synchronous  Replication Types
1. Priority; format 'FIRST 1(node1, node2)'
   waits for response from node 1, if there is no response sends the request to node 2
2. Quorum; format 'ANY 1 (node1, node2)'
   waits for confirmation from any node



Myths about Sync Replication

Myth 1
Transaction is committed after recieving confirmation from synchronous standby node
Truth
Transaction is always committed locally first
Primary holds locks until commit WAL record is confirmed to be received by teh standby nodes
These locks are released when they recieve confirmation from the required number of nodes
But if the query is cancelled after that, postgres is restarted or connection is broken then the transaction is committed


Myth 2
Synchronous guarantees zero recovery point Objective (RPO) / no data loss
Truth
it depends
if synchronous_commit = local then it disables waiting for synchronous nodes
Transaction becomes visible when the lock wait is cancelled in case of:
1. Query Cancellation
2. Tcp Connection Restart
3. Postgres Start


Myth 3
Reading from sync standby node is like reading from primary
Truth
Transaction replicated on a standby node is visible immediately meaning that if the primary is waiting for one or more nodes then the transaction might not be visible
Never do a write based read on standby.


Myth 4
If sync replication is used we don't need to use pg_rewind
Truth
WAL on primary is written independently from standby nodes and generated not only by transactions (eg vaccum)
Thus pg_rewind might be require