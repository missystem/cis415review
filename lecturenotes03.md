## [Lecture 03: Processes (Chapter 3)](https://github.com/missystem/cis415review/blob/master/lecture-3-processes.pdf)

## Outline
* [Process Concept](https://github.com/missystem/cis415review/blob/master/lecturenotes03.md#process-concept)
* Process Operation
* System Calls to Create Processes
* Process Management
* Process Scheduling

### Overview of Processes
* How are processes created?
	- from binary program to executing process
* How is a process represented and managed?
	- process creation, process control block
* How does the OS manage multiple processes?
	- process state, ownership, scheduling
* How can processes communicate?
	- interprocess communication, concurrency, deadlock

### Superview and User Modes
* OS runs in “supervisor” mode
	- has access to protected (privileged) instructions only
available in that mode (kernel executes in ring 0)
	- allows it to manage the entire system
* OS “loads” programs into processes
	- run in user mode
	- many processes can run in user mode at the same time

### Process Concept
* An operating system executes a variety of programs:
	- Batch system 
		- jobs
	- Time-shared systems
		- user programs or tasks
	- job & process used almost interchangeably
* A process is a **program in execution**
	- Process execution can result in more processes being created
* Multiple parts of a process
	- Program code 
		- image, text
	- Execution state
		- program counter, processor registers, ...
	- Stack 
		- containing temporary data (e.g., call stack frames) 
			- function parameters, return addresses, local variables, ...
	- Data section
		- containing global variables
	- Heap
		- containing memory dynamically allocated during run time











