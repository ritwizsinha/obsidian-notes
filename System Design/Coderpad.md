![[Pasted image 20250306171214.png]]


#### Learnings
1. This design is also heavy on the UI part, thus we need to take that  into consideration and design accordingly while discussing the UI element.
2. **Batching needs to happen to ensure that we don't overwhelm the system and database with updates. But at the same time we need realtime transfer of data to clients. To ensure that we pass small events to the primary server and then aggregate them in Kafka or some in memory queue and then store to the database.**
3. UI local storage can also be useful
4. Stateless AWS lambda might be used for code execution. We need to take care of security and isolation of code. For avoiding infinite loop, memory overconsumption, we can specify the memory and time limits for the container.
5. We need to make sure the clients for a session connect to a particular machine, this can be done using the API proxy or a reverse proxy.


### Scaling Websockets
##### Websockets
We send a normal HTTP connection with the upgrade header and connect to the server.  They then use the http session for websocket. When the reverse proxy sees a upgrade header to websockets it creates a persistent connection to the machine and becomes stateful, where it now streams the changes from the server to teh client and vice versa. To scale websockets, what can be done ![[Pasted image 20250306175041.png]]
What we do here is a layer 7 proxy connects the clients to certain instances of wesocket servers. The websocket servers, then connect to a redis instance and subscribe to a topic in redis. Whenever someone sends a message the websocket server gets it and sends it to redis which then notifies the subscribers, which are other websocket servers, and finally they can send that data to the clients.



##### Follow ups
1. How to scale websockets
2. How do we maintain online indicator feature ?
3. How to implement session replay?
4. How do we implement collaborative editing ? 
5. How to support for drawings?


#### Polling vs Long Polling vs SSE vs Websockets
- Polling is wasteful because of overflowing the network with useless requests. 
- Long polling is an optimization where the server holds the connection, until it has data available and then sends the data. This is disadvantageous in situations where we are doing frequent long polling, because a lot of headers need to be passed around. It is good if we rarely need data.
- Websockets are expensive to setup but no header transfer after that, so effecient. This might be wasteful if the updates are few and far between.
- Server sent events: Connection is only from server to client. It has less headers/time spent in creating the connections, but potential waste of resources with persistent connections. This always restablishes a connection if the connection is broken.
- Automatic reconnections in server sent events might cause a thundering herd problem. A random jitter can be used to fix the thundering herd, ie we start the reconnection after a random offset of time.


#### Live Collaboration
https://www.youtube.com/watch?v=YCjVIDv0zQY&t=10s


##### Operational Transform
Centralized server takes in concurrent writes and transforms them if necessary and then send back to clients.  This suffers from the limitation where one server needs to see all the writes, thus a lot of load on one server


#### CRDTs
We want to avoid sending writes to a single server, before sending it off to the clients, or else that server will become a bottleneck.
CRDTs are when we have data living on multiple servers such that when we eventually merge the CRDTs, all of hte nodes converge on a single value.