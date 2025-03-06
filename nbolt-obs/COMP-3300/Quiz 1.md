[[_Operating System Fundamentals]]

## Part 1. Consider the following code

### (a)

```c
#include <stdio.h>
#include <unistd.h>

int main() {
	printf("Hello\n");
	fork();
	fork();
	printf("Hello\n");
	return 0;
}
```

1. How many processes will be created? (4)
2. What will the total number of "Hello" outputs printed? (5)
3. Is the output order guaranteed to be the same every time the program runs? (No)
4. If the first `fork()` returns `123` to the parent, what is the PID of the newly created child? (123)
5. What will be the total number of hello outputs if we add `wait(NULL);` after each `fork()` call? (5)


### (b)

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
	int p = fork();
	if (p > 0) {
		printf("Hi there\n");
	} else {
		sleep(1);
		p = p + 5;
		printf("%d\n", p);
	}
	wait(NULL);
	printf("%d\n", p);
	return 0;
}
```

1. What is the output? Assume the child PID is 100.
>Hi there
>5
>5
>100

2. How many processes will be created? (2)
3. If we assign `p` to a pointer and then modify it, will it affect the program's output? (No)
4. What happens to the process if `wait(NULL)` is not used (zombie or orphan)? (orphan).


## Part 2. True or false

1. When a new thread is created, the OS allocates separate memory for it (false)
2. A multi-core system always gurantees parallel execution of an application (false)
3. In a PCB, the Program Counter is responsible for counting the timer of the real-clock (false)
4. A system program is not part of the kernel (true)
5. A system call is a software generated interrupt (true)
6. Device driver is a hardware component (false)
7. Device controller has a local buffer (true)
8. A system call changes the mode from kernel to user mode (false)
9. The OS manages resource allocation (true)
10. We should always keep policy separate from mechanism (true)


## Part 3. Choose the correct option

1. Which is NOT a multithreading model? (2)
	1. Many to many
	2. One to many
	3. One to one
	4. Many to one

2. Any dynamically allocated values are stored in which of the following location? (3)
	1. Data
	2. Stack
	3. Heap
	4. Text

3. Which of the following is not part of the process state? (3)
	1. New
	2. Ready
	3. Sleeping
	4. Running

4. Which of the following is not a benefit of the microkernel structure (4)
	1. Easy to extend
	2. Easy to port
	3. More relaible
	4. Less performance overhead

5. Which of the following is not a primary function of the OS? (3)
	1. File system management
	2. Process scheduling
	3. Code compilation
	4. None of the above

