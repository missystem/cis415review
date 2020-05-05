## [Lecutre 07: Concurrency and Synchronization (Chapter 6/7)](https://github.com/missystem/cis415review/blob/master/lecture-7-synchronization.pdf)

## Outline
* Background
* Critical-Section Problem
* Peterson’s Solution
* Synchronization Hardware
* Mutual Exclusion (Mutex) Locks
* Semaphores
* Monitors
* Classic Problems of Synchronization

---

### Roadmap
<img width="408" height="250" src="https://github.com/missystem/cis415review/blob/master/roadmap.png">

### Concurrency and Synchronization
* Processes can execute concurrently (logically, physically)
	- May be interrupted at any time, partially completing execution
	- OS must concurrently execute its own software
* There are different kinds of resources that are shared between processes:
	- Physical (terminal, disk, network, ...)
	- Logical (files, sockets, memory, ...)
	- Memory
* Concurrent access to shared resources must be done in a consistent manner or else errors arise
* Maintaining data consistency requires mechanisms to ensure the orderly execution of cooperating processes
* This is the role of synchronization

### Resources
* Focus on “memory” as the shared resource for the purposes of this discussion
	- Processes can all read and write into memory 
	- Suppose multiple processes share memory
		- use IPC shared memory segments mechanisms
	- It might be easier to think of multiple threads 
		- sharing memory is natural

### Example Problem Due to Sharing
* Consider a shared printer queue
	- *```spoolQueue[n]```*
* 2 processes want to enqueue an element each to this queue
* *```tail```* points to the current end of the queue
	- It is also a shared variable
* Each process needs to do <br />
	```
	tail = tail + 1; 
	spoolQueue[tail] = "element";
	```

### What are trying to do?
* Want to have “consistent” and “correct” execution 
<br /><img width="355" height="125" src="https://github.com/missystem/cis415review/blob/master/spoolQueue.png"><br />
* What could go wrong?
	- ```tail = tail + 1``` is NOT a single machine instruction
		- So, what? Why do we care?
	- What assembly code does the compiler produce? <br />
	*Load tail, R1 <br />
	Add R1, 1, R2 <br />
	Store R2, tail <br />*
	- These 3 machine instructions might NOT be executed atomically ... Why not?
		- To *execute atomically* means to execute multiple instructions logically together as if they were a single instruction without being interrupted
	- What is the problem?

### Interleaving
* Each process executes this set of 3 instructions
* Interrupts might happen at any time
	- a context switch can happen at any time
* Suppose we have the following scenario:
<br /><img width="253" height="125" src="https://github.com/missystem/cis415review/blob/master/Interleaving.png"><br />




























