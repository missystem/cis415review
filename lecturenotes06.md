## [Lecture 06: CPU Scheduling (Chapter 5)](https://github.com/missystem/cis415review/blob/master/lecture-6-scheduling.pdf)

## Outline
* Basic scheduling concepts and criteria 
* Scheduling algorithms
* Thread scheduling
* Multiple processor scheduling
* Real-Time CPU scheduling
* Algorithm evaluation
---

### Resource Allocation
* In a multiprogramming / multiprocessing system, OS shares resources among running processes
	- There are different types of OS resources?
* Which process gets access to which resources and why?
	- To maximize performance
	- To increase utilization, throughput, responsiveness, ... 
	- Enforce priorities

### Resource Types
* Memory
	- Allocate portion of finite resource
	- Physical resources are limited
	- Virtual memory tries to make this appear infinite
* I/O
	- Allocate portion of finite resource and time spent with the resource
	- Store information on disk
	- A time slot to store that information
* CPU
	- Allocate time slot with resource
	- A time slot to run instructions

### Types of CPU Scheduling
* CPU resource allocation => **Scheduling**
* *Long-term scheduling* (admission)
	- determining whether to add to the set of processes to be executed
* *Medium-term scheduling*
	- determining whether to add to the number of processes partially or fully in memory (degree of multiprogramming)
* *Short-term scheduling*
	- determining which process will be executed by the processor
* *I/O scheduling*
	- determining which process’s pending I/O request will be handled by an available I/O device

### CPU Scheduling Views
* Single process view
	- GUI (graphical user interface) request
		- click on the mouse (*responsiveness*) 
	- Scientific computation
		- long-running, but want to complete ASAP (*time to solution*)
* System view (objectives)
	- Get as many tasks done as quickly as possible
		- *throughput* objective
	- Minimize waiting time for processes
		- *response time* objective
	- Get full utilization from the CPU
		- *utilization* objective

### Process Scheduling
* Process transition diagram
* OS perspective
<img width="707" height="353" src="https://github.com/missystem/cis415review/blob/master/processscheduling.png">

### When does scheduling occur?
* CPU scheduling decisions may take place when:
	1. A process switches from running -> waiting
	2. A process switches from running -> ready
	3. A process switches from waiting -> ready
	4. A process terminates
* Process voluntarily gives up (yields) the CPU
	- CPU scheduler kicks in an decides who to go next
	- Process gets put on the ready queue
	- It could get immediately re-scheduled to run
 
### Scheduling Problem
* Choose the ready/running process to run at any time
	- Maximize “performance”
* Model (estimate) “performance” as a function 
	- System performance of scheduling each process
		- *f(process) = y*
	- What are some choices for *f(process)*?
* Choose the process with the best y
	- Estimating overall performance is intractable
	- Scheduling so all tasks are completed ASAP is a NP-complete problem
	- Adding in preemption does not help

### Preemption
* Can we reschedule a process that is actively running (i.e., preempt its execution)?
	- If so, we have a preemptive scheduler
	- If not, we have a non-preemptive scheduler
* Suppose a process becomes ready
	- A new process is created or it is no longer waiting
* There is a currently running process
* However, it may be “better” (whatever this means) to schedule the process just put on the ready queue
	- So, we have to preempt the running process
* In what ways could the new process be better?

### Basic Concepts – CPU-I/O Bursts
* Maximum CPU utilization is obtained with multiprocessing ... Why?
* CPU–I/O burst cycle
	- Process execution consists of cycles of CPU execution and I/O wait
		- run instructions (CPU burst)
		- wait for I/O (I/O burst)
* CPU burst distribution
	- How much a CPU is used during a burst?
	- This is a main concern ... Why?
* Scheduling is aided by knowing the length of these bursts
<img width="240" height="430" src="https://github.com/missystem/cis415review/blob/master/cpu_IO_burst.png">

### Histogram of CPU Burst Times
* Profile for a particular process
<img width="650" height="425" src="https://github.com/missystem/cis415review/blob/master/histogram_of_cpu_burst.png">

### Dispatcher
* Dispatcher module gives control of the CPU to the process selected by the *short-term* scheduler
* This involves:
	- Switching context
		- save context of running process
		- loading process context of selected process to run
	- Switching to user mode
	- Jumping to the proper location in the user program to continue execution of the program
* *Dispatch latency*
	- time it takes for the dispatcher to stop one process and start another running
	- Context switch time

### Scheduling Criteria
* *Utilization / Efficiency*
	- Keep the CPU busy 100% of the time with useful work
* *Throughput*
	- Maximize the number of jobs processed per hour.
* *Turnaround time (latency)*
	- From the time of submission to the time of completion.
* *Waiting time*
	- Sum of time spent (in ready queue) waiting to be scheduled on the CPU
* *Response time*
	- Time from submission until the first response is produced (mainly for interactive jobs)
* *Fairness*
	- Make sure each process gets a fair share of the CPU

### Scheduling Algorithm Optimization Criteria
* Max CPU utilization 
* Max throughput
* Min turnaround time 
* Min waiting time
* Min response time

### Scheduling Algorithms
* Some may seem intuitively better than others
* But a lot has to do with the type of offered *workload* to the processor
* Best scheduling comes with best context of the tasks to be completed
	- Knowing something about the workload behavior is important
* Distinguish between non-preemptive and preemptive cases
	- This has to do with whether a running process can be stopped during its execution and put back on the ready queue so that another process can acquire the CPU and run

### First-Come, First-Served (FCFS)
* Serve the jobs in the order they arrive
* Managed with a FIFO queue
* Non-preemptive
	- Process is run until it has to wait or terminates
	- OS can not stop the process and put it on ready queue
* Simple and easy to implement
	- When a process is ready, add it to tail of ready queue, and serve the ready queue in FCFS order
* Fair
	- No process is starved out
	- Service order is immune to job size (does not depend)
	- It depends only on *time of arrival*

| Process | Burst Time |	Burst time here represents the job’s entire execution time
|:-------:|:----------:| 
|    P1   |     24     | 
|    P2   |      3     |
|    P3   |      3     |




























