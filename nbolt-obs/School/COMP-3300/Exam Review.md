
# Chapter 1

## Definitions

> [!definition|*] Kernel
> The core of the OS that controls everything in the system (THE program of the OS).

> [!definition|*] Multicore System
> A multicore system is when multiple computing cores reside on a single chip. 
> 
> These can be more efficient than multiple chips with single cores, because on-chip communication is faster than between-chip communication.

> [!definition|*] Multiprogramming System
> A form of parallel processing where several programs run at the same time on one processor (system).



# Chapter 2

## Definitions

> [!definition|*] OS Structure
> An OS structure is how an OS is organized. The common structures are:
> - Monolithic
> 	- No structure: all functionality is placed in a single, static binary file that runs in a single address space
> 	- Little overhead and fast communication
> 	- Difficult to implement/extend
> - Layered
> 	- OS is broken into multiple layers (levels)
> 	- Bottom layer (0) is hardware, highest is user interface
> 	- Simple to construct/debug
> 	- Poor performance
> - Microkernels
> 	- Removes all nonessential components from the kernel and implements them as user-level programs in separate address spaces (resulting in a smaller kernel)
> 	- Provides minimal process/memory management
> 	- Easy to extend/port
> 	- Poor performance
> - Modules
> 	- The kernel has a set of core components and can link additional services (via modules) either at boot or runtime
> 	- Idea is to provide core services while allowing other services to be implemented dynamically


# Chapter 3

## Definitions

> [!definition|*] Process Control Block
> Each process is represented in an OS by a **process control block (PCB)**. It contains many pieces of information associated with a process, including
> - Process state (new, ready, running, waiting, halted, etc.)
> - Program counter (the address of the next instruction to-be-executed)
> - CPU registers
> - CPU-scheduling information
> - Memory-management information
> - Accounting information
> - I/O status information



# Chapter 4

## Definitions

> [!definition|*] Multithreading Model
> A multithreading model is a way of establishing a relationship between user and kernel threads. The common models are
> - Many-to-one (many user threads to one kernel thread)
> - One-to-one (one user thread to one kernel thread)
> - Many-to-many (many user threads to many kernel threads)





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



# Chapter 9

## Definitions

> [!definition|*] Hole
> A **hole** is a block of available memory.

> [!definition|*] Fragmentation
> When a process is unloaded/loaded from memory resulting in inefficient allocation/usage. We have two types:
> - **External Fragmentation**: total memory space exists to satisfy a request, but is not contiguous
> - **Internal Fragmentation**: allocated memory may be slightly larger than the requested memory. This size difference is memory internal to a partition and is not being used.

> [!definition|*] Page
> To avoid external fragmentation and varying-sized memory chunks, physical memory is divided into fixed-sized blocks called frames. We divide logical memory into blocks of the same sizes - these are called **pages**.

> [!definition|*] Transition Look-Aside Buffer
> The **transition look-aside buffer (TLB)** is a fast-lookup hardware cache, typically storing address-space identifiers.

> [!definition|*] Hit Ratio
> The percentage of times that a page number is found in the TLB is called the **hit ratio**. For example, an 80% hit ratio means we find the desired page number in the TLB 80% of the time.

> [!definition|*] Effective Access Time
> **Effective access time (EAT)** is a weighted average for the time it takes to get a value from memory.
> 
> For example, given an 80% hit ratio and that 10ns are needed to access memory ($10*2=20$ if not found in TLB):
> $$ EAT = 0.80(10) + 0.20(20) = 12 \text{ns} $$

> [!definition|*] Backing Store
> A fast disk large enough to accommodate copies of all memory images for all users.



## Dynamic Storage Allocation Problem

How do we satisfy a request of size $n$ from a list of free holes?
- **First-fit**: allocate the *first* hole that is big enough
- **Best-fit**: allocate the *smallest* hole that is big enough
	- Requires searching the entire list (unless ordered by size)
	- Produces the smallest leftover hole
- **Worst-fit**: allocate the *largest* hole
	- Requires searching the entire list (unless ordered by size)
	- Produces the largest leftover hole


## Address Translation Scheme

Run a program of size $N$ pages, we need to find $N$ free frames. We set up a **page table** to translate logical and physical addresses.

The address generated by the CPU is divided into:
- **Page number** $p$: used as an index into a page table which contains the base address of each page in physical memory
- **Page offset** $d$: combined with the base address to define the physical memory address that is sent to the memory unit

## Implementing Page Tables

Page tables are kept in main memory
- Page-table base register (`PTBR`) points to the page table
- Page-table length register (`PTLR`) indicates size of the page table

Memory structures for paging can get huge using straight-forward methods. One simple solution is to divide the page table into smaller units:
- Hierarchical paging
	- Break up the logical address space into multiple page tables (commonly two-level)
	- Page the page table
	- For example, logical address pages outer page table, which pages inner page table
- Hashed page tables
	- Virtual page number is hashed into a page table, which contains a chain of elements
	- Each element contains
		- Virtual page number
		- Value of mapped page frame
		- Pointer to next element
	- Virtual page numbers are compared in this chain, searching for a match
- Inverted page tables
	- Tracks physical pages (instead of keeping track of all possible logical pages)
	- One entry for each real page
		- One entry consists of the virtual address of the page with information of the process that owns the page
	- Decreases memory needed to store each page table, but increases time needed to search


## Swapping

A process can be **swapped** temporarily out of memory to a **backing store** and then brought back into memory for continued execution (e.g., if processes use too much storage).

The swapped out process does not need to swap back into the same physical address. Modern OS use a modified version of standard swapping, where swaps only happen when free memory is extremely low.



# Chapter 10

## Definitions

