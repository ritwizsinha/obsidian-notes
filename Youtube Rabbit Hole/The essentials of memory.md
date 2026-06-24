


#### Title

How a single threaded program beats multithreading.

##### Content
1. Something about threads.
2. Something about thread states.
3. Context Switching these threads.
4. Questions regarding scheduling.

##### Script Outline


1. A hook of understanding why threads and caches are important to understand
2. Giving a brief and quick overview for threads.
3. The traditional overview of user level and kernel level threads.
4. How that is not a case anymore and how [this](https://stackoverflow.com/questions/39185134/how-are-user-level-threads-scheduled-created-and-how-are-kernel-level-threads-c) changes how we understand the threading infrastructure.
5. A brief explanation of thread states (Runnable, Executing and Waiting)
6. Types of work that can be scheduled on a thread (IO vs CPU bound)
7. Context switching and its decisions required.
8. How frequent context switching can affect cache latencies for data.
9. An animation to show the difference between latencies from L1, L2,L3 and main memory
10. Understanding cache lines and the problem of frequent cache invalidation operations due to mutating shared data.
11. Important decisions about context switching
	1. You start your application and the main Thread is created and is executing on core 1. As the Thread starts executing its instructions, cache lines are being retrieved because data is required. The Thread now decides to create a new Thread for some concurrent processing. Here is the question.

		Once the Thread is created and ready to go, should the scheduler:
		
		1. Context-switch the main Thread off of core 1? Doing this could help performance, as the chances that this new Thread needs the same data that is already cached is pretty good. But the main Thread does not get its full time slice.
		2. Have the Thread wait for core 1 to become available pending the completion of the main Thread’s time slice? The Thread is not running but latency on fetching data will be eliminated once it starts.
		3. Have the Thread wait for the next available core? This would mean cache lines for the selected core would be flushed, retrieved, and duplicated, causing latency. However, the Thread would start more quickly and the main Thread could finish its time slice.

Threads are simple objects of computation.
You throw a computation at it and a thread gives you a predictable result. (well mostly).
A single program you run can be broken down into various threads.
All of them running concurrently to create the feel of realtime responses.
This is the basic unit of computations and describes almost all of the applications that run on a computer.
In this video we will take a deeper look at the threads themselves.
We would first discuss on the types and states of threads.
The kind of work that they do and the decisions involved in making our runtime this fast and efficient.
Chances are if you have been programming for some time you have used some sort of threading.
Understanding in depth of threads helps you not only write better code but debug better and quicker the nasty bugs that these small processors create.





Here is an engaging, 10-minute video script based on your detailed outline and the provided articles.

***

### **Video Script: The Hidden Price of Threading**

**(Visual: 
 A shot of code with title of what it is doing.
)**

**V.O.:** Have you ever written perfect, logical, beautiful code. But it’s *slow*. And you don’t know why. 
Take a look at this simple parse of multithreaded array to count the number of odd numbers in it.


**(Visual: Showing an arrow to make it multithreaded, showing its performance on 4, 10 and 14 cores)**
**V.O.:** What we realize is running this in a single thread is actually way more effecient than running it on 1-2-3 ... 16 cores.

**(Visual: Showing the graph for its speed on more number of cores)**
**V.O.:** Infact its still only 10% better when running with 16 cores.
This is confusing. 
Aren't we taught that adding the number of threads improves processing.
So why is it not working here.
Well you see not always.

**(Visual: Showing a process depending on a CPU cache  and threads)**
The reason isn't in your code's *logic*. It's in the hidden machinery running underneath. Your program's performance mainly depends upon how the threads and caches are interacing.


**(Visual: Showing the program again)**
In this video we will run why this program runs so slow.
How can we improve it and how to run multithreaded code in a better manner.

---

### ## 1. The World of Threads

**(Visual: A simple animation of a CPU  Processor having multiple small boxes and inside them a small loading kind of icon rotating showing a thread)**

**V.O.:** So, what is a thread? The simplest answer is: it’s a **path of execution**.


**(Visual:  A process breaking down into several threads and getting pushed to the CPU.)**

When you run your application, you create a Process. But the Process doesn't *do* anything. It's just a container. The work is done by **Threads**. A thread is the smallest sequence of instructions that the operating system's scheduler can manage.

You might have heard of the term kernel level vs user level threads. This is although a valid model to understand how threads work it is not how modern linux kernel sees threads.

**(Visual: A split-screen diagram. Left: "User-Level" (Many-to-One). Shows many small 'U' threads inside one big 'K' thread. Right: "Kernel-Level" (One-to-One). Shows one 'U' thread mapping directly to one 'K' thread.)**

**V.O.:** The textbook explanation goes like this:
* **User-Level Threads** are fast and cheap. They are managed by a library in your application, not the OS. They're invisible to the kernel. But they had a fatal flaw: if *one* user thread blocked to read a file, the *entire process* and *all other user threads* would grind to a halt.
* **Kernel-Level Threads** are the "real" threads. The OS manages them directly. They are heavy, slow to create, but if one blocks, the OS can just schedule another.

But here’s the plot twist, and this is critical: **That model is mostly obsolete.**

As that Stack Overflow link explains, modern operating systems like Linux won the "thread wars" with something called NPTL—the Native POSIX Thread Library.

When you call `pthread_create` in C today, you are creating a **kernel-level thread**. The kernel *is* aware of it. We now live in a 1:1 world: one application thread equals one OS-managed thread.

So where did "user threads" go? They've been reborn as things like **Goroutines** or **Fibers**. These are lightweight tasks managed by a *runtime* (like the Go runtime), which then schedules them onto that pool of "real" 1:1 kernel threads. It's a hybrid model, but the foundation is the kernel thread.  

---

### ## 2. The Life of a Thread

**(Visual: A simple state-machine diagram with three circles.)**

**V.O.:** To understand scheduling, you need to know a thread's three possible states.
1.  **Executing:** The thread is *on a CPU core* and is actively running instructions. This is the dream.
2.  **Runnable:** The thread is *ready* to run, but it's waiting in line for a CPU core to become free.
3.  **Waiting:** The thread is stopped. It cannot run, even if a CPU is free, because it's waiting for something... like data from the network, a file from the disk, or a lock from another thread.

The scheduler's entire job is to move threads between these three states as efficiently as possible. And the *kind* of work your thread does determines everything.

**(Visual: Split screen. Left: A CPU icon glowing hot. Right: A network/disk icon.)**

**V.O.:** We have two types of work:
* **CPU-Bound:** This is a number cruncher. It's always **Executing** or **Runnable**. It *never* wants to stop. Think calculating Pi or rendering a video.
* **I/O-Bound:** This is a "hurry up and wait" task. It spends almost all its time in the **Waiting** state. Think querying a database or calling a web API.

---

### ## 3. The Most Expensive Tax in Computing

**(Visual: Animation. A thread "A" is running on a CPU core. A scheduler "hand" pushes it off, saving its state (registers) to a box. It then grabs thread "B" from a queue, loads *its* state from a box, and places it on the core.)**

**V.O.:** This act of swapping one **Executing** thread for a **Runnable** one is called a **Context Switch**. And it is a performance *disaster*.

A context switch isn't free. It takes time. The Ardan Labs article estimates it can take between **1,000 and 1,500 nanoseconds**.

That sounds tiny, right? But a modern CPU can execute over *12 instructions per nanosecond*. That means every single time you swap threads, you have paid a tax of **12,000 to 18,000 instructions** of work that *didn't* get done.

For an I/O-bound thread, this tax is worth it. You swap out a **Waiting** thread for a **Runnable** one and keep the core busy. But for a CPU-bound thread? A context switch is just lost time.

But the *time* isn't even the worst part. The *real* cost is what it does to your **cache**.

---

### ## 4. The Data Problem: Why Your CPU Hates RAM

**(Visual: An animation showing the relative speed of caches. A fast, tiny L1, a medium L2, a larger L3, and a huge, slow Main Memory.)**

**V.O.:** Your CPU is a Formula 1 car. Your Main Memory (RAM) is a bicycle courier. Your CPU is *constantly* starving, waiting for data from RAM.

To solve this, the CPU has its own tiny, super-fast pools of memory called **caches**.
* Accessing the **L1 Cache** is like grabbing a note off your desk. Maybe **3-4 clock cycles**.
* Accessing **L2** is like a bookshelf in your office. Maybe **10-15 cycles**.
* **L3** is like a filing cabinet down the hall. **40-70 cycles**.
* Accessing **Main Memory**? That's like driving to the public library. **100-300 cycles** of waiting.

When a context switch happens, you swap in a new thread. All the data *that* thread needs? It’s not on the desk. It's not in the office. It's at the library. The new thread starts its life by *stalling*—waiting for data to be slowly fetched from RAM. This is called a **cache miss**.

**(Visual: An animation of a 64-byte block of memory moving from RAM to the Cache. Inside the block, a tiny 4-byte integer is highlighted.)**

**V.O.:** And it gets worse. When your CPU needs data, it doesn't fetch one byte. It fetches a **64-byte** block called a **Cache Line**.

This leads to a nightmare problem. Imagine two cores, Core 1 and Core 2.
1.  Core 1 needs a variable `X`. It fetches the *entire 64-byte line* containing `X` into its cache.
2.  Core 2 needs a variable `Y` that *just happens* to live on that *same* cache line. It fetches a *copy* of that same line.

Now... Core 1 *changes* its variable `X`.

**(Visual: Core 1's cache line flashes "MODIFIED". Core 2's cache line is instantly marked "INVALID" with a big red X.)**

**V.O.:** The hardware *must* ensure all cores see the same data. This is **Cache Coherency**. So, it *invalidates* the entire 64-byte line in Core 2's cache.

The next time Core 2 tries to read its *totally unrelated* variable `Y`, it gets a cache miss. It has to stall and drive *all the way back to the library* for a fresh copy.

This is called **False Sharing**, and it can silently kill your multi-threaded performance. Your threads are thrashing the cache, constantly invalidating each other's data, all because their variables were *too close* in memory.

---

### ## 5. The Scheduler's Impossible Choice

**(Visual: An animation of a CPU Core 1, with a "Main Thread" running on it. An "Idle Core 2" is nearby. A "New Thread" pops into existence.)**

**V.O.:** So, let's put it all together. You are the OS scheduler. You face an impossible choice.

Your application's **Main Thread** is running on **Core 1**. Its caches are "hot"—full of useful data. Suddenly, it creates a **New Thread** to do some work. This New Thread is now **Runnable**.

What do you do?

**V.O.: Option 1: Context-switch on Core 1.**
* You *immediately* stop the Main Thread (paying the 15,000-instruction tax) and put the New Thread on Core 1.
* **Pro:** The New Thread likely needs the *same data* as the Main Thread. The cache is already hot! This is a *huge* win for data latency.
* **Con:** We just paid the expensive context switch tax, and the Main Thread didn't finish its work.

**V.O.: Option 2: Wait for Core 1 to be free.**
* You tell the New Thread, "Wait in the **Runnable** queue until the Main Thread finishes its time slice."
* **Pro:** No extra context switch! And the cache will *still* be hot when the New Thread starts.
* **Con:** The New Thread is doing *nothing*. We are wasting an opportunity for concurrency.

**V.O.: Option 3: Use another core.**
* You see that **Core 2** is idle. You *immediately* schedule the New Thread onto Core 2.
* **Pro:** This is true parallelism! Both threads are **Executing** at the same time. Maximum throughput!
* **Con:** Core 2's caches are *cold*. It has *none* of the Main Thread's data. The New Thread will stall for hundreds of cycles as it drives to the "library" (RAM) to fetch all its data, which is already sitting in Core 1's cache.

**(Visual: All three options are shown on screen, each with a '?'.)**

**V.O.:** There is no single right answer. This is the fundamental tradeoff. Schedulers use complex heuristics to guess, but they're just guessing.

They are in a constant battle between **scheduling latency** (the cost of the switch) and **data latency** (the cost of a cache miss).

This is Mechanical Sympathy. Understanding that every line of code you write is part of this battle... *that* is what separates good code from fast code.

**(Video Ends)**
##### References
1. [User Level vs Kernel Level](https://stackoverflow.com/questions/39185134/how-are-user-level-threads-scheduled-created-and-how-are-kernel-level-threads-c)

