Region: A geographical area for serving isolated traffic from a data center
Zones: Different data centers within the same region
Cell: Group of servers and network infrastrcuture isolated in an area, isolated from failures in other cells
Keyspace: Is the logical illusion of a single database, for sharding enabled multiple MYSQL dbs are present in a keyspace, for a single shard, just that host is present.
Tablet: This is a combination of a mysql process and vttablet on the same machnie
$$