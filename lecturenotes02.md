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
* CPU fetches instruction and data from memory
	- into its instruction processing
	- into its cache / registers
* Memory controller implements logic for:
	- reading / writing to DRAM
	- refreshing DRAM to maintain contents
	- address translation circuitry for virtual memory

<img src="https://github.com/missystem/cis415review/blob/master/pc_motherboard.png">

### I/O Devices, Hard Disks, and SSD
*  Large variety, varying speeds, lot of diversity
	- Disk, tape, monitor, mouse, keyboard, NIC, ...
	- Serial or parallel interfaces
* Each device has a **controller**
	- Hides low-level details from OS (hardware interface)
	- Manages data flow between device and CPU/memory
* Hard disks are *secondary storage devices*
	- Mechanically operated with sequential access
	- Cheap (bytes / $), but slow
	- Orders of magnitude slower than main memory
* Solid state devices (SSD) are increasingly common

### Interconnects
* **bus** - hardware interconnect for supporting the exchange of data, control, signals, ...
	- Physicalspecification
	- Defined by a protocol
	- Data and control arbitration
* System bus - for CPU connection to memory primarily
	- Also to bridge to device buses
* PCI bus - for devices
	- Connects CPU-memory subsystem to:
		- fast devices
		- expansion bus that connects slow devices
* Other device "bus" types
	- SCSI, IDE, USB, ...
<img src="https://github.com/missystem/cis415review/blob/master/system_bus.png">

### Operating System Services
* Provides an environment for program execution
* For helping the user
	- User Interface (UI)
	- Program execution (load, run, terminate)
	- I/O operations (file or device)
	- File-system manipulation (file, directories, permission)
	- Communications (same computer or several over network) 
	- Error detection (hardware and software)
* For ensuring the efficient operation of the system
	- Resource allocation (across multiple, concurrent jobs)
	- Logging (or Accounting) (how much and what kind of resources)
	- Protection and security (resources, users)

Figure 2.1 A view of operating system services.
<img src="https://github.com/missystem/cis415review/blob/master/figure2.1_view_of_OS_services.png">

### OS Services and Hardware Support
* Protection
	- Kernel / User mode and protected instructions
	- Base / Limit registers
* Scheduling
	- Timer
* System Calls
	- Trap instructions
* Efficient I/O
	- Interrupts
	- Memory-mapping
* Synchronization
	- Stomic Instructions
* Virtual memory
	- Translation Lookaside Buffer (TLB)

### Kernel / User Mode
* A modern CPU has at least two execution modes
	- prevent user programs from interfering with the proper operation of the system
	- indicated by status bit in a protected CPU register
	- OS kernel runs in privileged mode (also called *kernel mode* or *supervisor mode*)
	- Applications run in normal mode (i.e., not privileged)
* OS can switch the processor to user mode
	- CPU then can only access user process address space
	- Can not do other things as well, such as talk to devices
* Certain events need the OS to run
	- must switch the processor to privileged mode
	- e.g., I/O control, timer management, and interrupt management
* OS redefinition
	- Software that runs in privileged mode

### Protected Instructions
* Instructions that require privilege, such as:
	- Direct access to I/O
	- Modify page table pointers or the TLB
	- Enable and disable interrupts
	- Halt the machine
* Access sensitive registers or perform sensitive
operations
* Only allow access in privilege modes
	- Otherwise, random programs may crash the machine

### Base and Limit Registers
* User processes must be protected from each other
* OS must be protected from user processes
* Memory referencing hardware to protect memory regions
	- Base register contains an address offset in physical memory (holds the smallest legal physical memory address)
	- Limit register is the maximum address that can be referenced (specifies the size of the range)
	- Both can be loaded only by the operating system before execution
* CPU checks each memory reference to make sure it is in proper range
* Both for instruction and data addresses
* Ensures references can not access other memory regions and corrupt memory

### Interrupts
* A key way in which hardware interacts with the operating system
* A hardware device triggers an interrupt by sending a signal to the CPU to alert the CPU that some event requires attention
* The interrupt is managed by the interrupt handler
* Interrupts make events that occur in the system visible for the OS needs to observe
* Interrupts can occur at any time
* OS polls for events (polling)
	- Inefficient use of resources
* OS is interrupted when events occur
	- I/O device has own logic (processor)
	- When device operation finishes, it pulls on interrupt bus
	- CPU “handles” interrupt

### Interrupt Vectoring
* Interrupts are asynchronous signals that indicate some need for attention by the OS
	- Replaces polling for events
* Represent:
	- Normal events to be noticed and acted upon
		- device notification
		- software system call
	- Abnormal conditions to be corrected (divide by 0)
	- Abnormal conditions that cannot be corrected (disk failure)
* Interrupt vectors are used to decide what to do with different interrupts
	- Address where interrupt routines live in the OS for different interrupt types
 <img src="https://github.com/missystem/cis415review/blob/master/figure1.5_Intel_processor_event_vector_table">

### Hardware Interrupts
* Signal from a device
	- Implemented by a controller (e.g., memory)
* Examples
	- Timer (clock)
	- keyboard, mouse
	- end of DMA transfer
* Response to processor request
* Unsolicited response
	- asynchronous












