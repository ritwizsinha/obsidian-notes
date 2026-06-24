


#### Malloc

For outputting the memory layout of a program running we can run 
```
cat /proc/<proc_id>/maps

```
This will give us the read only and read write data section, as well as beginning of the heap and stack

![[Pasted image 20251123011334.png]]



Some items malloced can be in the mmap region using mm_malloc, while some at the heap addresses.
Ideally the addresses specify by the terminal command should map to what the address in the pointers are.

`Brk` command enlarges the heap.
We can examine the memory addresses of let's say n number of elements starting from a pointer using
```
x/100 ptr
```

![[Pasted image 20251123014330.png]]


The whole of heap memory is stored as an implicit free list, which means, is that just from the header block, you can go to where the next chunk is, at the end if the chunk is of type `0x01` then we have reached the end header, denoting that this is the total memory allocated.

Coalescing is taking 2 free blocks and turning them into one free block. After every single free we do a coalescing of the blocks above and below it.
#### Some Gotchas
1. Getting malloced, memory is not sure to have zero values, because we might have some leftover from previous allocations.




##### References
1. [what's in a heap? simple malloc implementation internals explained](https://www.youtube.com/watch?v=EhmZvyNA-1A)
2. [ What does it take to design a memory allocator? Dynamic Memory Allocation System Design](https://www.youtube.com/watch?v=UTii4dyhR5c)