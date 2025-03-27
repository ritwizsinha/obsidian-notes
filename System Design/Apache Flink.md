#### Building blocks of Flink


#### Stateful Event and Stream processing
In a flink application we can define the flow and parsing and transformation/aggregation of data.


Source -> Transform -> Window(state read / write) -> Sink

The Sink can be any data store.
Flink takes care of turning this into a parallel data flow.
The state is stored is locally at each node, So for example in the window stage, we get a 5 second rolling window of aggregations, Flink creates a state for that and stores that on a local node in Rocksdb. So the amount of state we can handle in Flink is limited by the disk or memory limits of the machines.



#### Checkpoint barriers
Whenever a checkpoint barrier runs through the system, each operator creates a local snapshot of the state for the time when the barrier arrives at the operator. This snapshot is then written to storage like S3 for recovery. This enables exactly once guarantees in Flink, because the records cannot jump over the barrier



### Time handling
There are various kinds of time for an event
1. Time at which event was emitted
2. Time at which event was ingested into broker
3. Time at which it was ingested by Flink
4. Time at which it was processed.