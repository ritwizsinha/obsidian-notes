
1. Increasing the queue size in a server will negatively affect latency in manners we might not able to define or give an SLA for as each request might take separate time.
2. Process local queues are very helpful for 
	1. Decoupling requests and response
	2. We can absorb short time bursts of requests
3. Time is a hard thing to trust in distributed systems. One thing we should never do is get timestamps from two different systems and compare or difference them as both might be deviated from NTP in different directions leading to an incorrect duration value.
4. Depending on time on multiple nodes might be dangerous for something like schema migration which would take place in different nodes at different times leading to inconsistency in decoding the data in case if that particular node didn't get the schema update at the same time.
5. Toxiproxy can help to simulate network problems: limit the bandwidth, introduce latency, timeouts, and more. 
6.  CharybdeFS helps to simulate filesystem and hardware errors and failures
7. CrashMonkey, a filesystem agnostic record-replay-and-test framework, helps test data and metadata consistency for persistent files.
8. Cascading failures cause issues in well working systems crashing them as well, example is thundering herd problem.
9. To avoid cascading failures we add exponential backoff with jitter so that load is reduced significantly
10. To define the network that is connecting two nodes we define various types of networks
	1. Fair Loss Link: Messages eventually delivered, no message creation, sent messages are not repeated infinite number of time. Example is UDP
	2. Stubborn Link: A link which continues resending the packet after some timeout until it receives and ack. This can be a dangerous link if the process is not idempotent. To prevent issues in non-idempotent systems we come up with deduplication.
	3. Message ordering is ensured by tackling the problem in the client to track what is the highest seq number which has consecutive entries present behind it and what is the seq number with the reordering has been done and it is processed, this gives us the option to deduplicate messages which have already arrived. Example TCP
	4. TCP has exactly once delivery per sesssion but if the session is broken, it might try to send that packet again.
	5. Any misunderstanding about whether or not exactly-once delivery is possible most likely comes from approaching the problem from different protocol and abstraction levels and the definition of “delivery.” It’s not possible to build a reliable link without ever transferring any message more than once, but we can create the illusion of exactly-once delivery from the sender’s perspective by processing the message once and ignoring duplicates.
   11. No completely asynchronous consensus algorithm can tolerate the unannounced crash of even a single remote process according to FLP impossibility.