1. For sharding use cases customer's generally want to shard on a single id which is a universal identifier in one table and foreign key in other tables. They want pgdog to work transparently without repartitioning the data just because of migration costs. For example a customer id is the common link and then queries like ``
   ```sql
   SELECT * FROM users u JOIN devices d ON d.customer_id = u.customer_id AND d.user_id = u.id WHERE u.customer_id = ‘<uuid>’
   ```
   
   should route to just one shard.
1. Rebalancing shards with application level shard queries and table level partitions. Mentioned in this [blog]([https://blog.vectorchord.ai/3-billion-vectors-in-postgresql-to-protect-the-earth](https://blog.vectorchord.ai/3-billion-vectors-in-postgresql-to-protect-the-earth "https://blog.vectorchord.ai/3-billion-vectors-in-postgresql-to-protect-the-earth"))
2. Hosting own servers is very useful and in mind for multiple companies because of new companies like Neon and Supabase going down and impacting customers.











### Companies Interestd in Sharding solutions
1. Render.com
2. VectorChord


#### Customer Envs
I have more then 20 env in preprod with small database and traffic to migrate first. Concerning production it's 1 big database with 4 replica, but I will probably not use the feature concerning routing read/write query because it's already done on the code. Those database can have pretty heavy traffic Peak as 24mib/s on master and 54mib/s on the replica



#### Work streams Identified
#### FFI Interface with RUST and C
   <details><summary>Context</summary>"1. ### Lev_:_ 
    
    Hey. What sharding function would you want to add? Can probably be added in core, plugins are still not quite ready
    
2. ### _[_10:46 PM_]_Hutch_:_ 
    
    List or Range based sharding
    
3. ### _[_10:48 PM_]_Hutch_:_ 
    
    essentially it's for a very coarse grained spatial partitioning scheme
    
4. ### _[_10:50 PM_]_Hutch_:_ 
    
    looking at the plugin api it could be very simple logic inside `pgdog_route_query`
    
5. ### _[_10:50 PM_]_Hutch_:_ 
    
    basically a match statement fed by config
    
6. ### _[_11:06 PM_]_Lev_:_ 
    
    Cool sharding idea. Yeah, that function isn't being called at the moment. The FFI interface between C and Rust was really tough, I needed to pass a lot of context into the plugin and back. Not giving up on it, just not building it this very moment. I think range-based sharding is pretty cool, let me know if you want to discuss it further. I'm not opposed to having multiple sharding functions in core. I think the more the better. Ideally though, they match up to what Pg partitions can do as well (so range works too), so people can shard both in the pooler and inside Postgres.
    
7. ### _[_11:10 PM_]_Lev_:_ 
    
    That being said, if you want to push on the plugin API, I'd welcome some ideas there too. It would be great to make that work. Just the FFI interface is rough."</details>   




#### Refactoring the sharding code 
<details><summary>Details</summary>"1. ### Hutch_:_ 
    
    anyway -- if you have any thoughts on where it could be added I can start poking around
    
2. ### _[_11:21 PM_]_Lev_:_ 
    
    Yeah for sure: [https://github.com/pgdogdev/pgdog/blob/main/pgdog/src/frontend/router/sharding/mod.rs#L40](https://github.com/pgdogdev/pgdog/blob/main/pgdog/src/frontend/router/sharding/mod.rs#L40 "https://github.com/pgdogdev/pgdog/blob/main/pgdog/src/frontend/router/sharding/mod.rs#L40")
    
3. ### _[_11:21 PM_]_Lev_:_ 
    
    That code needs refactoring, there are a few redundancies
    
4. ### _[_11:22 PM_]_Lev_:_ 
    
    shard_value is for text-encoded values, shard_binary is for well, binary-encoded values. You want to support both encodings, especially if you're using Python & asyncpg/SQLAlchemy.
    
5. ### _[_11:22 PM_]_Hutch_:_ 
    
    got it
    
6. ### _[_11:22 PM_]_Lev_:_ 
    
    For your thing, you're gonna need to add more context, right now it supports vector/centroid-based sharding and regular hash mod shards sharding.
    
7. ### _[_11:23 PM_]_Lev_:_ 
    
    Btw, if you want, we can start a Slack channel. I'm looking for early design partners, not sure where you're working, but might be a good fit.
    
8. ### _[_11:26 PM_]_Hutch_:_ 
    
    sure thing -- I'm at a non-profit so no noncompetes or license issues
    
9. ### _[_11:26 PM_]_Lev_:_ 
    
    Super!
    
10. ### _[_11:26 PM_]_Lev_:_ 
    
    DM me your email"</details>


#### 
   