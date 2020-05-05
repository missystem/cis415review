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

<br /><img width="275" height="412" src="https://github.com/missystem/cis415review/blob/master/strictAlternation.png"><img width="374" height="432" src="https://github.com/missystem/cis415review/blob/master/fixingProgress.png"><br />

### Fixing the “progress” requirement
* What about this code? (Right)
* Each process has a flag to say that they want to enter the critical section
* Got mutual exclusion
* Problems?
	- Deadlocked
* For this reason, it does NOT meet the progress or bounded waiting requirements either

### Peterson’s Solution
* Consider 2 processes
* Assume that the LOAD and STORE instructions are atomic and cannot be interrupted
* The two processes share two variables: 
	```
	int turn;
	boolean flag[2]
	```
* Variable turn indicates whose turn it is to enter the critical section
	- The ```flag``` array 
		- indicate if a process is ready to enter the critical section
		- ```flag[i] = true``` implies that process P<sub>i</sub> is ready
* Algorithm for Process Pi and Process Pj
<br /><img width="560" height="330" src="https://github.com/missystem/cis415review/blob/master/PetersonsSolution.png"><br />

### Does Peterson’s Solution work?
* Prove that the 3 CS requirements are met:
	- Mutual exclusion is preserved <br />
	P<sub>i</sub> enters CS only if:<br />
		- either ```flag[j] = false``` or ```turn = i```
		- if both processes are interested in entering, then 1 condition is false for one and true for the other process
	- Progress requirement is satisfied
		- a process wanting to enter will be able to do so at some point
		- Why?
	- Bounded-waiting requirement is met
		- eventually it will be P<sub>i</sub>’s turn if P<sub>i</sub> wants to enter
* Peterson’s solution **ONLY** works for 2 process solutions

### Multiple Processes
* How do we extend for multiple processes?
* Is there a way to modify Peterson’s approach?
* Consider the following enter /exit routines:
```
int turn;
int flag[N]; /* all set to FALSE initially */

enterCS(int myid) {
    otherid = (myid+1) % N;
    turn = otherid;
    flag[myid] = TRUE;

    while (turn == otherid && flag[otherid] == TRUE) ;
    /* proceed if turn == myid or flag[otherid] == FALSE */
}

leave_CS(int myid) {
	flag[myid] = FALSE;
}
```
* Remember, processes might do this multiple #s of times, including 0

### Bakery Algorithm (Leslie Lamport, 1974)
* We need to enforce a sequence in some manner that everyone will follow and contribute to making progress
* Think about a bakery
* See [A New Solution of Dijkstra's Concurrent Programming Problem](http://lamport.azurewebsites.net/pubs/bakery.pdf)
```
Notation: (a,b) < (c,d) if a<c  or  a=c and b<d

Every process has a unique id (integer) Pi

bool choosing[0..n-1]; /* all set to FALSE */ 
int number[0..n-1]; /

enter_CS(myid) { 
	choosing[myid] = TRUE; 
	number[myid] = max(number[0],number[1],...,number[n-1]) + 1;
	choosing[myid] = FALSE; 
	for (j=0 to n-1) {
	    while (choosing[j])
	      ;
	    while (number[j] != 0) && ((number[j],Pj)<(number[myid],myid))
	      ;
	}
}

leave_CS(myid) {
	number[myid] = 0;
}
```
* Evaluation of the Bakery Algorithm
	- Does it work?
	- What do you need to prove?
	- Show that it meets
		- Mutual exclusion
		- Progress
		- Bounded waiting requirements
	- Need to know that maximum \# processes

### Looking to the Hardware
* Complications arose because we had atomicity only at the granularity of a machine instruction
	- What a machine instruction could do is (was) limited
* Can we provide specialized instructions in hardware to provide additional functionality (with an instruction still being atomic)?
	- Looking again to hardware to help solve a OS problem q Many systems (now) provide hardware support for implementing the critical section code
* All solutions below are based on idea of locking
	- Protecting critical regions via locks
	- But without special instructions

### Hardware Support for Synchronization
* Uniprocessors – could disable interrupts
	- Currently running code executes without preemption
	- Generally too inefficient on multiprocessor systems 
		- OS using this not broadly scalable
* Modern CPUs provide atomic hardware instructions (2 general types)
	- **Test-and-Set**
		- test memory word and set value
	- **Compare-and-Swap**
		- swap contents of 2 memory words

### Critical-section Solution Using Locks
<br /><img width="170" height="165" src="https://github.com/missystem/cis415review/blob/master/lock.png"><br />
* The infinite while loop is just to suggest that a process will possibly want to enter its critical section multiple \#s of times, including 0 times and 1 time
* The problem before was that ```acquire``` and ```release``` could not be done in a single instruction
* Suppose it could with a single atomic instruction

### test_and_set instruction
* Definition
```
boolean test_and_set(boolean *target) { 
	boolean rv = *target;
	*target = true;
	return rv;
}
```
* Assume it executes atomically
* Returns the original value of passed parameter
* Set the new value of passed parameter to TRUE

### Solution using test_and_set()
* Shared boolean variable ```lock```, initialized to FALSE
* Mutual-exclusion implementation with ```test_and_set()```:
```
while (TRUE){ 
	...

	while (test_and_set(&lock) == TRUE) 
		;

		/* critical section */

	lock = FALSE;

		/* remainder section */
}
```
* if two test and set() instructions are executed simultaneously (each on a different core), they will be executed sequentially in some arbitrary order

### compare_and_swap Instruction
* Definition:
```
int compare_and_swap(int *value, int expected, int newvalue)
{
	int temp = *value;

	if (*value == expected)
		*value = newvalue;

	return temp;
}
```
* Assume it executes atomically
* Returns the original value of passed parameter value
* Set the variable ```value``` to the value of the passed parameter ```newvalue```
	- Only if ```value == expected```
* That is, the swap takes place only under this condition

### Solution using compare_and_swap
* Shared integer ```lock``` initialized to 0
```
while (TRUE) {
	...

	while(compare_and_swap(&lock, 0, 1) != 0)
		;	/* do nothing */

		/* critical section */

	lock = 0;

		/* remainder section */
}
```
* Does is work?
	- satisfies the mutual-exclusion requirement
	- does not satisfy the bounded-waiting requirement

### Bounded-waiting with test_and_set
* N processes: P<sub>0</sub>, P<sub>1</sub>, ..., P<sub>N-1</sub>
* This is the code for P<sub>i</sub> (i.e., each process is executing this code with *i* set to the process ID (*0 ≤ i ≤ N-1*)
* Bounded-waiting mutual exclusion with ```compare_and_swap()```
```
while (TRUE) {
	waiting[i] = TRUE;
	key = TRUE;
	while (waiting[i] && key == 1)
		key = compare and swap(&lock,0,1); 
	waiting[i] = false;

	    /* critical section */

	j = (i + 1) % n;
	while ((j != i) && !waiting[j])
		j = (j + 1) % n;
	
	if (j == i)
		lock = 0;
	else
		waiting[j] = false;
	
		/* remainder section */
}
```

### Spinning vs. Blocking
* In the previous solutions, we are spinning (busy-waiting) for some condition to change
	- This change should be effected by some other process
	- We are “presuming” that this other process will eventually get the CPU
		- is this a reasonable assumption?
		- needs a preemptive scheduler ... Why?
* This can be *inefficient* because:
	- wasting time quantum spinning
	- Sometimes, the programs may not work
		- suppose if the OS scheduler is not preemptive

### Blocking Approaches
* If instead of busy-waiting, the process relinquishes the CPU at the time when it cannot proceed, the process is said to **block**
	- It is still wanting to get into the critical section
	- It is put in the blocked queue
* It is the job of the process changing the *condition* to wake up a blocked process
	- Moves it from blocked back to ready queue
* Advantage:
	- Do not unnecessarily occupy CPU cycles 
* Disadvantages?

### Blocking Example
<br /><img width="390" height="165" src="https://github.com/missystem/cis415review/blob/master/lock.png"><br />
* Think of L as a lock
* NOTE: These must be OS system calls! Why?












