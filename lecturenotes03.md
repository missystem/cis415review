## [Lecture 03: Processes (Chapter 3)](https://github.com/missystem/cis415review/blob/master/lecture-3-processes.pdf)

## Outline
* [Process Concept](https://github.com/missystem/cis415review/blob/master/lecturenotes03.md#process-concept)
* [Process Creation](https://github.com/missystem/cis415review/blob/master/lecturenotes03.md#process-creation)
* Process Operation
* [System Calls to Create Processes](https://github.com/missystem/cis415review/blob/master/lecturenotes03.md#program-creation-system-calls)
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

### Process Execution State
* As a process executes, it changes state
	- New:		  The process is being created (starting state)
	- Running:	  Instructions are being executed
	- Waiting:	  The process is waiting for some event to occur
	- Ready:      The process is waiting to run
	- Terminated: The process has finished execution (end state)
<img src="https://github.com/missystem/cis415review/blob/master/process_state.png">

### Process State
* consists of:
	- [Address space](https://github.com/missystem/cis415review/blob/master/lecturenotes03.md#process-address-space) (what can be addressed by a process)
	- Execution state (what is need to execute on the CPU)
	- Resources being used (by the process to execute)
* Address space contains code and data of a process
* Processes are individual execution contexts
	- Threads are also include here
* Resources are physical support necessary to execute
	- Memory: physical memory, address translation support
	- Storage: disk, files, ...
	- Processor: CPU (at least 1)

### A Process in Memory
* A process has to reference memory for different purposes
	- Instructions
	- Stack
		- subroutine “frames”, local variables
	- Data
		- static and dynamic (heap)
* Logical memory
	- is what can be referenced by an address
	- # address bits in instruction address determine logical memory size
	- a “logical address” is from 0 to the size of logical memory (max)
* Compiler and OS determine where things get placed in logical memory
<img width="412" height="524" src="https://github.com/missystem/cis415review/blob/master/process_in_memory.png">

### Process Address Space
* All locations addressable by process
	- Also called logical address space
	- Every process has one
* Restrict addresses to different areas
	- Restrictions enforced by OS
	- Text segment is where read only program instructions are stored
	- Data segment hold the data for the running process (read/write)
		- heap allows for dynamic data expansion
	- Stack segment is where the stack lives
* Process (logical) address space starts at 0 and runs to a high address
<img width="310" height="450" src="https://github.com/missystem/cis415review/blob/master/address_space.png">

### Process Address Space
* Program (Text)
* Global Data (Data)
* Dynamic Data (Heap)
	- grows up
* Thread-local Data (Stack)
	- grows down
* Each thread has its own stack
* \# address bits determine the addressing range <br />
<img width="363" height="493" src="https://github.com/missystem/cis415review/blob/master/process_add_space01.png">

```
int value = 5;					Global

int main() {
	int *p;					Stack

	p = (int *)malloc(sizeof(int));		Heap

	if (p == 0) {
		printf("ERROR: Out of memory\n”);
		return 1; 
	}

	*p = value; 
	printf("%d\n", *p); 

	free(p);
	return 0;
}
```

### [Process Address Space in Action](https://github.com/missystem/cis415review/blob/master/ProcessAddressSpaceInAction.md)

### Process Creation
* What happens?
	- New process object in the OS kernel is created
		- build process data structures
	- Allocate address space (abstract resource)
		- later, allocate actual memory (physical resource)
	- Add to execution (ready) queue
		- make it runnable
* Is the OS a process?
	- Yes, it is the first process when system is booted

### Process Creation Options (Parent and Child)
* Process hierarchy options
	- Parent process (very first process) creates Children processes
	- Child processes can create other child processes
	- Tree of processes
	<img src="https://github.com/missystem/cis415review/blob/master/process_tree.png">
* Resource sharing options
	- Parent and children share all resources
	- Children share subset of parent’s resources
	- Parent and child share no resources
* Execution options
	- Parent and children execute concurrently
	- Parent waits until children terminate
* Address space
	- Child duplicate of parent
	- Child has a program loaded into it

### Executing a Process
* What is required?
* Registers store state of execution in CPU
	- Stack pointer
	- Data registers
* Program count to indicated what to execute?
	- CPU register holding address of next instruction
* A “thread of execution”
	- able to execute instructions
	- has its own stack
	- each process has at least 1 thread of execution
* A process thread executes instructions found in the process’s address space ...
	- usually the text segment
* ... until a trap or interrupt
	- Time slice expires (timer interrupt)
	- Another event (e.g., interrupt from other device)
	- Exception (program error)
	- System call (switch to kernel mode)

### Process Control Block (PCB)
* also called the *task control block*
* contains information associated with a specific process
* including:
	- Process state:
		- new, ready, running, waiting, halted, etc
	- Program counter
		- indicates the address of the next executed process's instruction
	- CPU registers
		- for process thread
		- vary in number and type, depending on the computer architecture
	- CPU-scheduling information
		- includes a process priority, pointers to scheduling queues, and any other scheduling parameters
	- Memory-management information
		- memory allocated to the process
	- Accounting information
		- the amount of CPU and real time used, time limits, account numbers, job or process numbers, etc
	- I/O status information
		- the list of I/O devices allocated to the process
		- a list of open files, etc
<img style="float: right;" src="https://github.com/missystem/cis415review/blob/master/PCB.png">

### Program Creation System Calls
* *fork()*
	- Copy address space of parent and all threads
* *forkl()*
	- Copy address space of parent and only calling thread
* *vfork()*
	- Do not copy the parent’s address space
	- Share address space between parent and child
* *exec()*
	- Load new program and replace address space
	- Some resources may be transferred (open file descriptors)
	- Specified by arguments

### Process Creation with New Program
<img src="https://github.com/missystem/cis415review/blob/master/fork.png"><br />
* **Parent process** calls *fork()* to spawn child process
	- Both parent and child return from fork()
	- Continue to execute the same program
* **Child process** calls *exec()* to load a new program

### C Program Forking Separate Process
```
int main( )
{
	pid_t pid;
	// fork another process
	pid = fork( );
	if (pid < 0) { // error occurred
		fprintf(stderr, "Fork Failed"); exit(-1);
	} 

	else if (pid == 0) { /* child process */
		execlp("/bin/ls", "ls", NULL); // exec a file
	} 

	else { 		/* parent process */
		// parent will wait for the child to complete
		wait(NULL);
		printf ("Child Complete"); 
		exit(0);
	} 	
}
```
```
execl, execlp, execle, execv, execvp, execvpe
```
all execute a file and are frontends to execve <br />

### Process Termination
* Process executes last statement and asks the OS to delete it (via *exit()*)
	- Output data from child to parent (via *wait()*)
	- Process' resources are deallocated by OS
* Parent may terminate execution of children processes (via *abort()*) if:
	- Child has exceeded allocated resources
	- Task assigned to child is no longer required
	- Parent is exiting
		- some OS do not allow child to continue if parent terminates
		- all children terminated - cascading termination

### Process Layout
<img align="left" width="401" height="402" src="https://github.com/missystem/cis415review/blob/master/process_layout.png">

1. PCB with new PID created 
2. Memory allocated for child initialized by copying over from the parent
3. If parent had called wait(), it is moved to a waiting queue 
4. If child had called exec(), its memory is overwritten with new code and data
5. Child added to ready queue and is
all set to go now








