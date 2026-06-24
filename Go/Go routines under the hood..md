
Go routines are light weight threads.
These are started and managed by the go runtime. They have just 2kb memory(that's where low overhead comes from) to start with but have growable stacks. They have cheap context switching using go scheduler. 
#### This is a go routine struct
![[Pasted image 20250913221352.png]]
- ActiveStackChans: Keeps track of whether there are external channels pointing in to our go routine
- ParkingOnChan: This indicates that the go routine is sending some data or receiving some data over a channel thus avoid stack shrinking
- Each go function can cause morestack to allocate more stack memory if needed at runtime. 2x size of current stack and copies the stack.

#### Go Scheduler
This is responsible for runnign go routines. We have 3 entities in a scheduler
1. G: Goroutine, the code to execute
2. M: Os Thread, where to execute it
	1. P: Logical Processor,  permissions + resources to execute it.
###### M:N Scheduler
If we have M goroutines to be scheduled over N schedulers, thus multiplexing go routines onto threads. Thus go routines are attached to local run queues of processors
![[Pasted image 20250913223041.png]]
Thus processors know what goroutines are available for a thread to run.
In case of a blocking system call, the scheduler still keeps the G attached to the M and avoids consuming P resources of other go routines. Go scheduler only does a context switching for expensive system calls. This hanging on to the OS thread to avoid context switching is what makes the go routines so fast and effecient


##### Blocking async Network call
![[Pasted image 20250913225538.png]]

##### Blocking on channels
Within channel struct we have receive queues, which are list of go routines that are blocking and want to send data to this channel 

#### Work stealing between threads