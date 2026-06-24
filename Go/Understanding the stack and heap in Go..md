
- In go each go routine has a separate stack.
- In go we don't know where the variable is getting stored thus we don't need to care, although it does affect speed.
- Anything on the heap is managed in the garbage collector leading to a higher latency for the whole program.
- We need to look into this problem only if it shows excessive heap allocations 
- ![[Pasted image 20250913214943.png]]Go's stack is self cleaning, it knows at a particular time what part of stack is valid and what is not and it can cleanup the part which is invalid.
- ![[Pasted image 20250913215458.png]]
  Sharing data down the stack is not the problem, but if we declare a variable in the downstream function and try to pass it upstream, go compiler detects it and allocates that variable in heap memory. This is called "escape to heap". Thus sharing up typically escapes to the heap. But only the compiler knows at the end. We can ask the compiler using the -m flag.
  ```go
	1. go help build
	2. go tool compile -h
	3. go build -gcflags "-m"
```
Thus at the compile time, it is already decided whether we move to heap or not.

#### When does value go in the heap
- When the value is of undefined size
- When the value size is defined but too large
- When the value is accessed outside of its stack frame.

Thus the reader interface looks liek this
![[Pasted image 20250913220516.png]]
This way, this byte variables, doesn't esscape to the heap and stays on the stack, because if it returned a bunch of bytes, that would have to be on the heap.