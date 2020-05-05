## [Lecture 08: Deadlocks (Chapter 8)](https://github.com/missystem/cis415review/blob/master/lecture-8-deadlocks.pdf)

## Outline
* System Model
* Deadlock Characterization
* Methods for Handling Deadlocks q Deadlock Prevention
* Deadlock Avoidance
* Deadlock Detection
* Recovery from Deadlock

---

### System Model
* System consists of resources
* Resource types R<sub>1</sub>, R<sub>2</sub>, . . ., R<sub>m</sub>
	- CPU cycles, memory space, I/O devices, ...
* Each resource type R<sub>i</sub> has W<sub>i</sub> instances
* Each process utilizes a resource as follows:
	- Request
	- Use
	- Release
* We want a general way to think about problems like the dining philosophers

### Deadlock Characterization
* Deadlock can arise if four conditions hold simultaneously: 
	1. <u>Mutual exclusion</u>
		- only one process at a time can use a resource
	2. <u>Hold and wait</u>
		- a process holding at least one resource is waiting to acquire additional resources held by other processes
	3. <u>No preemption</u>
		- a resource can be released only voluntarily by the process holding it, after that process has completed its task using the resource
	4. <u>Circular wait</u>
		- there exists a set {T<sub>1</sub>, T<sub>2</sub>, ..., T<sub>n</sub>} of waiting processes such that T<sub>1</sub> is waiting for a resource that is held by T<sub>2</sub>, T<sub>2</sub> is waiting for a resource that is held by T<sub>3</sub>, ..., T<sub>n-1</sub> is waiting for a resource that is held by T<sub>n</sub>, and T<sub>n</sub> is waiting for a resource that is held by T<sub>1</sub>

### Resource Allocation Graph
* Aset of vertices V and a set of edges E
* V is partitioned into two types:
	- T = {T<sub>1</sub>, T<sub>2</sub>, ..., T<sub>n</sub>}, the set consisting of all the processes in the system
	- R = {R<sub>1</sub>, R<sub>2</sub>, ..., R<sub>m</sub>}, the set consisting of all resource types in the system
* *Request edge*
	- Directed edge T<sub>i</sub> -> R<sub>j</sub>
* *Assignment edge*
	- Directed edge R<sub>j</sub> -> T<sub>i</sub>
* A resource allocation graph is used to determine “state” of the resource system

### Resource-Allocation Graph Symbols
* Process
<br /><img width="62.5" height="54" src="https://github.com/missystem/cis415review/blob/master/process.png"><br />
* Resource type with 4 instances
<br /><img width="25" height="25" src="https://github.com/missystem/cis415review/blob/master/resourcetype4instance.png"><br />
* T<sub>i</sub> requests instance of R<sub>j</sub>
<br /><img width="62" height="38" src="https://github.com/missystem/cis415review/blob/master/requestinstance.png"><br />
* T<sub>i</sub> is holding an instance of R<sub>j</sub>
<br /><img width="63" height="38" src="https://github.com/missystem/cis415review/blob/master/holdinginstance.png"><br />

### Example of a Resource Allocation Graph
* The sets T, R, and E:
	- T = {T<sub>1</sub>, T<sub>2</sub>, T<sub>3</sub>}
	- R = {R<sub>1</sub>, R<sub>2</sub>, R<sub>3</sub>, R<sub>4</sub>}
	- E = {T<sub>1</sub> → R<sub>1</sub>, T<sub>2</sub> → R<sub>3</sub>, R<sub>1</sub> → T<sub>2</sub>, R<sub>2</sub> → T<sub>2</sub>, R<sub>2</sub> → T<sub>1</sub>, R<sub>3</sub> → T<sub>3</sub>}
	<br /><img width="135" height="195" src="https://github.com/missystem/cis415review/blob/master/RAG.png"><br />
* Resource instances:
	- One instance of resource type R<sub>1</sub>
	- Two instances of resource type R<sub>2</sub>
	- One instance of resource type R<sub>3</sub>
	- Three instances of resource type R<sub>4</sub>
* Thread states:
	- Thread T<sub>1</sub> is holding an instance of resource type R<sub>2</sub> and is waiting for an instance of resource type R<sub>1</sub>
	- Thread T<sub>2</sub> is holding an instance of R<sub>1</sub> and an instance of R<sub>2</sub> and is waiting for an instance of R<sub>3</sub>
	- Thread T<sub>3</sub> is holding an instance of R<sub>3</sub>
* Given the definition of a resource-allocation graph, it can be shown that
	- If the graph contains no cycles, then no thread in the system is deadlocked.
	- If the graph does contain a cycle, then a deadlock may exist

### Resource Allocation Graph With A Deadlock
<br /><img width="140" height="200" src="https://github.com/missystem/cis415review/blob/master/RAGwithDeadlock.png"><br />
* Look for hold and wait conditions that create a circular waiting state
	- Every process is waiting on a resource that is being held by another process in the cycle
* If there is a circular wait state, then no process can proceed because they are all blocked
	- Every process is waiting indefinitely because no resource will be released

### Resource Allocation Graph With a Cycle but No Deadlock.
<br /><img width="165" height="195" src="https://github.com/missystem/cis415review/blob/master/RAGwithCyclenoDeadlock.png"><br />
* It is possible for there to be a cycle in the resource allocation graph, but the resource system is not deadlocked
* There is no deadlock. Observe that thread T<sub>4</sub> may release its instance of resource type R<sub>2</sub> 
* That resource can then be allocated to T<sub>3</sub>, breaking the cycle

### Summary about Resource Allocation Graph
* If resource allocation graph contains no cycles:
	- no deadlock
* If resource allocation graph contains a cycle: 
	- If there is only one instance per resource type <br />
	==> deadlock
	- If there are several instances per resource type <br />
	==> possibility of deadlock

### Methods for Handling Deadlocks
* Basically, we need to ensure that the system will never enter a deadlock state
* This can be done in 2 ways:
	- *Deadlock prevention*
	- *Deadlock avoidance*
* Allow the system to enter a deadlocked state, detect it, and recover
	- Need to guarantee that recovery is possible and valid
* Ignore the problem altogether and pretend that deadlocks never occur in the system
	- Used by most operating systems, including UNIX

### Deadlock Prevention – Requirements
* Idea is to restrain the ways request can be made
* Mutual exclusion
	- Not required for sharable resources (e.g., read-only files) 
	- Must hold for non-sharable resources
* Hold and wait
	- Must guarantee that whenever a process requests a resource, it does not hold any other resources (strict)
	- Require process to request and be allocated ALL of its resources before it begins execution
	- Allow process to request resources only when the process has none allocated to it
	- Low resource utilization and starvation possible
* No preemption
	- If a process that is holding some resources requests another resource that cannot be immediately allocated to it, then all resources currently being held by that process are released
	- Preempted resources are added to the list of resources for which the process is waiting
	- Process will be restarted only when it can regain its old resources, as well as the new ones that it is requesting
* Circular wait
	- Impose a total ordering of all resource types, and require that each process requests resources in an increasing order of enumeration

### Example using Pthread Mutex Locks
* Think of the mutex locks as representing a resource being requested
* Does this code generate a deadlock?
* Why or why not?
* What is wrong?
* Hint: order matters
```
/* Thread one runs in this function */ 
void *do_work_one(void *param) 
{
	pthread_mutex_lock(&first_mutex); pthread_mutex_lock(&second_mutex);
		/** 
		* Do some work 
		*/ 
	pthread_mutex_unlock(&second_mutex);
	pthread_mutex_unlock(&first_mutex);

	pthread_exit(0); 
}

/* Thread two runs in this function */ 
void *do_work_two(void *param) 
{
	pthread_mutex_lock(&second_mutex); pthread_mutex_lock(&first_mutex);
		/** 
		* Do some work 
		*/ 
	pthread_mutex_unlock(&first_mutex);
	pthread_mutex_unlock(&second_mutex);

	pthread_exit(0); 
}
```

### An Account Transaction Example
* Transactions 1 and 2 execute concurrently
	- Transaction 1 transfers $25 from account A to account B 
	- Transaction 2 transfers $50 from account B to account A
* Locks are locked in an order, unlocked in reverse
* Deadlock example with lock ordering
```
void transaction(Account from, Account to, double amount) { 
	mutex lock1, lock2;
	lock1 = get_lock(from);
	lock2 = get_lock(to);

    acquire(lock1);
    	acquire(lock2);

	    	withdraw(from, amount);
	        deposit(to, amount);
    	release(lock2);
   	release(lock1);
}
```

### Deadlock Avoidance
* Requires that the system has some additional *a priori* information available
* Simplest and most useful model requires that each process declare the maximum number of resources of each type that it may need
* The deadlock-avoidance algorithm dynamically examines the resource allocation state to ensure that there can never be a circular-wait condition
* Resource allocation state is defined by the number of available and allocated resources, and the *maximum* possible demands of the processes

### Safe State
* When a process requests an available resource, it must be decided if immediate allocation of the resource to the process leaves the system in a safe state
q System is in safe state if there exists a sequence
<T<sub>1</sub>, T<sub>2</sub>, ..., T<sub>n</sub>> of ALL the processes in the system
such that for each T<sub>i</sub>, the resources that T<sub>i</sub> can still request can be satisfied by currently available resources + resources held by all the T<sub>j</sub>, with j < i
	- If T<sub>i</sub> resource needs are not immediately available, then Pi can wait until all T<sub>j</sub> have finished
	- When T<sub>j</sub> is finished, T<sub>i</sub> can obtain needed resources, execute, return allocated resources, and terminate
	- When T<sub>i</sub> terminates, T<sub>i</sub>+1 can obtain its needed resources

### Basic Facts
* If a system is in safe state <br />
	==> no deadlocks
* If a system is in unsafe state <br />
	==> possibility of deadlock
* Avoidance achieved by ensuring that a system will never enter an unsafe state

### State Relationships
* A resource allocation graph can be in 2 mutually exclusive states: 
	- safe
	- unsafe
* In the unsafe state, a resource allocation graph can be vulnerable to deadlock or in a deadlocked condition
* Safe, unsafe, and deadlocked state spaces
<br /><img width="162" height="162" src="https://github.com/missystem/cis415review/blob/master/safeunsafedeadlockstate.png"><br />

### Deadlock Avoidance Algorithms
* Avoidance algorithms prevent deadlocks from ever happening
	- Never allowing an unsafe state to be entered)
* Approaches depend on assumptions about the resource allocation graph
* Single instance of a resource type
	- Use a resource allocation graph to evaluate
* Multiple instances of a resource type
	- Must run an algorithm on the resource allocation graph
	- Use the Banker’s algorithm

### Resource Allocation Graph Scheme
* Claim edge P<sub>i</sub> -> R<sub>j</sub> indicates that process P<sub>j</sub> may request resource R<sub>j</sub>
	- Represented by a dashed line
* Claim edge converts to a *request edge* when a process requests a resource
* Request edge converted to an *assignment edge* when the resource is allocated to the process
* When a resource is released by a process, assignment edge reconverts to a claim edge
* Resources must be claimed a priori in the system

### Resource Allocation Graph for Deadlock Avoidance
<br /><img width="140" height="140" src="https://github.com/missystem/cis415review/blob/master/RAGfordeadlockavoidance.png"><br />

### An Unsafe State in a Resource Allocation Graph.
<br /><img width="140" height="135" src="https://github.com/missystem/cis415review/blob/master/RAGunsafestate.png"><br />

### Resource Allocation Graph Algorithm
* Suppose that process T<sub>i</sub> requests a resource R<sub>j</sub>
* The request can be granted only if converting the request edge to an assignment edge does not result in the formation of a cycle in the resource allocation graph
* Cycles are evaluated using all types of edges, including claim edges

### Banker’s Algorithm
* Suppose each resource type has multiple instances
* Requirements:
	- Each process must a priori claim maximum use
	- When a process requests a resource it may have to wait
	- When a process gets all its resources it must return them in a finite amount of time
* Banker’s algorithms is a bookkeeping method for tracking and assigning resources

### Data Structures for Banker’s Algorithm
* Let n = number of processes, and <br />
	m = number of resources types
* **Available**: Vector of length m. If ```Available[j] = k```, there are k instances of resource type R<sub>j</sub> available.
* **Max**: n x m matrix. If ```Max[i,j] = k```, then process P<sub>i</sub> may request at most k instances of resource type R<sub>j</sub>
* **Allocation**: n x m matrix. If ```Allocation[i,j] = k``` then P<sub>i</sub> is currently allocated k instances of R<sub>j</sub>
* **Need**: n x m matrix. If Need[i,j] = k, then P<sub>i</sub> may need k more instances of R<sub>j</sub> to complete its task <br />
	**Need [i,j] = Max[i,j] – Allocation [i,j]**

### Safety Algorithm
1. Let Work and Finish be vectors of length m and n, respectively. Initialize:<br />
	Work = Available<br />
	Finish[i] = false for i = 0, 1, ..., n-1<br />
2. Find an i such that both: <br />
	Finish[i] = false <br />
	Need<sub>i</sub> ≤ Work <br />
	If no such i exists, go to step 4 <br />
3. Work = Work + Allocation<sub>i</sub> <br />
	Finish[i] = true <br />
	go to step 2 <br />
4. If Finish[i] == true for all i, then the system is in a safe state

### Resource-Request Algorithm for Process Pi
* Request<sub>i</sub> = request vector for process P<sub>i</sub>
* If Request<sub>i</sub>[j] = k then process Pi wants k instances of resource type R<sub>j</sub>
	1. If Request<sub>i</sub> ≤ Needi go to step 2
	Otherwise, raise error condition, since process has exceeded its maximum claim
	2. If Request<sub>i</sub> ≤ Available, go to step 3
	Otherwise Pi must wait, since resources are not available
	3. Pretend to allocate requested resources to P<sub>i</sub>: <br />
	Available = Available – Request<sub>i</sub>; <br />
	Allocation<sub>i</sub> = Allocation<sub>i</sub> + Request<sub>i</sub>; <br />
	Need<sub>i</sub> = Need<sub>i</sub> – Request<sub>i</sub>;<br />
	If safe => the resources are allocated to P<sub>i</sub><br />
	If unsafe => P<sub>i</sub> must wait, restore old resource allocation

### Banker's Algorithm
* 5 processes P<sub>0</sub> through P<sub>4</sub>
* 3 resource types:
	- A (10 instances), B (5 instances), C (7 instances)
* Snapshot at time T<sub>i</sub>:

| Process | Allocation | Max | Available |
|:-------:|:----------:|:---:|:---------:|
|		  |     ABC    | ABC |    ABC    |
| P<sub>0</sub> | 010 | 753 | 332 |
| P<sub>1</sub> | 200 | 322 |	  |
| P<sub>2</sub> | 302 | 902 |	  |
| P<sub>3</sub> | 211 | 222 |     |
| P<sub>4</sub> | 002 | 433 |     |

### Check for Safety
* Matrix *Need* is defined to be Max – Allocation

| Process | Need |
|:-:|:-:|
| | ABC |
| P0 | 74 | 3 |
| P1 | 12 | 2 |
| P2 | 60 | 0 |
| P3 | 01 | 1 |
| P4 | 43 | 1 |








