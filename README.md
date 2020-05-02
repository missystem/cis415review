# CIS 415 2020S Midterm Review

## Exam 2 parts:
 1. concepts (1.5h)
 2. problems (1.5h)
 
## Types of questions:
* multiple choices & T/F
* fill-in-the-blank & matching
* essay

## Lecture Slides
* [Lecture 01: Introduction to Operating Systems (Chapter 1)](https://github.com/missystem/cis415review/blob/master/lecture-1-introduction.pdf)
* [Lecutre 02: OS Structure and System Calls (Chapter 2)](https://github.com/missystem/cis415review/blob/master/lecture-2-structure.pdf)
* [Lecture 03: Processes (Chapter 3)](https://github.com/missystem/cis415review/blob/master/lecture-3-processes.pdf)
* [Lecture 04: Interprocess Communication (Chapter 3)](https://github.com/missystem/cis415review/blob/master/lecture-4-ipc.pdf)
* [Lecture 05: Threads (Chapter 4)](https://github.com/missystem/cis415review/blob/master/lecture-5-threads.pdf)
* [Lecture 06: CPU Scheduling (Chapter 5)](https://github.com/missystem/cis415review/blob/master/lecture-6-scheduling.pdf)
* [Lecutre 07: Concurrency and Synchronization (Chapter 6/7)](https://github.com/missystem/cis415review/blob/master/lecture-7-synchronization.pdf)
* [Lecture 08: Deadlocks (Chapter 8)](https://github.com/missystem/cis415review/blob/master/lecture-8-deadlocks.pdf)



## [Lecture 01: Introduction to Operating Systems (Chapter 1)](https://github.com/missystem/cis415review/blob/master/lecture-1-introduction.pdf)

### What is Operating System
- a program that acts as an intermediary between a user of a computer and the computer hardware

### What does an OS do?
* Acts as a Resource manager
* Process
	- hides programs from one another
* Traffic cop
	- resource management
	- decide which program to run and when
* Memory management
	- protection from other programs' mistakes
* Security
	- protection from other programs' malice
* System call interface
	- abstract, simplified interface to services
	- like a function library, but communicates with the OS
* Device management
* Communication
	- between processes
	- to devices & networks

 
### OS system goals
* Execute user programs
* Make solving user problems easier
* Make the computer system covenient to use
* Use the computer hardware in an efficient manner

### Computer System Structure
* Hardware
	- provides basic computing resources
	- CPU, memory, I/O devices
* Operating system
	- controls and coordinates use of hardware among various applications and users
* Application programs
	- use system resources to solve computing problems
	- word processors, compilers, web browsers, database systems, video games, ...
* Users
	- People, machines, other computers
 <img src="https://github.com/missystem/cis415review/blob/master/ch1_OSstructure.png">

### Basic Computer System Organization
* Computer-system operation
	- One or more CPUs, device ontrollers connect through common bus providing access to shared memory
	- Concurrent execution of CPUs and devices competing for memory cycles
<img src="https://github.com/missystem/cis415review/blob/master/ch1_systembus.png">

### Computer System Architecture
* Traditionally, most systems use a single general-purpose processor 
	- Most systems have special-purpose processors as well
* Modern computers use multiprocessor systems now
	- Advantages:
		- Increased throughput
		- Economy of scale
		- Increased reliability – graceful degradation or fault tolerance
	- Types:
		- Symmetric multiprocessing (SMP) - each processor performs all tasks
		- asymmetric multiprocessing – each processor has a specific task

	- Can run parallel applications

### Computer System Operation
* I/O devices & the CPU can execute concurrently
* Each device controller 
	- is in charge of a device type
	- has a local buffer
* CPU moves data between main memory & local buffers associated with I/O devices
* I/O is from the device -> local buffer of controller
* Device controller informs CPU that it has finished its operation by causing an **interrupt**

### What Operating Systems Do?
* User view:
	- ease of use, convenience, performance
	- dedicated resources
* Server (System) view:
	- shared resources
	- keep all users happy

### Operating System Definition
* Resource allocation and control
* as Resource allocator
	- manages all resources (CPU time, memory space, storage space, I/O devices, etc)
	- Figure possibly conflicting requests for resources
		- OS decides how to allocate them to specific programs and users so that it can operate the computer system efficiently and fairly.

* as Control program
	- manages the execution of user programs to prevent errors and improper use of the computer
* Different view on scope:
	- it includes everything a vendor ships when you order “the operating system”
	- the OS is the one program running at all times on the computer — usually called the kernel
		- 2 other types of programs: 
			- system programs, which are associated with the operating system but are not necessarily part of the kernel
			- application programs, which include all programs not associated with the operation of the system.

### Page Fault Handling
* is caused when access to a virtual memory location is not found in physical memory
* What happens?
	- Interrupt is generated by the hardware
	- Handler in OS determines how to obtain memory
	- If page is still on disk, then handler
		- allocates physical page
		- makes I/O request to disk via file system and driver
	- Driver copies page from disk into new physical page
	- OS restarts the process at the interrupted instruction
* Multiple processes -> more complex
* OS has to make trade-offs

### Learning about OS
* OS has many protocols like page fault handling
* OS designers add layers of abstraction to simplify programming (e.g., virtual memory)
* The design of protocols using these concepts involves trade-offs (e.g., disk performance)

### Device I/O - How a modern computer works?
How a modern computer system works?
	von Neumann architecture
<img src="https://github.com/missystem/cis415review/blob/master/ch1_von_Neumann_architecture.png">

### Common Functions of Interrupts
* Interrupt transfers control to the interrupt service routine, generally through an interrupt vectors
* The operating system preserves the state of the CPU by storing registers and the program counter
* Determines which type of interrupt has occurred: 
	- Polling
	- Vectored interrupt system
* Interrupt architecture must save the address of the
interrupted instruction
* A trap (or an exception) is a software-generated interrupt, caused 
	- by an error (division by zero or invalid memory access)
	- by a specific request from a user program, that an operating-system service be performed by executing a special operation called a system call
* An operating system is interrupt driven

### I/O Structure
After I/O starts, control returns to user program ...
* Only upon I/O completion
	- wait instruction idles the CPU until the next interrupt 
	- wait loop (contention for memory access)
	- at most one I/O request is outstanding at a time
* Without waiting for I/O completion
	- system call – request to the OS to allow user to wait for I/O completion
	- device-status table contains entry for each I/O device indicating its type, address, and state
	- OS indexes into I/O device table to determine device status and to modify table entry to include interrupt

### Interrupt Timeline
<img src="https://github.com/missystem/cis415review/blob/master/ch1_interrupt_timeline.png">

### I/O Sturcture - 2 Methods
<img src="https://github.com/missystem/cis415review/blob/master/ch1_two_IO_methods.png">
* Synchronous
	- After I/O starts, control returns to user program only upon I/O completion
	- A wait instruction idles the CPU until next interrupt
	- Or the CPU runs a wait loop (problems?)
	- No simultaneous I/O processing (only 1 request)
* Asynchronous
	- After I/O starts, control returns to user program without waiting for I/O completion
	- User program then waits for I/O complete
	- I/O interrupts upon completion

### Device Status Table
<img src="https://github.com/missystem/cis415review/blob/master/ch1_device_status_table.png">
* When a kernel supports asynchronous I/O, it must be able to keep track of many I/O requests at the same time. For this purpose, the operating system might attach the wait queue to a device-status table. The kernel manages this table, which contains an entry for each I/O device. Each table entry indicates the device’s type, address, and state (not functioning, idle, or busy). If the device is busy with a request, the type of request and other parameters will be stored in the table entry for that device.

### Timer Interrupts for Kernel Control
* Prevent infinite loop / process hogging resources
	- Timer is set to interrupt the computer after some time period
	- Keep a counter that is decremented by the physical clock
	- Operating system set the counter (privileged instruction)
	- When counter reaches zero, generate an interrupt
	- Set up before scheduling process to regain control or terminate program that exceeds allotted time

### Process Management
* 










