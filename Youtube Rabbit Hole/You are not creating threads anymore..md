
**SHORT**


##### Script
Threads are the basic unit of execution. A single process can be considered a container running your program which breaks down into multiple threads and each thread be executed on a single processor. There is the concept of user vs kernel thread that every programmer might have heard about from os level lectures or other youtube videos. It talks about how the program could create many threads which could map to a single kernel thread. Thus kernel as little idea about each individual thread the program creates.So you might expect that when you create a thread in C using pthread_create it is a user level thread. You might be surprised to hear that this is not how the actual thread model works. User level threads actually don't exist now. The thing is threads were not a native concept to operating systems a decade ago but nowadays every os has a native thread implementation. Thus when we create a thread it is actually a thread managed by the kernel.  The old concept of user level thread originates from the usage of LWP in the earlier linux system in which threads as a concept didn't exist. So LWP implemented it and managed it in the user space without the kernel knowing anything about it. Nowadays LWP has a new name it is called the "green threads". Green threads are light abstractions over normal threads implemented in language runtimes like goroutines in go, coroutines in cpp and fibers in ruby.  These threads make concurrency much simpler to the programmer but is not evident from the os.



#### Refined

[Show a thread]
So, what exactly _is_ a thread?

[Show a process breaking down into multiple threads]
Think of a process as a container running your program.  
Inside that process, threads are the **basic units of execution** —  
each thread can run a different part of your code _at the same time_.

[Show a cpu as a rectangle having multiple smaller cores rectangles and circle rotating in them.]
If your CPU has multiple cores,  
multiple threads can literally run **in parallel** —  
one per core.

[Create two types of threads having different colors]
[Map user level threads to multiple kernel level threads having a combination of those colors.]
Now you might’ve heard about _user threads_ and _kernel threads_ —  
a concept every OS course loves to bring up.  
The idea was simple:  
your program could create many user threads,  
but only a few of them were actually visible to the kernel.  
So, scheduling happened in user space.

[Black this concept out.]
But here’s the twist — that model is mostly **history**.

[Show document for pthread_create]
When you call something like `pthread_create()` in C,  
you’re not creating a user thread at all.  
[From running the function pthread_creata, create a kernel threa]
That function eventually calls a system call like **`clone()`**,  
which directly asks the **kernel** to create a **new kernel thread**.

[Make a single process to multiple small processeß]
Each one has its own stack, registers,  
and gets scheduled just like a tiny process.  
So the kernel _knows_ every thread you create.

[Show LWP as creation by sun os]
That old “user-level thread” model came from the early Linux days —  
when threads didn’t exist yet,  
and something called **Light Weight Processes (LWPs)**  
faked threading entirely in user space.

[Green threads heading with green threads mapping to Goroutines, Coroutines and fibers]
Today, that concept survives as **green threads** —  
like **goroutines in Go**, **coroutines in C++**, or **fibers in Ruby** —  
which are managed by your language runtime, not the OS.

[Show a kernel knowing about each process.]
So yeah — when you create a thread today,  
it’s really a **kernel thread** under the hood.  
User threads? They’re just a clever illusion from the past. ⚙️💡