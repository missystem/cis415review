## [Lecutre 02: OS Structure and System Calls (Chapter 2)](https://github.com/missystem/cis415review/blob/master/lecture-2-structure.pdf)

### Outline
* Hardware and OS relationship
* Operating System Services
* User Operating System Interface q System Calls
* Types of System Calls
* System Programs
* Operating System Design and Implementation q Operating System Structure
* Operating System Debugging
* Operating System Generation
* System Boot

### Objectives
* To describe computer system organization
* To describe the services an operating system provides to users, processes, and other systems
* To discuss the various ways of structuring an operating system
* To explain how operating systems are installed and customized and how they boot

### Canonical System Hardware
* CPU
	- processor to perform computations
* Memory
	- hold instructions and data
* I/O devices
	- disk, monitor, network, printer
* Bus
	- systems interconnection for communication
<img src="https://github.com/missystem/cis415review/blob/master/canonical_system_hardware.png">

### CPU Architecture
* CPUs are semiconductor device with digital logic
	- Arithmetic Logical Unit (ALU)
	- Program Counter (PC) and registers
	- Instruction Set Architecture (ISA)
* Registers
	- part of ISA
	- CPU's scratchpads for program execution
	- fastest memory available in a computer system
	- instruction, data, address (n-bit architecture, 32-bit or 64-bit)
* Cache
	- fast memory (close to CPU)
	- faster than main memory, but more expensive
	- managed by the hardware (not seen by the OS)
* Clock to synchronizes constituent circuits

### Memory
* Random Access Memory (RAM)
	- Semiconductor DIMMs on PCB
	- Volatile Dynamic RAM (DRAM)
* Use as "main" memory for instruction and data
* OS manages main memory
	- It is a resource necessary for any code execution
* CPU fetches instruction and date from memory
	- into its instruction processing
	- into its cache / registers
* Memory controller implements logic for:
	- reading / writing to DRAM
	- refreshing DRAM to maintain contents
	- address translation circuitry for virtual memory













