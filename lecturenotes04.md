## [Lecture 04: Interprocess Communication (Chapter 3)](https://github.com/missystem/cis415review/blob/master/lecture-4-ipc.pdf)

## Outline
* [Interprocesses Communication (IPC)](https://github.com/missystem/cis415review/blob/master/lecturenotes04.md#interprocess-communication-ipc)
* [Summary](https://github.com/missystem/cis415review/blob/master/lecturenotes04.md#summary)


### Interprocess Communication (IPC)
* Allow cooperating processes to exchange data
* Why provide environment that allows process cooperation?
	- Information sharing
	- Computation speedup
	- Modularity
* 2 fundamental models:
	- Shared memory
		- A region of memory that is shared by the cooperating processes is established
		- Processes can then exchange information by reading and writing data to the shared region
		- Faster than message passing
	- Message passing
		- Communication takes place by means of messages exchanged between the cooperating processes
		- Useful for exchanging smaller amounts of data (no conflicts need be avoided)
		- Easier to implement in a distributed system than shared memory
<img width="553" height="353" src="https://github.com/missystem/cis415review/blob/master/communication_models.png">

### IPC in Shared-Memory Systems
* Producer-Consumer Problem
* 2 types of buffers
	- Unbounded buffer 
		- Variable in size (no practical limit on the size of the buffer)
		- The consumer may have to wait for new items
		- The producer can always produce new items
	- Bounded buffer
		- Fixed in size
		- The consumer must wait if the buffer is empty
		- The producer must wait if the buffer is full

### IPC in Message-Passing Systems
* Communication link logical implementations
	- Direct or indirect communication
	- Synchronous or asynchronous communication 
	- Automatic or explicit buffering
* Naming
* More sections from book p127

### Communication in Client–Server Systems
* Sockets
	- An endpoint for communication
	- A pair of processes communicating over a network employs a pair of sockets (one for each process) <br />
	<img width="640" height="440" src="https://github.com/missystem/cis415review/blob/master/communication_models.png"> <br />
* Remote Procedure Calls (RPC)
	- message-based communication scheme
	- Each message is addressed to an RPC daemon listening to a **port** on the remote system, and each contains an identifier specifying the function to execute and the parameters to pass to that function
		- port: a \# included at the start of a msg packet
	- The function is then executed as requested, and any output is sent back to the requester in a separate message



## Summary
* **Shared memory**
	- Two (or more) processes share the same region of memory
* **Message passing**
	- Two processes communicate by exchanging messages with one another
* **Pipe**
	- provides a conduit for two processes to communicate
	- 2 forms of pipes:
		- Ordinary pipes
		- Named pipes
* 2 common tyeps of **client–server communication**
	- **sockets**
		- allow two processes on different machines to communicate over a network
	- **remote procedure calls (RPCs)**
		- abstract the concept of function (procedure) calls in such a way that a function can be invoked on another process that may reside on a separate computer




