
Consider the following set of processes.

| Process | Priority | Burst Time |
| ------- | -------- | ---------- |
| P1      | 1        | 10         |
| P2      | 1        | 4          |
| P3      | 2        | 5          |
| P4      | 3        | 4          |
| P5      | 2        | 8          |

1. Draw the Gantt Chart using Priority Scheduling with Round Robin ($q=4$).

<br>
<br>
<br>


2. Write the waiting and turnaround time for each process using the same algorithm.

| Process | Waiting Time | Turnaround Time |
| ------- | ------------ | --------------- |
| P1      |              |                 |
| P2      |              |                 |
| P3      |              |                 |
| P4      |              |                 |
| P5      |              |                 |


2. Consider the following synchronization code using semaphores for two threads, $T_{1}$ and $T_{2}$, running concurrently on the same core (context switching can occur at any point).
	1. Does this code have a race condition?
	2. Does this code possibly lead to a deadlock?
	3. Modify the problematic lines to ensure correct synchronization (initially, `S1=1` and `S2=2`).

T1:
```c
wait(S1);
wait(S2);
// Critical section
signal(S2);
signal(S1);
```

T2:
```c
wait(S2);
wait(S1);
// Critical section
signal(S1);
signal(S2);
```


3. Identify whether the following are true/false:
	1. The dining philosophers’ problem is a classic example of a race condition.
	2. Atomic operations can help prevent race conditions.
	3. If a system has a circular wait, it is guaranteed to be in a deadlock state.
	4. Aging cannot be implemented using a multilevel feedback queue.
	5. Soft affinity guarantees that a thread will always execute on the same CPU.
	6. In the readers-writers problem, multiple readers can access the resource simultaneously if no writer is present.
	7. A single mutex lock is sufficient to solve the producer-consumer problem with multiple producers and consumers.
	8. In the Resource Allocation Graph, a claim edge $T_{i} \to R_{j}$ indicates that resource $R_{j}$ is currently being held by thread $T_{j}$.


4. Choose the correct option from the following:
	1. What does a producer do when the buffer is full in the producer-consumer problem?
		1. Discards new items
		2. Forces the consumer to remove an item
		3. Waits until space is available
		4. Continues writing, overwriting old data
	2. What is spinlock in synchronization?
		1. A process actively checks for condition using a loop
		2. A process terminates immediately when a resource is unavailable
		3. A process continues execution without waiting for a resource
		4. All of the above


5. Draw the RAG using the given information

- Represent threads as circles
- Represent resources as squares
- Use edges in correct direction to indicate allocation/requests
- The resource allocation and request details are as follows

| Thread | Currently Holding | Requesting |
| ------ | ----------------- | ---------- |
| T1     | R1                | R2         |
| T2     | R2                | None       |
| T3     | None              | R2         |
| T4     | None              | R1         |











Are there any deadlocks? If yes, write the processes involved.