## [Lecture 05: Threads (Chapter 4)](https://github.com/missystem/cis415review/blob/master/lecture-5-threads.pdf)

## Outline

### Program with Multiple Processes


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

