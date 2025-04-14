

# Chapter 5

## Definitions (arrival time, etc.)

> [!definition|*] Burst time
> The total time taken by the process for its execution on the CPU.

> [!definition|*] Arrival time
> The time when a process enters into the ready state/is ready for its execution.

> [!definition|*] Completion time
> The time when a process completes its execution and exits the system.

> [!definition|*] Response time
> The interval a process spends in the ready queue and when it gets to the CPU for the first time.
> $$ RT = \text{time of first execution} - AT $$

> [!definition|*] Waiting time
> The total time a process is in the ready queue.
> $$ WT = TT - BT $$
> 

> [!definition|*] Turnaround time
> The interval of a process' submission to completion. 
> $$TT = CT - AT = BT + WT$$

> [!definition|*] Throughput
> The number of processes that are completed per unit of time.


## Scheduling algorithms

### FCFS

First-Come, First-Served Scheduling
- Simplest scheduling algorithm
- Whichever process requests the CPU first gets it first
- Managed with a queue
- Pros:
	- Simple
- Cons:
	- Average waiting time is often long

### SJF

Shortest-Job-First Scheduling
- Executes the process with the shortest-next-burst time
	- For ties, FCFS is used
- Can be preemptive or non-preemptive
	- A preemptive SJF will (upon arrival of a new process) preempt the currently-executing process, if its remaining time is longer than the new process' burst time
- Pros:
	- Minimum average waiting time
- Cons:
	- Potential for starvation or long turnaround times


### RR

Round-Robin Scheduling
- Similar to FCFS but with preemption
- Comes with a pre-defined **time quantum** $q$
- Scheduler limits processes to use burst times of $\leq q$
	- If a process exceeds $q$ it is preempted and put to the back of the ready queue
- Pros:
	- No possibility of starvation
- Cons:
	- Large $q \implies$ essentially becomes FCFS
	- Small $q \implies$ lots of context switches, low throughput


### Priority

Priority Scheduling
- A priority gets assigned to each process - the lowest # priorities go first
- SJF is a special case of this
- Can be preemptive or non-preemptive
- Pros:
	- The (defined) importance of a process decides which go first
- Cons:
	- Potential for starvation
		- Can be solved through **aging** (increasing priority of processes waiting for a long time)

### MFQ

Multilevel Queue Scheduling
- In practice it is often easier to have separate queues for each priority, where schedulers simply schedule processes in the highest-priority queue
- If there are multiple processes in the highest-priority queue, they are executed in RR order