### Timeline
- [ ] Research: 2 Days
- [ ] Script: 2 Days
- [ ] Animation and prompting: 4 Days





### Research
#### Go Runtime
![[Pasted image 20251001192953.png]]

G: Goroutine, M: Os Thread, P: Process


To get the current go routine we use `getg()` function which is a part of the runtime functions. This gives the current go routine running. This can sometimes return `g0` or `gsignal` depending on if you are on the system stack or the signal stack respectively. Thus to check if we are on the user stack or not, we run 
```go

getg() == getg().m.curg
```


#### Different kinds of stacks
1. User Stack: A small stack assigned to each go routine, starting from 2Kb but can be extended
2. System Stack: A stack assigned to each M to execute runtime code. This stack only stores the runtime code and does not have space for the queue for spare Gs. That is allocated separately in the heap.
3. Signal Stack: In linux there is a signal stack as well for signals.

Code running on the system stack is implicitly non-preemptible and the garbage collector does not scan system stacks. While running on the system stack, the current user stack is not used for execution


#### Stack splitting
The user stack can grow if more space is required. This is checked as a part of prologue or setup of running a go function, where we check the size of the function or the size of the stack frame for this function(local variables + call arguments). If the stack space is less we do a stack splitting and allocate more space and copy the stack items to the new stack. This can be prevented with the `//go:nosplit` in the go function preventing the split to happen. This functions are then checked statically if they overflow stack size or not.



#### Optimisations

When a goroutine finishes execution, it isn’t destroyed; instead, it becomes _dead_ and is placed into the free list of the current processor `P` . If `P`’s free list is full, the dead goroutine is moved to the global free list. When a new goroutine is created, the scheduler first attempts to reuse one from the free list before allocating a new one from scratch.




#### Ideas for videos
Make a visualization tool for visualization go stuff in live programs.
Do a comparison between C++ creating 100 threads to read a file and go doing the same thing.

This video is an excellent example of how indirection is a useful technique in computer which can solve very many problems,



### Preemption
 Go runtime, there is a daemon running on a dedicated thread `M` without a `P`, called `sysmon` (i.e. system monitor). When `sysmon` finds a goroutine that has been using `P` for more than 10ms ([`forcePreemptNS`](https://github.com/golang/go/blob/go1.24.0/src/runtime/proc.go#L6245-L6245) constant in Go runtime), it signals thread `M` by executing [`tgkill`](https://man7.org/linux/man-pages/man2/tkill.2.html) system call to forcefully preempt the running goroutine.


The way go routines block on channels is very different from waiting on mutexes, as the first one doesn't involve system calls but the second does adding to overhead.

There is always the possiblity of deadlocks in a scheduler with fixed number of threads. This is because all of the current running threads would be blocked on say a mutex and the mutex would have been held by something which is already in the sleeping state and can't be scheduled because of all threads being blocked. Thus while creating a scheduler whenever as a thread we do a system call we wake another thread up to pull items from the wait queue.


### Script

What you are looking at here in this complicated diagram that u and I are going to build up in this video.
Is a visualization of once of the most powerful tools used in the go runtime.
Its known as the go scheduler.
Go is a beautiful language. It is designed to be simple, efficient, and easy to use.
In this video we would see the user level threads that you might have heard about in go called go routines from popular tech companies and even startups actually useful or not or is just hype. In this video we would start from the bare bones and develop an intuition for how we can improve threads and try to reach a construct which might be similar to what might go have done. We would first discuss
1. Go Runtime. ## Go specific?
2. Basic thread model.


##### Go Runtime
The go runtime is a thin layer of intelligent logic fitted between the system call apis and the go code we write. It is primarily written in assembly and Go code itself.


##### Basic Thread model
In multicore processors of today threads are the atomic level of execution. A single process can spawn a bunch of threads but all of these threads get scheduled on actual hardware cores that the cpu has. So if the threads are more than the number of cores, some threads would have to wait for other to complete, but these reduces reactivity in the system, thus each thread is assigned a chunk of cpu time where it does the work and then is interrupted for other threads to run, this increases multiprogramming or reactivity in the system. Now some threads might also be waiting for IO and thus might be in waiting state and hogging the processor, thus threads are also moved to waiting and any active threads are picked up. This is known as Many to Many threading model, where any number of threads can be spawned and can be run on the a fixed number of processors.

##### Can we do better?
This simple thread model are present in almost all languages, allowing runtimes to create theoretically infinite number of threads. 
But why theoretically ? 
So let's visualize what a M:N Threading model looks like.
Let's assume we are on a 4 core system.
Firstly we have 4 kernel threads.
Then we have a bunch of user level threads creating in the language runtime.
For scheduling all of them we need a queue.
So adding and removing is done from the queue as physical threads free up.
When a kernel thread frees up it takes a lock on the global queue and gets a mini thread to execute.
Each thread you create is not free but incurs an extra space of storing the program counter some registers and a stack to store local variables.
Depending on operating systems this ranges from 1MB ~ 8MB.
Moreover even when the thread is sleeping or waiting on IO, these threads are still consuming this much amount of memory.
Assuming a memory limit of 32GB in RAM, we can create around 4000 threads, but practically this number is much less.
Some languages like C++ for example recommend for you to use a threadpool, instead of creating inumerable number of threads, which will severely bloat the size of the program. 


##### Problems in this model
We can clearly see in this model, the global queue with a lock is clearly a bottleneck.
Acquiring a lock as you can imagine is not free.
With the amount of context switches which are going to happen this is not a good option.
Moreover this threads frequently share go routines.  
A child go routine created would be placed on a different thread and thus locality of the caches of cores will not be utilized.
Another problem is that threads allocate local space to do heap allocations.
This space is not freed up when the M is blocking in a system call. 
Thus a lot of memory is being wasted where it is not even being used.
Let's solve these problems one by one.

##### Solving Problem 1
We have an easy solution for our first problem
Instead of a global run queue we can add a smaller bounded queue on each thread.
This ensures that threads created and used up on this thread are more often than not from the local run queue and prevent the frequency of locks.
This also solves our second problem.
Having a local run queue introduces locality.
Now more often than not threads created on this core are processed on the same core.
But this creates another issue.
What if the core is blocked ? 
It might be for IO or a system call.
All of the work items on the queue wait for the one thread to be done causing performance degradation.
We can easily solve this by introducing the concept of work stealing.
Its exactly as it sounds like.
In certain intervals when one core is done with its work it can pick up items pending on other local queues and execute them.
This solution now seems excellent.
We have solved both problem 1 and problem 2 in a single stroke.
Let's try to solve last issue

##### Solving Problem 3
To reduce the usage of memory in this solution, we would use a technique well known in computer science.
Indirection.
You see each thread is hogging the memory when it stalls and blocking the run queue, so what if we offload this work.
We can create a new container processor p.
Thus the run queue and local cache now attached to p instead of M.
Now even if a bunch of Ms are in system call memory consumption would be low.
Ps are limited and each core is assigned a single processor.

This solves our problem of memory consumption and also makes the work stealing effective with defined number of Ps.
So here is our final model.


###### Preemption
Now we are not aware what kind of code will run in the workers.
It might be cpu bound or it might be io bound.
But if there are tight loops like a long nested for loop, it is unlikely to give up the thread.
This is a problem because we need the thread to schedule other workers which are getting starved. 
Here we use another brilliant idea you might have heard of in operating systems, called time slicing.
We allow each worker to only occupy a slice of time say 10 ms and prempt it when it goes over.
A beautiful solution but we need an observer here to find out which workers are taking more time and need to be signalled. 
So let's allocate a single thread who just looks at workers and shoots them down when they go over the limit.


##### States of G

Let's follow a worker G from its creation to death.
First a G is created, it hasn't been initialized.
After initialization it becomes a runnable pushed to the run queue and is ready to be picked up.
The core picks G up starts executing it.
It could be a cpu heavy workload with no IO and is completely executed by the core and is moved to a dead state.
Or it could take a lot of time and exhaust its time slice and get preempted moved to runnable state again.
Or it could do some IO and get moved to the waiting state.
After syscall is done it could then be moved to the runnable state as the syscall is done.



##### States of M
Ms are the actual kernel threads running in the os.
Whenever a new M is created it is


##### State of P
Ps are tasked with maintaining the run queues, the memory and an ordered list of timed out workers. If some workers are sleeping for particular interval it maintains a ordered list of these timers.
Ps are created when the program is started using the programming runtime with the state idle.
A thread when acquires a processor to run Gs, P transitions to the running state.




##### Creating a stack on top of a queue.
Now the queue approach we have has a major drawback.
Its the problem of locality with related go routines.
Let's assume we have two threads dependent on each other, one is sending data other is receiving it over a pipe. 
Whenever we push data to the pipe we get preempted and something else gets scheduled.
The listener when trying to listen on a socket also gets preempted and pushed to the back of the queue. Thus this program due to lack of locality would require at least premption time * number of waiting workers to process one item.
What we need is some way of addressing workers related one another. Thus we push them to the head of the queue. Creating a stack of buffer size 1 on top of a queue.



##### Handling system calls.
System calls are kernel level operations like file reading, allocating memory, establishing connections etc.
Every worker sometime or the other calls these calls when reading a file or accepting a connection.
These might take undefined amount of time.
And thus we need to free up the processors to attach to another M, while this M continues to service the G in sys call.
Let's consider a single P, M and G. 
G issues a system call. Thus some dissociation has to happen
We set the state of G, M and P to be in syscall.
P is disconnected from M and G. 
P is monitored by sysmon, if it is in syscall state after a defined amount of time, another M and G is assigned to it for it to execute.
After the thread is done with syscall, we move this pair to the same Processor if available or assign it to some other free processor.
If no free processor is found we just push G to the global run queue and detach M and move it to the idle state.






#### Overview
1. About Go
2. State the importance of go scheduler in the go runtime.
3. List down topics which would be discussed.
4. Discuss about threads.
5. Discuss about problems with threads
6. Question about what could be a better implementation on top of threads.
7. Post the idea of having a queue of mini threads running on top of a particular os thread.
8. Discuss the deficiencies of such a design.
9. Try to solve the deficiencies.
10. Talk about fairness
11. Try to solve for fairness using preemption(Convoy Effect)


### Sources
- [Scheduler Structures](https://go.dev/src/runtime/HACKING)
- [Go Scheduler](https://nghiant3223.github.io/2025/04/15/go-scheduler.html)
- [Async IO on Linux: select, poll, and epoll](https://jvns.ca/blog/2017/06/03/async-io-on-linux--select--poll--and-epoll/#why-don-t-we-use-poll-and-select)
- [TCMalloc](https://google.github.io/tcmalloc/design.html)
- [Queues, Fairness and the Go Scheduler](https://www.youtube.com/watch?v=wQpC99Xu1U4&t=677s)
- [Dmitry Vyukov — Go scheduler: Implementing language with lightweight concurrency](https://www.youtube.com/watch?v=-K11rY57K7k&t=1149s)
- 