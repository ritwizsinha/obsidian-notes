
All connection poolers, client libraries need to speak the postgres protocol

##### Mesage Structure

```md
Q 12 SELECT 1
[Message Type 1 byte] [Message Length 4 bytes][Message Content]
```


Handshake is initiated by teh client with any of the following messages
1. The "startup packet"
2. SSL Request
3. GSS Encryption Request
4. Query Cancel Packet
5. TLS Hello

#### Phases
#### Startup Packet
If TLS is enabled then after establishing it a startup packet is sent containing
1. Protocol Version
2. Db Name
3. Supported Protocol Extensions
4. User Name
5. Optional Settings that will be set to GUCs after startup
6. Application_name


##### Authentication
The server sends a number of challenge response messages and client responds
After authentication server will send:
1. BackendKeyData: Contains the query cancellation key that will be needed to perform query cancellation later.
2. Parameter Status: Report current value of GUCs
3. ReadyForQuery flag



#### Running Queries
1. Simple Query Protocol: 
2. Extended Query Protocol: supports parametrs, prepared statements and cursors

##### Simple Query Protocol

Q SELECT * FROM TABLE                                      
										   <- Row Description
										   <- DataRow
										   <- Data Row
										   <- Command Completion
										   <- Ready for Query
##### Extended Query Protocol

Client
-> Parse SELECT * from tbl where id=$1
-> Bind 1234
-> Describe
-> Execute
										   <- Row Description
										   <- DataRow
										   <- Data Row
										   <- Command Completion
										   <- Ready for Query


Describe message returns the row description if provided in EQP
This protocol is sometimes very useful to avoid the pass of hard to plan and long queries over the network and just send the parameters in Bind


Parse can pass two types of queries
named and unnamed variant
unnamed would be for one off queries while named variants can be reused

Bind
Passes the prepared statement name above, destination portal name, values, whether to use binary or text format for each column


Synchronization
After each logical statement client sends a sync message, the server buffers responses and doesn't send anything until sync is called from client or buffer fills up.
Sync closes the implicit transaction.
Any error in between can be sent by the server, making the connection enter an error mode,



##### Copy protocol

Copy in, client -> server

```sql
COPY tbl from STDIN
COPY tbl to STDOUT
```


##### Replication protocol
If we specify replication option in the startup packet a replication connection is created instead of a regular one
Commands
1. IDENTIFY_SYSTEM
2. TIMELINE_HISTORY
3. CREATE_REPLICATION_SLOT
4. START_REPLICATION


##### Physical Replication Mode
START_REPLICATION command enters COPY mode
Both sides can send COPY_DATA messages

Messages include
1. XLogData (Chunk of WAL)
2. Keepalive messages
3. Hot Standy fedback messages


##### Query Cancellation
This is preformed by opening another TCP connection, Instead of sending a startup packet, client sends a cancel Requestmessage, with the secret token recieved in startup packet



#### Building a Connection Pooler
We have multiple query states that need to be managed
1. Prepared Statements
2. Cursors
3. Current Transaction
4. SET variables
5. Caches
6. Cancellation

##### Protocol Extendability
Extensions can change the protocol


#### Problems with Connection poolers
The cancel connection needs to know which pooler to go to to cancel the connection, because other poolers would not have context.