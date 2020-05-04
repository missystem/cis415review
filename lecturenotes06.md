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






























