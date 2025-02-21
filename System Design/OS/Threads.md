OS threads are lighter weight compared to OS processes.
Only shares the code and data(initialized and uninitialized) with the main thread. The threads share the same address space as the main process.

##### Concurrency vs Parallelism
Interleaving of tasks is concurrency, true running in different cpus is parallelism


#### Amdahll's law
The percentage speedup when switching from a serial to parallel execution
speedup <= 1 / (s + (1 - s) / N)

where s is the percentage serial


#### User vs Kernel Threads
User threads: Managed by user level threads library.
Kernel threads: Supported by kernel

#### Implicit Threading
1. Thread pools
2. Fork Joins
3. OpenMP
4. Grand Central Dispatch
5. Intel Threading Building blocks

##### Thread Pools
Have a set of threads created without work assigned

##### Fork Join
Create multiple threads from a thread and wait for them to join before moving ahead.

### Signals
Events triggered by the CPU and the software running on it. It notifies a process of some event occuring. Interrupts are per cpu while signals are per process. Every signal has a default signal handler in the kernel. User defined signal handler can override the signal.

#### Issues
1. What do to in case of a thread calls fork.
2. Where should a signal to a process goes.


#### Thread Cancellation
Asynchronous: Terminates immediately
Deferred: Checks if it should be cancelled at regular intervals.


#### Thread Local Storage
This is not stack storage, this is storage dedicated to the thread. This is useful for threads in a thread pool. TLS is unique to each thread. It is stored in thread stack.



## Scheduling

Dispatcher executes context scheduling. 
1. It switches context
2. Switches to user context
3. Jumps to the proper location in user program to restart the program.

Convoy effect: Short duration processes getting stuck behind long duration processes

Round robin in scheduling of processes, after time slice is over it switches the process.


### Priority Scheduling.
Priority scheduling is required so that low priority processes are not given equal priority as high priority ones. 
This causes starvation but can be solved using aging.


### Multilevel Queue
We have multiple queues storing processes in the ready state. Each queue has different scheduling algorithms. We first decide which queue the process will join. We also have scheduling among the queues. We could have one for real-time processes, second for system processes, then interactive processes and then batch processes. Each process in a different queue would have a larger time quantum.

#### Multilevel feedback queue
We can promote or demote a process between the queues and aging can be implemented to achieve the promotion or demotion.