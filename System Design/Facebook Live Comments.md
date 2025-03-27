

#### Learnings
1. Websocket Scalling
2. Redis Pub Sub
3. Redis CDC
4. Websocket reconnections
5. Consistent hashing with websocket servers, so that minimal users are disrupted and co locality is taken advantage of.
6. SSE is good usecase where the read/write is highly skewed, with low writes from client and more reads, SSE is the best.
7. Important to Identify that here the writes << reads, thus SSE would do.
8. Kafka has issues with dynamic subscriptions and unsubscription, because of its pull based model