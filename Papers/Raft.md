
For evaluating systems we need to assume both that the nodes might be honest but messages may get lost and that nodes might be dishonest. Thus for sysmtem models we need to assume
1. Network Behaviour (message loss)
2. Node behaviour (Crashes)
3. Timing Behaviour (latency)


##### Network Behaviour
1. Reliable: Messages are received if they are sent and maybe reordered
2. Fair Loss: Messages may be lost, duplicated or reordered, retrying eventually gets through
3. Arbitrary: Malicious

Network Partition is when some links are dropping delaying all messages over some time


##### Node Behaviour
1. Crash Stop (fail-stop)
2. Crash Recovery (fail-recovery): It goes down but also comes up
3. Byzantine: Crash or malicious 

##### Timing Behaviour
1. Synchrnous: Some defined message latency
2. Partially Synchronous: Async for some time sync for other times
3. Asynchronus: Messages can be delayed arbitrarily


We achieve high availibility using fault tolerance


##### Clock Synchronization
Clock Skew is the difference in two clocks at a point of time
NTP server is a more accurate time server which we can get accurate time from to sync our clocks and reduce clock skew
Because of clock skew code like

```go
startTime := time.now()
//do something
endTime := time.now()
```

Then endTime - startTime can become negative because of clock skew correction if the clock correction happens in between doing something. Thus we need a monotonic clock instead of a NTP synced clock (time of the day clock).

Due to clock skew if we use time of the day clock for ordering messages, it could lead to an incorrect ordering over multiple machines having different clock skew. Thus we need a logical clock in this case

Logical timestamp is just counter that increments when something happens


##### Broadcast Protocols

Brodcast algorithms talk about sending messages to multiple nodes, here sending those messages is called brodcast and receiving is called delivery. Each of the node might not immediately deliver because it might need to ensure that messages are present in a certain order.

##### Fifo Broadcast
If m1 and m2 are brodcasted by the same node they must be delivered in the same order as well. This order basically ensures the case where one message by a node is not delivered to a certain node and the next messages get delivered before.


##### Causal Broadcast
If a broadcast of one message happened before another as in the causal relation then all nodes deliver in the causal order itself 

##### Total Broadcast
In a system the order of delivery of events to each of the nodes match a global order. This is why nodes cannot send as soon as they recieve they might have to hold up some entries before sending to create an accurate global order


##### Gossip Protocols
Every node relays messages to x other nodes ensuring a high probability that a single message reaches all nodes. They are useful in acheiving reliable broadcast so that if any of the nodes sending the message crashes we have a high probability that the nodes which received that message do send that message to all other nodes



