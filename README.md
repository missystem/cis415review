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


### [Lecture 01: Introduction to Operating Systems (Chapter 1)](https://github.com/missystem/cis415review/blob/master/lecture-1-introduction.pdf)

**Operating System** is a program that acts as an intermediary between a user of a computer and the computer hardware

 
**OS system goals**
* Execute user programs
* Make solving user problems easier
* Make the computer system covenient to use
* Use the computer hardware in an efficient manner

**Computer System Structure**
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

**Basic Computer System Organization**
* Computer-system operation
	- One or more CPUs, device ontrollers connect through common bus providing access to shared memory
	- Concurrent execution of CPUs and devices competing for memory cycles
<img src="https://github.com/missystem/cis415review/blob/master/ch1_systembus.png">

