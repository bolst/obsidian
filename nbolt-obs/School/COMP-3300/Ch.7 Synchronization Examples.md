[[_Operating System Fundamentals]]

# Classical Synchronization Problems

## Bounded-buffer problem

The bounded-buffer problem has
- `n` buffers, each holding one item
- Semaphore `mutex` initialized to 1
- Semaphore `full` initialized to 0
- Semaphore `empty` initialized to `n`

```c
// producer
while (true) {
		// produce an item in next_produced

	// wait until there is at least one empty slot in buffer
	// and decrement empty since we will insert to one slot
	wait(empty);

	// lock buffer so that consumer cant access until we are done
	wait(mutex);
	
		// add next_produced to buffer

	// lock is released
	signal(mutex);

	// full is incremented because we just filled slot in buffer
	signal(full);
}

// consumer
while (true) {
	// wait until there is at least one full slot in buffer
	// decrement since we will be occupying after completion
	wait(full);

	// acquire lock on buffer
	wait(mutex);
	
		// remove item from buffer to next_consumed

	// release lock
	signal(mutex);

	// increment since we just removed data from the slot
	signal(empty);
	
		// consume item in next_consumed
}
```



## Readers-writers problem

The readers-writers problem has a dataset shared among a number of concurrent processes
- **Readers** only read the dataset; they do not perform any updates
- **Writers** can both read and write

The problem is, how do we allow multiple readers at the same time? **We only allow one single writer to access the shared data at the same time**. There are several variations of how readers/writers are considered - they all involve some form of priorities.

```c
semaphore rw_mutex = 1;
semaphore mutex = 1;
int read_count = 0;

// writer
while (true) {
	wait(rw_mutex);
		// writing is performed
	signal(rw_mutex);
}

// reader
while (true) {
	wait(mutex);
	read_count++;
	if (read_count == 1) {
		wait(rw_mutex);
		signal(mutex);
	}

	// reading is performed

	wait(mutex);
	read_count--;
	if (read_count == 0)
		signal(rw_mutex);
	signal(mutex);
}
```

This solution can result in a situation where a writer process never writes. It is referred to as the "first reader-writer" problem.

The "second reader-writer" problem is a variation on the first reader-write problem that states:
- Once a writer is ready to write, no "newly arrived reader" is allowed to read

Both the first and second can result in starvation, leading to even more variations. This problem is solved on some systems through the kernel providing reader-writer locks.


## Dining philosophers problem

$N$ philosophers sit at a round table with a bowl of rice in the middle.
- They spend their lives alternating thinking and eating
- They do not interact with their neighbors
- Occasionally they try to pick up 2 chopsticks (one at a time) to eat from the bowl
	- They need both to eat, and release both when done
- In the case of 5 philosophers, the shared data is
	- The bowl of rice (dataset)
	- Semaphore `chopstick[5]` initialized to 1

### Semaphore solution

```c
while (true) {
	wait(chopstick[i]);
	wait(chopstick[ (i+1) % 5 ]);
	
	// eat
	
	signal(chopstick[i]);
	signal(chopstick[ (i+1) % 5]);
	
	// think
}
```


### Monitor solution

```c
monitor DiningPhilosophers
{
	enum { THINKING, HUNGRY, EATING } state[5];
	condition self[5];
	
	void pickup (int i) {
		state[i] = HUNGRY;
		test(i);
		if (state[i] != EATING) self[i].wait;
	}
	
	void putdown (int i) {
		state[i] = THINKING;
		// test left/right neighbors
		test((i+4) % 5);
		test((i+1) % 5);
	}
	
	void test(int i)
	{
		if (state[(i+4) % 5] != EATING)
			&& state[i] == HUNGRY
			&& state[(i+1) % 5] != EATING) {
				state[i] = EATING;
				self[i].signal();
			}
	}
	
	initialization_code() {
		for (int i = 0; i < 5; i++) {
			state[i] = THINKING;
		}
	}
}
```

Each philosopher invokes the `pickup()` and `putdown()` in the following sequence

```c
DiningPhilosophers.pickup(i);
// eating
DiningPhilosophers.putdown(i);
```

There is no deadlock here, but starvation is possible.


# Synchronization within the kernel

## Synchronization in Windows

Windows uses interrupt masks to protect access to global resources on uniprocessor systems. It uses **spinlocks** on multiprocessor systems.

Provides **dispatcher objects** which act as mutex, semaphores, events, etc. Dispatcher objects are either signalled-state or non-signalled-state.

## Synchronization in Linux

Linux provides semaphores, atomic integers, spinlocks, and reader-writer versions of both. On a single-CPU system, spinlocks are replaced by enabling and disabling kernel preemption.

`atomic_t` is the type for an atomic integer. Consider
```c
atomic_t counter;
int value;
```

| Atomic Operation                 | Effect          |
| -------------------------------- | --------------- |
| `atomic_set(&counter, 5);`       | `counter = 5`   |
| `atomic_add(10, &counter);`      | `counter += 10` |
| `atomic_sub(4, &counter);`       | `counter -= 4`  |
| `atomic_inc(&counter);`          | `counter += 1`  |
| `value = atomic_read(&counter);` | `value = 12`    |


# POSIX synchronization

The POSIX API provides mutex locks, semaphores, and condition variables.

```c
#include <pthread.h>

pthread_mutex_t mutex;

// create/initialize mutex lock
pthread_mutex_init(&mutex, NULL);

// acquire lock
pthread_mutex_lock(&mutex);

// critical section

// release lock
pthread_mutex_unlock(&mutex);
```


For semaphores, it provides two named versions - **named** and **unnamed**. Named semaphores can be used by unrelated processes, unnamed cannot.

```c
#include <semaphore.h>

// named semaphores
// -----------------------------
// create semaphore and initialize to 1
sem_t *sem;
sem = sem_open("SEM", O_CREAT, 0666, 1);

// acquire semaphore
sem_wait(sem);

// critical section

// release semaphore
sem_post(sem);

// unnamed semaphores
// -----------------------------
// create semaphore and initialize it to 1
sem_t sem;
sem_init(&sem, 0, 1);

// acquire semaphore
sem_wait(&sem);

// critical section

// release semaphore
sem_post(&sem);
```

Since POSIX is typically used in C/C++ and these languages do not provide a monitor, POSIX condition variables are associated with a POSIX mutex lock to provide mutual exclusion.

```c
pthread_mutex_t mutex;
pthread_cond_t cond_var;

pthread_mutex_init(&mutex, NULL);
pthread_cond_init(&cond_var, NULL);

// thread waiting for a == b
pthread_mutex_lock(&mutex);
while (a != b) pthread_cond_wait(&cond_var, &mutex);

pthread_mutex_unlock(&mutex);
```


## Synchronization in Java


# Alternative approaches

## Transactional memory

Consider a function `update()` that must be called atomically. One option is to use mutex locks:

```c
void update() {
	acquire();
	// modify shared data
	release();
}
```

> [!definition|*] Memory transaction
> A **memory transaction** is a sequence of read-write operations to memory that are performed atomically. A transaction can be completed by adding `atomic{S}` which ensures statements in `S` are executed atomically.

```c
void update() {
	atomic {
		// modify shared data
	}
}
```


## OpenMP

OpenMP is a set of compiler directives and API that supports parallel programming.

```c
void update(int value) {
	# pragma omp critical
	{
		count += value;
	}
}
```

Code in `#pragma omp critical` is treated as the critical section and is performed atomically.


## Functional programming

Functional programming languages offer a different paradigm than procedural languages in that they do not maintain state. Variables are treated as immutable and cannot change state once they have an assigned value.