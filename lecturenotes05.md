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

