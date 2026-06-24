

##### Script Talking Points

1. Talk about how if you could have global variables, and statics that might be leading to latencies.
2. Linear array traversal is better for performance for small datasets and sometimes even beats out better asymptotic complexity algorithms.
3. The example where we just access one bit to check if an object can be accessed or not. This leads to serious wastage of cache line.
4. Better use of instruction cache when we separate different objects to different arrays and process one by one so that we don't get different objects one after another leading to poor cache locality or sort the items.
5. Explain cache acccess patterns



##### Script Outline
1. [x] A simple overview of a 2 d array traversal with code and animation
2. [x] Lets make this multithreaded, show the code and the animation.
3. [x] Show the performance difference between both by increasing the number of threads using the graph.
4. [x] Explain what we are going to look at in this video
	1. [x] Threads
	2. [x] Memory
	3. [x] Cache
		1. [x] Different types of caches
	4. [x] Solution to this problem.
5. [x] Explain that access to memory is slow with animation.
6. [x] Explain cache.
7. [x] Explain multi levels of cache along with diagrams.
8. [x] Explain memory
9. [ ] Structure of L1, L2 and L3 caches.
10. [x] Explain the concept of variable sharing among multiple threads.
11. [x] How we prevent that using mutexes
12. [x] How we cannot prevent false sharing.
13. [x] Explain false sharing.
14. [x] Explain the solution to the discussed problem.
15. [x] Something about global arrays and static variables facing the same problem.
16. [x] Showing why object oriented design could be bad for cache coherency issues.


##### Rough Script
Your multithreaded code is most probably slower than normal single threaded code.
Don't believe me, take a look at this.
**V**: A dot diverging into multiple threads, multiple threads taking longer than a single thread converging to a single dot.

Take a look at this code, it simply traverses a 2d array in row major order, meaning horizontally and counts the number of odd numbers, the code is fairly simple. If we want to make it faster,
**V**: Left side showing the code under the Dracula theme and right showing the visualization of the array traversal. 
We might think of a multithreaded solution to this problem, dividing the matrix into submatrices and providing each thread a submatrix to iterate on. At the end accumulate all the results and there is our answer.But does this really increase the throughput ?
V: Left side highlighting the code being talked about and right side showing multithreaded traversal of the grid.
If we run it we can see, that instead of improving the runtime the program is actually running at least 50% slower.  This is weird and surprising. Even on increasing the threads the performance still falls of a cliff.
V: Showing a graph of the runtime for multithreaded vs the single threaded approach.

In this video we will dissect why your multithreading implementation might make your code slower and what happens under the hood. We will understand about caches, how threads interact with caches. what things to keep in mind while writing multithreaded code. We will also find a solution to this problem, at the end of this video.
V: Show a preview of all the other animations in this course.


As CPUs became faster and faster, reading from main memory quickly become a bottleneck.
V: Show CPUs getting faster in speeds and memory speed remaining the same.
Caches are memory stores smaller than the main memory providing lightning fast access to data and instructions due to expensive hardware.  To support optimal performance the cpu has multiple caches attached to it, each getting faster and expensive as we go towards the CPU.
V: Show  animations from main memory to a cache and then create multiple level of caches. Each cache data transfer rate can be animated according to the cycles they take for transfer of data. 

We call these cache layers as L1, L2 and L3 caches, with each CPU having a dedicated L1 and L2 cache and sharing the L3 cache with other processors.
V: Label these caches as L1, L2 and L3 and introduce multiple 3 CPUs and assign an L1  and L2 cache to each.

These cache have different latencies for fetching data with the main memory being the slowest.
V: Showing request for data from a program to L1, L2, L3 and main memory.

To understand caches we need to have an intuition of memory and its organization. 
We access memory in chunks called word. 
V: Show a chunk of memory and name a divide it into equal sized rectangles, name a single rectangle as a word, and then create a zoom out animation to show a size of 2^24 items. Then zoom in to a bunch of words and add the address of a chunk on the left size in binary. 



Programs generally follow the concept of spatial locality, ie whenever an address is accessed the next address to be accessed is more likely to be exactly next in memory as well.
V: Map code spaces to memory.
We leverage that in caches by getting data in the granularity of multiple words called a line instead of a single word.
V: Now move memory element to the left and pick multiple of these contiguous memory segments to put in a cache called a line.
 In caches we generally group together a bunch of words and create a cache line, this line contains multiple words and is read and written together. This is because of the principle of locality where if we are reading an address it is generally advantageous for the CPU to get the next or previous address as well depending on the order. 










Cpus bring data into cache L1, L2 because of each core having a separate L1 and L2 cache each one gets its own copy. When the program writes to the data now the core on which the action is performed updates its cache instead of writing directly to the memory and the value gets invalidated on other caches if brought in. This leads to the problem of shared variables, where if 2 cores change the same value, nobody knows which will apply first causing a race condition. This is the reason mutexes or atomic variables need to be used to define an order to these operations. But considering cache lines are invalidated instead of actual values even if two cores change values residing in the same cache line, it might lead to performance degradation, because once a core writes to a cache line and invalidates it on other cores read on other other cores is impacted and degraded in performance. This leads to the problem of false sharing where even without sharing actual variables, program's performance is impacted because of the location of the object in the memory.

Now it might make sense why the program performed so poorly even increasing the number of cores. To solve this we can just use thread local variable in the multithreaded code and then at the end write to the array to prevent continuous false sharing problems. 

This is the reason why global variables or arrays might be detrimental to your performance if changed in tight loops.
There are other performance implications to this cache workflow as well. If we use something like Object Oriented Programming, and structure objects, we might store the data regarding the activitiy of variables inside the object itself, this leads to the issue where when iterating through the objects to perform some operation we might check some boolean to identify if the object is live or not. This leads to bringing it in the cache line for just checking the variables just to discard it when it is not live.
Another issue would be iterating through a bunch of abstracted objects, where different virtual functions need to be brought into the cache, leading to suboptimal performance.



#### Modified Script



### 🕒 **0:00 – 0:20 | Hook** (1)

**Narration (authoritative tone):**  
Threads are weird.
And you might be surprised to hear this.
Your multithreaded code is _probably slower_ than your single-threaded one.
Let's take a simple example to illustrate this.

**V:**  
A single glowing dot splits into several smaller ones—each labeled as a thread. They rush to complete their tasks, yet the central timer shows they take longer before merging back into one dot.

---

### 🕒 **0:20 – 1:00 | Example setup (2)**

**Narration:**  
Here’s a simple program.  
It traverses a two-dimensional array —horizontally—and counts how many numbers are odd. Straightforward, right?

**V:**  
Left side: code shown in the _Dracula theme._  
Right side: a 2D grid lighting up row by row as each element is accessed.

**Narration:**  
Now let’s parallelize it.  
We divide the matrix into sub-matrices, give each thread its own section, and combine the results at the end. 
This is the code for that.
Surely, that should make it faster.

**V:**  
Highlight code that spawns threads.  
On the right, animate multiple colored grids being scanned simultaneously—each representing a thread’s section.

---

### 🕒 **1:00 – 1:25 | The surprise (3)**

**Narration:**  
But when we run it, something strange happens.  
Increasing the number of threads should decrease runtime by a a factor of number of threads, as work gets divided.
But that doesn't seem to happen.
Instead of improving performance, it runs at marginal improvements or even no improvement at all.  
We see much smaller performance improvements from what was expected. 

**V:**  
Show a runtime comparison graph:  
Single-threaded bar short; multi-threaded bars growing longer with more threads.  
Add a subtle “falling off a cliff” animation for dramatic effect.

---

### 🕒 **1:25 – 1:45 | Purpose of the video (4)**

**Narration:**  
In this video, we’ll dissect **why** that happens.  
We’ll look inside your CPU, explore how **caches** actually work, how **threads** interact with them, and what are common causes for un expected performance nightmares in otherwise good looking multi threaded code.
And finally, we’ll see how to fix these issues.

**V:**  
Quick montage preview: CPU cross-section, cache hierarchy, multi-core diagram, and the optimized version running faster.

---

### 🕒 **1:45 – 2:30 | The real bottleneck (5)**

**Narration:**  

Initially CPUs were on par with memory with their processing speed.
As processors became faster,  **main memory**, failed to keep up.  
Fetching data from RAM became the bottleneck.

**V:**  
Show CPU clocks speeding up dramatically while memory speed remains nearly flat.  
Add a “Bottleneck” barrier between CPU and memory with a slow loading indicator.

**Narration:**  
To bridge that gap, CPUs introduced **caches** — small but ultra-fast memory units built with expensive hardware.  



**V:**  


---

### 🕒 **2:30 – 3:00 | Cache hierarchy (6)**

**Narration:** 
We have multiple level of these caches and each level of cache gets faster and smaller as we move closer to the CPU core. 
As you can clearly see the difference of fetching from main memory and the fastest cache is huge.
We call these levels **L1, L2, and L3 caches.**  
Each CPU core has its own private L1 and L2 cache, while all cores share the L3 cache.

**V:**  
Animate a layered pyramid: Main Memory → L3 → L2 → L1 → CPU.  
Show data flowing upward faster and faster
Display three CPU cores. Each has a small L1 and L2 block attached, all connecting to one large shared L3.  
Label each clearly.

**Narration:**  
And these caches differ in latency.  
A request satisfied by L1 is near-instant.  
L2 takes longer.  
L3, even more.  
And main memory? Dozens of times slower.

**V:**  
Show a latency meter — L1: tiny bar, L2: longer, L3: longer still, Memory: huge bar stretching across the screen.

---

### 🕒 **3:00 – 3:45 | Understanding memory (7)**
 
**Narration:**  
To understand caching, we need to picture how memory is organized.  
Memory is accessed in fixed-size chunks called **words**.
Each word has an address and it is the atomic unit for reading and writing from memory.
**V:**  
Zoom into a long strip of memory divided into small rectangles labeled “word.”  
Display binary addresses on the left.

**Narration:**  
Programs exhibit **spatial locality** — when one address is accessed, nearby addresses are likely next.
So, instead of fetching a single word, CPUs grab a whole **cache line**, a group of consecutive words.

Cache lines then become the atomic unit of reading and writing to the cache.

**V:**  
Animate the CPU selecting several adjacent memory blocks at once, grouping them into a “cache line,” then storing it inside L1 cache.  
Highlight how multiple words travel together.


Sentences:
To understand caching, we need to picture how memory is organized.  
Main memory is accessed in fixed-size chunks called **words**.

Now most programs exhibit **spatial locality**
When one address is accessed, its more likely that nearby addresses would be accessed next.
This is true for both code and data.
Considering the usage pattern CPUs grab a whole group of consecutive words.
This is called a cache line and is stored as an entry in the cache.

---

### 🕒 **3:45 – 4:45 | Multiple cores, shared data (8)**

**Narration:**  
Each CPU core maintains its own L1 and L2 caches.  
Let's imagine we have a global array, shared between multiple threads. This array is divided across memory but comes in the same cache line.
When a core modifies data, it updates its local cache first — not the main memory.  
Other cores that had cached the same data must then **invalidate** their copies.

**V:**  
Show a large array.
Divide it into chunks and add it to memory.
Now the cache picks up the whole piece as a cache line.
Show two cores each with their own caches.  
One writes to a variable, its cache line glows red, while the other’s copy fades out to show invalidation.

**Narration:**  
Now imagine two cores updating the same variable at the same time.  
Neither knows which value is correct — this is the classic **race condition**.  
That’s why we use **mutexes** and **atomics** to enforce order.

**Narration:**  
But cache invalidation works at the _line_ level, not at the _variable_ level.  
So even if two threads modify different variables located on the same cache line, the line keeps getting invalidated — over and over again.  
This is **false sharing**, and it destroys performance.

**V:**  
Zoom into a cache line containing two distinct variables.  
Two cores alternately write to them; the line keeps bouncing between caches in a ping-pong effect, visibly stalling both threads.

---

### 🕒 **4:45 – 5:15 | Back to our example (9)**

**Narration:**  
Now, think back to our multithreaded matrix traversal.  
Each thread was writing to parts of the same array — often sharing the same cache lines.  
Every write forced invalidations and re-fetches, throttling performance.

**V:**  
Show the earlier multithreaded grid, now with visible cache lines flashing red each time a thread writes to them — representing false sharing chaos.

**Narration:**  
The fix is simple:  
Use **thread-local variables** within each thread.  
Then, after all threads finish, merge the results once.  
This avoids constant cache line invalidation.

**V:**  
Show separate threads working independently with isolated local data, then writing their results to the main array once at the end.  
Overlay text: _“Thread-local = no false sharing.”_

---

### 🕒 **5:15 – 6:00 | Broader performance pitfalls (10)**

**Narration:**  
But caches affect much more than just multithreading.  
In object-oriented code, small flags or booleans stored within objects can cause entire objects to be loaded into the cache — even if you only needed one field.

**V:**  
Animate an array of objects. Accessing one boolean causes a large block to load into cache, wasting bandwidth.  
Show many “unnecessary” fields dimly loaded in gray.

**Narration:**  
And in abstract code using **virtual functions**, each function pointer may point to code scattered across memory.  
When iterating, the CPU keeps chasing those instructions — missing the cache repeatedly.

**V:**  
Show multiple objects pointing to widely separated memory blocks.  
As each function executes, display “cache miss” flashes and delayed responses.

---

### 🕒 **6:00 – 6:30 | Conclusion (11)**

**Narration:**  
When threads compete for cache lines instead of cooperating, more cores can actually make your program slower.

**V:**  
Final animation:  
A single-threaded bar outperforming multi-threaded until optimization is applied.  
Then, after adding thread-local variables, the optimized multi-threaded bar finally overtakes it.  
Fade out with the title:

**Text on screen:**  
🧠 _“Optimize for cache, not just for cores.”_


---

## 🎞️ **Storyboard: Why Your Multithreaded Code Is Slower**

| **Time**        | **Scene Title**                                     | **Visual Elements / Animation Direction**                                                                                                                                                                                                                                            | **Narration (Condensed for timing)**                                                                                                                                              |
| --------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **0:00 – 0:20** | **Hook – The Surprise Claim**                       | • Black background fades in. • A glowing white dot appears center-screen. • Dot splits into multiple smaller colored dots labeled _Thread 1, Thread 2,_ etc. • Each moves outward, works briefly, and reconverges. • Timer shows threads take longer.                                | “Your multithreaded code is probably slower than your single-threaded one. Don’t believe it? Let’s prove it.”                                                                     |
| **0:20 – 1:00** | **The Simple Program**                              | • Split screen: Left shows code in _Dracula_ theme with syntax highlighting. • Right shows 2D grid lighting row-by-row. • Next, divide grid into colored blocks for threads. • Threads process simultaneously; colored scanning rectangles move independently.                       | “This program just traverses a 2D array counting odd numbers. We divide it for multiple threads — surely it should be faster.”                                                    |
| **1:00 – 1:25** | **The Performance Drop**                            | • Runtime comparison graph animates in. • Bars: single-thread (short green), multi-thread (long red). • Subtitle: _‘50% slower’_. • Bars grow longer as thread count increases.                                                                                                      | “But instead of improving performance, it runs about fifty percent slower. Adding more threads only makes it worse.”                                                              |
| **1:25 – 1:45** | **Video Roadmap**                                   | • Montage preview: CPU cross-section, cache hierarchy, cores communicating, optimized final run. • Quick flashes of upcoming visuals.                                                                                                                                                | “In this video, we’ll see why that happens, how caches work, and how to fix it.”                                                                                                  |
| **1:45 – 2:30** | **The Memory Bottleneck**                           | • Chart: CPU clock speed line rising sharply; memory speed line flat. • Animate a “Bottleneck” barrier between CPU and RAM. • Transition to close-up of CPU and memory modules.                                                                                                      | “As processors got faster, main memory couldn’t keep up. Fetching from RAM became the bottleneck.”                                                                                |
| **2:30 – 3:00** | **Cache Hierarchy Visualization**                   | • Build a pyramid or layered stack: Main Memory → L3 → L2 → L1 → CPU core. • Animate data moving upward quickly. • Add latency bars: L1 small, L2 larger, etc.                                                                                                                       | “To bridge that gap, CPUs added caches — L1, L2, L3 — each smaller, faster, and closer to the core.”                                                                              |
| **3:00 – 3:45** | **How Memory Is Organized**                         | • Zoom into memory strip divided into small rectangles labeled _Word_. • Add binary addresses on the left. • Animate contiguous blocks grouped into one _Cache Line_ moving into L1 cache.                                                                                           | “Memory is accessed in fixed-size chunks called words. CPUs fetch data in cache lines — groups of nearby words.”                                                                  |
| **3:45 – 4:45** | **Multi-Core Cache Interaction**                    | • Show two or three CPU cores, each with its own L1/L2 cache. • One core modifies data — cache line glows red. • Other cores’ same line fades out = invalidation. • Then alternate writes cause the cache line to bounce back and forth — _ping-pong_ effect.                        | “Each core has its own caches. When one writes data, others must invalidate their copies. When threads modify data in the same cache line, we get false sharing.”                 |
| **4:45 – 5:15** | **Return to the Example – False Sharing in Action** | • Show earlier grid visualization. • Now overlay faint dashed boxes showing cache line boundaries. • Threads writing trigger flashing red invalidations across shared lines. • Graph overlay: performance drops sharply.                                                             | “In our example, each thread wrote into the same array — sharing cache lines. Every write caused invalidations, slowing everything.”                                              |
| **5:15 – 5:45** | **The Fix – Thread Local Storage**                  | • Show separate threads each with their own small local buffer. • They work independently (no flashing red lines). • At the end, results merge into a single array. • Add text: _‘Thread-local = No false sharing’_.                                                                 | “Use thread-local variables, then combine the results. This eliminates false sharing and restores performance.”                                                                   |
| **5:45 – 6:00** | **Cache Pitfalls in OOP**                           | • Visualize array of objects. • Accessing a small boolean lights up the entire large object in cache. • Grayed-out fields show waste. • Next, show scattered virtual function pointers causing cache misses with flashing ‘MISS’ icons.                                              | “In object-oriented code, small accesses can drag huge objects into cache. Virtual functions scattered in memory also hurt performance.”                                          |
| **6:00 – 6:30** | **Conclusion & Summary**                            | • Clean infographic summary: “Caches determine performance.” • Comparison bars: single-threaded > multi-threaded (unoptimized), then optimized multi-threaded overtakes. • End card: _‘Optimize for Cache, Not Just for Cores’_. • Fade to black with soft ambient background score. | “Caches decide whether your code runs in microseconds or milliseconds. When threads fight over cache lines, more cores can make code slower. Optimize for cache, not just cores.” |

---



#### Questions
1. What are cache lines Done
2. How do CPUs invalidate cache lines. Not needed
3. How do cores on a different processor acccess the data. Not needed
4. Whats the size of caches on latest intel processors.
##### References
1. [Cache Coherency Issues](https://www.youtube.com/watch?v=WDIkqP4JbkE)
2. [Os Scheduler](https://www.ardanlabs.com/blog/2018/08/scheduling-in-go-part1.html)