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
 <img src="https://github.com/missystem/cis415review/blob/master/figure1.5_Intel_processor_event_vector_table.png">

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
<img src="https://github.com/missystem/cis415review/blob/master/logical_diagram_of_interrupt_routing.png">

### Timer 
* OS needs timers for:
	- Time of day, CPU scheduling, ...
	- There can be multiple timers for different things
* Based on a hardware clock source (e.g., 1 Ghz)
	- Count-down timer (e.g., 1 Ghz to 1 Khz)
	- Generates an interrupt at 0 (e.g., every 1 msec)
* Use timers to ensure that certain future events occur
* Most importantly, it is a way for OS to regain control
* User programs can set up their own “software” timers

### Software Interrupts
* Software interrupts (traps)
	- Special interrupt instructions
		- int is the interrupt instruction
		- int 0x80 passes control to interrupt vector 0x80
	- Exceptions
		- can be fixed (e.g., page fault)
		- cannot be fixed (e.g., divide by zero)
* All invoke OS, just like a hardware interrupt
	- Trap starts running OS code in supervisor access space
	- This space can not be overwritten by the user program

### How Does a Process Run? (high level view)
* OS keeps track of which process is assigned to which sections in memory along with other details
* For a new process to run, memory is assigned by the OS, which puts the code in that location
	- switch to user mode
	- start running at first address of the program
* OS keeps record of every process
	- This is the *process context*
	- Assigned memory, current Program Counter, ...
	- Enough info to restart process where it left off

### Then along comes an interrupt ...
1. A hardware interrupt or a trap (software interrupt) happens
	- Example: received input from keyboard
2. OS records state of running process’s context
	- stored in a Process Control Block (PCB)
3. OS services the interrupt
	- Example: send something to the printer
4. OS picks process to restart
	- which process is picked depends on scheduling
	- moves back into user mode

### Interrupt Handling
* Each interrupt has an *interrupt handler*
* When an interrupt request (IRQ) is received
	- If interrupt mask allows interrupt, then ...
		1. save state of current process
			- at time of interrupt something else may be running
			- state: Registers (stack pointer), program counter, ...
		2. execute handler
		3. return to current process or another process

Interrupt (Trap) Handlers
<img src="https://github.com/missystem/cis415review/blob/master/interrupt_handler.png">

Multiple Interrupts
<img src="https://github.com/missystem/cis415review/blob/master/multiple_interrupts.png">

### Device Access
* Port I/O
	- uses special I/O instructions
	- uort number, device address (not process address)
* Memory-mapped I/O
	- uses memory instructions (load/store)
		- memory-mapped device registers
	- does not require special instructions
[<img src="https://github.com/missystem/cis415review/blob/master/isolated_vs_memory_mapped_I/O.png">](http://ece-research.unm.edu/jimp/310/slides/8086_IO1.html)
IORC: I/O Read Control 		IOWC: I/O Write Control

### Direct Memory Access (DMA)
* Direct access to I/O controller through memory
* Reserve area of memory for communication with
the I/O device
	- Video RAM
		- CPU writes frame buffer
		- video card displays it
	- Network interfaces
* DMA is efficient and convenient and fast
	- Multiple components can talk to other components concurrently, rather than competing for cycles on a shared bus
	- After setting up buffers, pointers, and counters for the I/O device, the device controller transfers an entire block of data directly to or from the device and main memory, with no intervention by the CPU
	- Only one interrupt is generated per block, to tell the device driver that the operation has completed, rather than the one interrupt per byte generated for low-speed devices
	- While the device controller is performing these operations, the CPU is available to accomplish other work
<img src="https://github.com/missystem/cis415review/blob/master/figure1.7_how_modern_computers_work.png">

### Synchronization
* How can OS synchronize concurrent processes?
	- multiple threads, processes, interrupts, DMA
* CPU must provide mechanism for atomicity
	- Series of instructions that execute as one or not at all
* One approach:
	- Disable interrupts, perform action, enable interrupts
	- Advantages:
		- Requires no hardware support
		- Conceptually simple
	- Disadvantages:
		- Could cause starvation
* A Modern Synchronization Approach
	- Use hardware support for atomic instructions
		- Small set of instructions that cannot be interrupted
	- Examples:
		- **Test-and-set (TST)** <br />
		if word contains given value, set to new value
		- **Compare-and-swap (CAS)** <br />
		if word equals value, swap old value with new
		- Intel: LOCK prefix (XCHG, ADD, DEC, ...)
	- Used to implement locks 

### Process Address Space
* Process Address Space is all locations that are addressable by the process
* Every running program can have its own private address space
* Can restrict use of addresses so as to isolate different area
	- Restrictions are enforced by OS
	- Text section
		- read only program instructions are stored in
		- the executable code
	- Data section
		- hold the data for the running process (read/write)
		- global variables
	- Heap section
		- memory that is dynamically allocated during program run time
		- allows for dynamic data expansion
	- Stack section
		- temporary data storage when invoking functions <br />
		(such as function parameters, return addresses, and local variables)
	- the sizes of the text and data sections are fixed, as their sizes do not change during program run time <br />
	the stack and heap sections can shrink and grow dynamically during program execution
<img src="https://github.com/missystem/cis415review/blob/master/figure3.1_layout_of_a_process_in_memory.png">








