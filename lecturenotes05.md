## [Lecture 05: Threads (Chapter 4)](https://github.com/missystem/cis415review/blob/master/lecture-5-threads.pdf)

## Outline

### Program with Multiple Processes
<img width="811" height="487" src="https://github.com/missystem/cis415review/blob/master/prog_with_multiple_processes.png">

### [Program Creation System Calls](https://github.com/missystem/cis415review/blob/master/lecturenotes03.md#program-creation-system-calls)
* fork()
	- Copy address space of parent and all threads
* vfork()
	- Do not copy the parent’s address space
	- Share address space between parent and child
	- While parent blocks, child must exit or call exec
* exec()
	- Load new program and replace address space
	- Some resources may be transferred (open file descriptors)
	- Specified by arguments
* clone()
	- Like fork() but child shares some process context
	- More explicit control over what is shared
	- Process address space can be shared!
	- Calls a function pointed to by argument

### Process Model
*  Much of the OS’s job is keeping processes from interfering with each other
* Each process has its OWN resources to use
	- Program code to execute, address space, files, ...
* Processes are good for isolation (protection)
	- Prevent one process from affecting another process
* Processes are *heavyweight*
	- Pay a price for isolation
	- A full “process swap” is required for multiprocessing 
	- There is lots of process state to save and restore
	- OS must context switch between them 
		- intervene to save/restore all process state
* Is there an alternative?

### Why Threads?
* A process is “a program in execution”
	- Memory address space containing code and data
	- Other resources (e.g., open file descriptors)
	- State information (PC, register, SP) => PCB details
* Consider a process in 2 respects (categories) 
	1. Collection of resources required for execution
		- code, address space, open files, ...
	2. A “thread of execution”
		- current state of execution (CPU state) 
		- where the execution is now (PC)
* Suppose we think about these separately!
	- Resources
	- Execution state

### Terminology
* Multiprogramming
	- Running multiple programs on a computer concurrently
	- Each program is one process or a set of processes
	- Processes of different programs are independent
* Multiprocessing
	- Running multiple processes on a computer concurrently 
	- OS manages mapping of processes to processor(s)
* Multithreading
	- Define multiple execution contexts (threads of execution) in a single address space (of a process)
	- OS manages mapping of threads to an address space
	- OS manages mapping of threads to processor(s)

### What’s a Thread?
* A basic unit of CPU utilization; 
* It comprises 
	- a thread ID
	- a program counter (PC)
	- a register set
	- a stack
* Thread of execution through a program on a CPU
	- Program counter					(*per thread*)
	- Registers							(*per thread*)
* Memory
	- Address space						(*process*)
		- address space is *shared*
	- Stack 							(*per thread*)
		- each thread has its own stack pointer
	- Heap <br />						(*process*)
		\+ private dynamic memory 		(*per thread*)
* I/O
	- Share files, sockets, ...			(*process*)

### Why Multithreaded Applications?
* Multiple threads sharing a common address space
	- More correctly, they share process resources, including memory
* Why would you want to do that?
	- Some applications could be written to support concurrency
	- How do you get concurrency?
		- One way is to create multiple processes
		- Use IPC to support process-level concurrency
* Some applications want to share data structures among concurrently executing parts of the computation
	- Is this possible with processes? => shared segments
	- Is it difficult with processes? => well, it is not that easy
	- Again, use IPC for process-level data sharing
* What is the problem? What is the solution?

### Advantages of Threads
* Threads could be used if there is no need for the OS to enforce resource separation
	- This is a trust issue, in part (back off on isolation / protection)
	- However, introduces more issues with respect to concurrency
* Benefits
	* Responsiveness (improved)
		- Possible to have a thread of execution that never blocks
	* Resource sharing (is facilitated)
		- All threads in a process have equal access to resources
	* Economy (of resources)
		- Thread-level resources are “cheaper” than process resources!
		- Threads are “lighter weight”
	* Utilization of multiprocessors (Scalability)
		- Run multiple threads on multiple processes without the overhead of running multiple processes

### Single-Threaded vs. Multithreaded
* Regular UNIX process can be considered as a special case of a multithreaded process
	- A process that contains just one thread
* Multithreaded process has multiple threads
<img width="1328" height="604" src="https://github.com/missystem/cis415review/blob/master/singleThreaded_multithreaded.png">

### Working with Threads
*  In a C program
	- *main()* procedure defines the first thread 
	- C programs always start at *main()*
* Now create a second thread
	- Allocate resources to maintain a second execution context in same address space
		- think about what state will be needed for a thread
	- Want something similar to *fork()* but simplere
		- supply a procedure name when start the new thread
	- Remember this creates another thread of execution

### Threads vs. Processes (difference)
* Easier to create than a new process
* Less time to terminate a thread than a process
* Less time to switch between two threads 
	- within the same process
* Less communication overheads
	- communicating between the threads of one process is simple because the threads share everything 
	- address space is shared
	- thus memory is shared

### Which is Cheaper?
* Create new process or create new thread (in existing process)?
* Context switch between processes or threads? 
* Interprocess or Interthread communication?
* Sharing memory between processes or threads?
* Terminating a process or terminating a thread (not the last one)? <br />

| Process creation method | Time (sec), elapsed (real) |
| -----------------------:|:--------------------------:|
| *fork()*                | 22.27 (7.99)               |
| *vfork()* (faster fork) | 3.52 (2.49)                |
| *clone()*               | 2.97 (2.14)                |







## Summary
* **thread** 
	- a basic unit of CPU utilization
	- threads belonging to the same process share many of the process resources
		- including code and data
* There are 4 primary benefits to multithreaded applications: 
	1. responsiveness
	2. resource sharing
	3. economy
	4. scalability.
* **Concurrency** 
	- multiple threads are making progress, whereas 
* **Parallelism** 
	- multiple threads are making progress simultaneously. 
* On a system with a single CPU, only concurrency is possible; parallelism requires a multicore system that provides multiple CPUs.
* challenges in designing multithreaded applications:
	- dividing and balancing the work
	- dividing the data between the different threads
	- identifying any data dependencies
	- test and debug (especially challenging)
* **Data parallelism** 
	- distributes subsets of the same data across different computing cores and performs the same operation on each core
* **Task parallelism** 
	- distributes tasks across multiple cores
	- each task is running a unique operation
* User applications create **user-level threads**
	- must ultimately be mapped to kernel threads to execute on a CPU
	- approaches:
		- many-to-one: maps many user-level threads to one kernel thread
		- one-to-one 
		- many-to-many
• **thread library** 
	- provides an API for creating and managing threads
	- 3 common thread libraries: Windows, Pthreads, Java threading
	- Windows
		- for the Windows system only
	- Pthreads 
		- available for POSIX-compatible systems such as UNIX, Linux, and macOS
	- Java threads 
		- run on any system that supports a Java virtual machine (JVM)
• **Implicit threading**
	- involves identifying tasks — not threads
	- allowing languages or API frameworks to create and manage threads
	- Approaches
		- thread pools
		- fork-join frameworks
		- Grand Central Dispatch
	- an increasingly common technique for programmers to use in developing concurrent and parallel applications.
• **Threads Termination**
	- Asynchronous cancellation
		- stops a thread immediately, even if it is in the middle of performing an update
	- Deferred cancellation
		- informs a thread that it should terminate but allows the thread to terminate in an orderly fashion
	- In most circumstances, deferred cancellation is preferred to asynchronous termination.
• Linux system
	- does not distinguish between processes and threads;
	- it refers to each as a task
	- *clone()* system call used to create tasks 
		- behave either more like processes or more like threads

