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
* Leading to incorrect execution:
<br /><img width="380" height="120" src="https://github.com/missystem/cis415review/blob/master/incorrectExecution.png"><br />
* ==> Race condition

### Race Conditions
* Several processes access and manipulate the same data concurrently and the outcome of the execution depends on the particular order in which the access takes place
* Race conditions are timing dependent
	- Errors can be non-repeatable
* Race conditions CAN cause the “state” of the execution to be inconsistent (incorrect)
	- It does not mean that because there is a race condition that the state WILL become inconsistent

### How to avoid race condition?
* Atomic: 
	- A set of instructions is **atomic** if it executed as if it was a single instruction (logically)
* What does this mean exactly?
	- Executing the instructions can not be interrupted?
	- It has more to do with the outcomes of executing the instructions with respect to other processes
* Suppose the 3 assembly instructions we were looking at were atomic
* Does this avoid the race condition?
* Critical Section: 
	- When executing a set of instructions is vulnerable to a race condition, that set of instructions are said to constitute a **critical section**
	- the process may be accessing — and updating — data that is shared with at least one other process

### Critical Section Problem (Dijkstra, 1965)
* Consider system of n processes {P<sub>0</sub>, P<sub>1</sub>, ... P<sub>n-1</sub>}
* Each process has critical segment of code
	- Process may be changing common variables, updating table, writing file, ... (in its critical section)
	- When one process is in its critical section, no other may be in its critical section (mutual exclusion)
* *Critical section problem* is to design a *protocol* between the processes to solve this
	- Each process must enter the critical section (entry section)
	- Each process then executes the critical section instructions 
	- Each process must exit the critical section (exit section)
	- Each process executes outside the critical section

### Critical Section
* General structure of process *P<sub>i</sub>*
<br /><img width="400" height="215" src="https://github.com/missystem/cis415review/blob/master/generalCriticalSectionStructure.png"><br />
* It is the entry and exit code that defines the critical section protocol
* The infinite do loop is just to suggest that a process will possibly want to enter its critical section multiple \#s of times, including never (0 times) or just once (1 time)

### Requirements for Solution to CS Problem
1. Mutual exclusion
	- If process *P<sub>i</sub>* is executing in its critical section, then no other processes can be executing in their critical sections
2. Progress
	- If no process is executing in its critical section and some processes wish to enter their critical sections, then only those processes that are not executing in their remainder sections can participate in deciding which will enter its critical section next, and this selection cannot be postponed indefinitely
3. Bounded waiting
	- There exists a bound, or limit, on the number of times that other processes are allowed to enter their critical sections after a process has made a request to enter its critical section and before that request is granted
	- Assume that each process executes at a nonzero speed
	- No assumption concerning relative speed of the *N* processes

### How to Implement Critical Sections
* Implementing critical section solutions follows 3 fundamental approaches
	1. Disable Interrupts
		- Effectively stops the scheduling of other processes
		- Does not allow another process to get the CPU
	2. Busy Waiting / Spinlock solutions
		- Pure software solutions
		- Integrated hardware-software solutions
	3. Blocking Solutions

### Disabling Interrupts
* We already know how to prevent another process from interrupting the current running process
	- Do not allow interrupts to occur by disabling them
* Advantages:
	- Simple to implement (single instruction)
* Disadvantages:
	- Do not want to give such power to user processes
	- Does not work on a multiprocessor ... Why?
		- Disabling interrupts on a multiprocessor can be time consuming, since the message is passed to all the processors
		- This message passing delays entry into each critical section, and system efficiency decreases
		- Also consider the effect on a system’s clock if the clock is kept updated by interrupts
	- Disables multiprogramming even if another process is NOT interested in critical section

### Busy Waiting (aka Spinning)
* Overall philosophy:
	- Keep checking some state (variables) until they indicate other process(es) are not in critical section
<br /><img width="453" height="227" src="https://github.com/missystem/cis415review/blob/master/busyWaiting.png"><br />
* Remember, P1 and P2 might do this multiple \#s of times, including 0.

### Reading, Writing, and Testing Locks
* Is the instruction below atomic?
	<br /> A: load locked, R1 
	<br />cmp R1, 1
	<br />beq A<br />
	```while (locked == TRUE)```  
* How about these instructions?
	```
	locked = TRUE;
	locked = FALSE;
	```
* Generally, if the high-level statement compiles to a single machine instruction, it is atomic
* Need reading, writing, testing of locks to be atomic

### Try Strict Alternation
* Consider this code (Left)
* Idea is to take turns using the critical section
	- Variable turn is used for this
* Does it work?
* What problems do you see? 
	- Is there mutual exclusion?
	- Is there progress?
	- Is there bounded waiting?  
* Remember, P1 and P2 might do this multiple #s of times, including 0

<br /><img width="275" height="412" src="https://github.com/missystem/cis415review/blob/master/strictAlternation.png"><img width="277" height="320" src="https://github.com/missystem/cis415review/blob/master/fixingProgress.png"><br />

### Fixing the “progress” requirement
* What about this code? (Right)
* Each process has a flag to say that they want to enter the critical section
* Got mutual exclusion
* Problems?
	- Deadlocked
* For this reason, it does NOT meet the progress or bounded waiting requirements either








