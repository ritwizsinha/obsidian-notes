

#### Barrier
Let all threads upto n collect in a particular place and once collected release all of them. This can be considered as the starting line before a race.


**Semaphore:** Use a semaphore when you (thread) want to sleep till some other thread tells you to wake up. Semaphore 'down' happens in one thread (producer) and semaphore 'up' (for same semaphore) happens in another thread (consumer) e.g.: In producer-consumer problem, producer wants to sleep till at least one buffer slot is empty - only the consumer thread can tell when a buffer slot is empty.

**Mutex:** Use a mutex when you (thread) want to execute code that should not be executed by any other thread at the same time. Mutex 'down' happens in one thread and mutex 'up' _must_ happen in the same thread later on. e.g.: If you are deleting a node from a global linked list, you do not want another thread to muck around with pointers while you are deleting the node. When you acquire a mutex and are busy deleting a node, if another thread tries to acquire the same mutex, it will be put to sleep till you release the mutex.

**Spinlock:** Use a spinlock when you really want to use a mutex but your thread is not allowed to sleep. e.g.: An interrupt handler within OS kernel must never sleep. If it does the system will freeze / crash. If you need to insert a node to globally shared linked list from the interrupt handler, acquire a spinlock - insert node - release spinlock.




### Solving Concurrency Problems
1. Understand what the shared variables are.
2. Try to create a State machine for transition of these variables.



### Concurrency Afterthoughts
1. Using raw mutexes is unprofessional, as if the call crashes, no one is there to unlock mutex, 



##### Live Lock
Processes not making progress but still showing up as working.


#### Re entrant lock
This kind of lock allows for re entering of a synchronization lock, thus the thread doesn't 


1. Mutex implies mutual exclusion and is used to serialize access to critical sections whereas semaphore can potentially be used as a mutex but it can also be used for cooperation and signaling amongst threads. Semaphore also solves the issue of missed signals. 
2. Mutex is owned by a thread, whereas a semaphore has no concept of ownership. 
3. Mutex if locked, must necessarily be unlocked by the same thread. A semaphore can be acted upon by different threads. This is true even if the semaphore has a permit of one


