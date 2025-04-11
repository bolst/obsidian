[[_Operating System Fundamentals]]

# The critical-section problem

Consider a system of $n$ processes $p_{0},p_{1}, \dots, p_{n-1}$. Each process has a critical section segment of code (i.e., where common variables are changed, tables are updated, etc.). The **critical section problem** is a protocol that decides what processes can be in their critical section at a single time.

A general structure of a process would be
```c
while (true) {
	// entry section
		// critical section
	// exit section
	// remainder section
}
```


## Requirements for a solution

1. **Mutual exclusion** - if a process is executing in its critical section, then no other processes can be executing in their critical sections
2. **Progress** - if no process is executing in its critical section and another process wants to enter their critical section, then the selection of processes that will enter the critical section next cannot be postponed indefinitely
3. **Bounded waiting** - a bound must exist on the number of times that other processes are allowed to enter their critical sections after a process requests to enter its critical section


# Peterson's Solution

Peterson's solution is a **two process solution**. The processes share two variables:
- `int turn` - indicates whose turn it is to enter the critical section
- `bool flag[2]` - indicates if a process is ready to enter the critical section

```c
while (true) {
	flag[i] = true;
	turn = j;
	while (flag[j] && turn == j);
	/* critical section */
	flag[i] = false;
	// remainder
}
```

Although it is useful for demonstrating an algorithm, Peterson's solution is not guaranteed to work on modern architectures. However, understanding why it **won't** work is useful for understanding race conditions.

To ensure Peterson's solution will work on modern computer architecture, we must use [[Ch.6 Synchronization Tools#Memory barriers|memory barriers]]

# Hardware support for synchronization 

## Memory barriers

**Memory models** describe the interactions of threads through memory and their shared use of data. They can be either:
- **Strongly ordered** - where a memory modification of one processor is immediately visible to all other processors
- **Weakly ordered** - where a memory modification of one processor may not be immediately visible to all other processors

> [!definition|*] Memory barrier
> A **memory barrier** is an instruction that forces any change in memory to be propagated (made visible) to all other processors.

When a memory barrier instruction is performed, the system ensures all load/store operations are completed before any subsequent ones are performed (essentially like the `await` operator in C#).

## Hardware instructions
### Test and set

This is one of the two instructions that allow us to either *test and modify* the content of data, or to *swap* the contents of two words uninterruptedly.

```c
bool test_and_set(bool *target) {
	bool rv = *target;
	*target = true;
	return rv;
}
```

- Executes atomically
- Returns the original value of the passed parameter
- Sets the new value of the passed parameter to `true`

For example, say we have a shared bool variable `lock` initialized to `false`. An implementation of a solution would be

```c
while (true) {
	while (test_and_set(&lock));
	// critical section
	lock = false;
	// remainder section
}
```

### Compare and swap

This is the other of the two instructions that allow us to either *test and modify* the content of data, or to *swap* the contents of two words uninterruptedly.

```c
int compare_and_swap(int *value, int expected, int new_value) {
	int temp = *value;
	if (*value == expected)
		*value = new_value;
	return temp;
}
```

- Executes atomically
- Returns the original value of passed parameter `value`
- Sets the variable `value` to `new_value` iff `*value == expected`

For example, say we have a shared integer `lock` initialized to 0. An implementation of a solution would be

```c
while (true) {
	while (compare_and_swap(&lock, 0, 1) != 0);
	// critical section
	lock = 0;
	// remainder section
}
```


## Atomic variables

Typically, instructions such as [[Ch.6 Synchronization Tools#Compare and swap|compare and swap]] are used as building blocks for other synchronization tools. One tool is an **atomic variable** that provides atomic (i.e., uninterruptible) updates on basic data types such as integers and booleans.

> [!example|*]
> Let `sequence` be an atomic variable and `increment()` be an operation on `sequence`. The command `increment(&sequence)` ensures `sequence` is incremented without interruption.

```c
void increment(atomic_int *v) {
	int temp;
	do {
		temp = *v;
	} while(temp != compare_and_swap(v, temp, temp+1));
}
```

# Mutex and spin locks

The solutions outlined in [[Ch.6 Synchronization Tools#Hardware instructions|hardware instructions]] are complicated and generally inaccessible to application programmers. OS designers build software tools to solve the critical section problem - the simplest is a **mutex lock**.

> [!definition|*] Mutex lock
> A **mutex lock** (mutually exclusive flag) is a boolean variable indicating if a lock is available or not.

Critical sections are protected by using `acquire()` and then `release()` on a lock. Calls to these functions must be **atomic**.

This solution requires **busy waiting** (i.e., a process waiting and continuously checking a condition for if it can proceed) - this lock is called a **spin lock**.

```c
while (true) {
	// acquire lock
		// critical section
	// release lock
	// remainder section
}
```


# Semaphores

> [!definition|*] Semaphore
> A **semaphore** $S$ is a synchronization tool that provides more sophisticated ways (over Mutex locks) for processes to synchronize their activities. 

The semaphore $S$ (an integer) can only be accessed via two indivisible operations: `wait()` and `signal()`.

```c
wait(S) {
	while (S <= 0); // busy wait
	S--;
}

signal(S) {
	S++;
}
```

Semaphores can either be (1) **counting semaphores** where it is stored as an integer value, or (2) a **binary semaphore** where the integer value can only be 0 or 1 (this is the same as [[Ch.6 Synchronization Tools#Mutex and spin locks|mutex lock]]). We can solve various synchronization problems with semaphores.

We have to guarantee that no two processes can execute `wait()` and `signal()` on the same semaphore at the same time, so the implementation becomes the critical section problem with both of these placed in it.

This is not a good solution because applications may spend lots of time in critical sections.

### Semaphore implementation with no busy waiting

With each semaphore there is an associated waiting queue. Each entry has two items: a value (integer) and a pointer to the next record in the list. We also have two operations
- `block` - places the process invoking the operation on the appropriate waiting queue
- `wakeup` - remove one of the processes in the waiting queue and place it in the ready queue

```c
wait (semaphore *S) {
	S->value--;
	if (S->value < 0) {
		block(); // add process to S->list
	}
}

signal (semaphore *S) {
	S->value++;
	if (S->value <= 0) {
		wakeup(P); // remove process P from S->list
	}
}
```


# Monitors

> [!definition|*] Monitor
> A monitor is a high-level abstraction that provides a convenient and effective mechanism for process synchronization. Only one process may be active in a monitor at a time.


## Condition variables

Two operations are allowed on a condition variable
- `x.wait()` - a process that invokes the operation is suspended until `x.signal()`
- `x.signal()` - resumes one of the processes that invoked `x.wait()`

For example, consider $P_{1},P_{2}$ that need to execute statements $S_{1},S_{2}$ and the requirement that $S_{1}$ happens before $S_{2}$.
- Create a monitor with two procedures $F_{1},F_{2}$ that are invoked by $P_{1},P_{2}$ respectively
- Create condition variable `x` and boolean variable `done`
```c
F1 {
	S1;
	done = true;
	x.signal();
}
F2 {
	 if (done == false)
		 x.wait();
	S2;
}
```


## Monitor implementation with semaphores

```c
semaphore mutex = 1;
semaphore next = 0;
int next_count = 0;

// replace every function P with
wait(mutex);
	// body of P
if (next_count > 0)
	signal(next);
else
	signal(mutex);
```

This ensures mutual exclusion within the monitor.

## Resuming processes in a monitor

If several processes are queued on a condition variable `x` and `x.signal()` is executed, which process should be resumed? We use the conditional-wait construct of the form `x.wait(c)` where
- `c` is an integer (the priority number)
- The process with the lowest number (highest priority) is scheduled next


# Liveness issues

Processes may have to wait indefinitely while trying to acquire a synchronization tool such as a mutex lock or semaphore.

> [!definition|*] Liveness
> **Liveness** refers to a set of properties a system must satisfy to ensure processes make progress (e.g., indefinite waiting is an example of a liveness failure).

> [!definition|*] Deadlock
> A **deadlock** is when two or more processes are waiting indefinitely for an event that can be caused by only one of the waiting processes.

For example, $P_{0}$ is waiting for a signal from $P_{1}$, but in order for $P_{1}$ to send the signal, it needs a signal from $P_{0}$. These signals will never be executed so $P_{0},P_{1}$ are **deadlocked**.

There are other forms of deadlocks as well:

> [!definition|*] Starvation
> **Starvation** is indefinite blocking - a process may never be removed from the semaphore queue in which it is suspended.

> [!definition|*] Priority inversion
> **Priority inversion** is a scheduling problem when lower-priority process holds a lock needed by a higher-priority process.
