[[_Operating System Fundamentals]]


# System Model

The system model describes a system consisting of resources with types $R_{1},R_{2},\dots,R_{m}$ (e.g., CPU cycles, memory space, I/O devices).

Each resource type $R_{i}$ has $W_{i}$ instances.

Each process utilizes a resource as follows
1. request
2. use
3. release


# Deadlock Characterization

Deadlocks can arise if four conditions hold simultaneously
- **Mutual exclusion:** only one thread at a time can use a resource
- **Hold and wait:** a thread holding at least one resource is waiting to acquire additional resources held by other threads
- **No preemption:** a resource can be released only voluntarily by the thread holding it, after that thread has completed its task
- **Circular wait:** there exists a set $\{ T_{0},T_{1},\dots T_{n} \}$ of waiting threads such that $T_{n}$ is waiting for a resource that is held by $T_{n+1}$ (and $T_{n}$ is waiting for $T_{0}$).


# Resource-allocation graph

> [!definition|*] Resource-allocation graph
> A **resource-allocation graph** consists of a set of vertices $V$ and edges $E$ and is partitioned into two types
> - $T = \{ T_{1},T_{2},\dots,T_{n} \}$ consisting of all threads in the system
> - $R = \{ R_{1},R_{2},\dots, R_{m} \}$ consisting of all resource types in the system
> 
> The graph consists of **request edges** $T_{i} \to R_{j}$ and **assignment edges** $R_{j} \to T_{i}$.

> [!example|*]
> - One instance of $R_{1}$
> - Two instances of $R_{2}$
> - One instance of $R_{3}$
> - Three instances of $R_{4}$
> - $T_{1}$ holds one instance of $R_{2}$ and is waiting for an instance of $R_{1}$
> - $T_{2}$ holds one instance of $R_{1}$, one instance of $R_{2}$, and is waiting for an instance of $R_{3}$
> - $T_{3}$ holds one instance of $R_{3}$

**With deadlock**
![[rag-deadlock.png|300x400]]

**Without deadlock**
![[rag-nodeadlock.png|300x400]]

`\end{proof}`

- graph contains no cycles $\implies$ no deadlock
- graph contains cycle $\implies$
	- if only one instance per $R_{i}$, deadlock
	- if several instances per $R_{i}$, possible deadlock


# Method for handling deadlocks

We ensure a system will **never** enter a deadlock state through [[Ch.8 Deadlocks#Deadlock prevention|deadlock prevention]] and [[#Deadlock avoidance|deadlock avoidance]]. Or, we allow the system to enter a deadlock and then recover from it.

## Deadlock prevention

We invalidate one of the four necessary conditions for the deadlock:
- **Mutual exclusion:** not required for sharable resources and must hold for non-sharable resources
- **Hold and wait:** must guarantee that whenever a thread requests a resource, it does not hold any other resources
	- Require threads to request and be allocated its resources before it begins executing (or when it has no resources)
	- Low resource utilization so starvation is possible
- **No preemption:** if a process with resources requests another that cannot be immediately allocated, all resources being held are released
	- Preempted resources are added to the list of resources for which the thread is waiting
	- Thread will be restarted iff it can regain its old resources and the new ones it wants
- **Circular wait:** Impose a total ordering of all resource types, and require that each thread requests resources in an increasing order of enumeration

Invalidating the **circular wait** is most common. We simply assign each resource a unique number, and each resource must be acquired in order.

For example, if `first_mutex = 1` and `second_mutex = 5`, the code below could not be written
```c
pthread_mutex_lock(&second_mutex);
pthread_mutex_lock(&first_mutex);
// do work
pthread_mutex_unlock(&first_mutex);
pthread_mutex_unlock(&second_mutex);
```

## Deadlock avoidance

This requires that a system has some additional information available. The simplest and most useful model requires that each thread declares the **maximum number** of resources of each type that it may need.

The deadlock-avoidance algorithm dynamically examines the resource-allocation state to ensure there can never be a circular-wait condition.

(safe states, Banker's algorithm)

# Deadlock detection
(detection algorithm, recovery from deadlock, resource preemption)