
# Part 1

## Question 1.1

Consider the following code

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

1. How many processes will be created?
   
2. How many times will "Hello" be outputted?
   
3. Is the output order guaranteed to be the same every time the program runs?
   
4. If the first `fork()` returns 123 to the parent, what is the PID of the newly created child?
   
5. What will be the total number of "Hello" outputs if we add `wait(NULL)` after each `fork()` call?


## Question 1.2

Consider the following code

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
	int p = fork();
	if (p > 0) {
		printf("Hi There!\n");
	}
	else {
		sleep(1);
		p += 5;
		printf("%d\n", p);
	}
	
	wait(NULL);
	printf("%d\n", p);
	return 0;
}
```

1. Write the output, assuming the PID of the child is 100.
   
   
   
   
2. How many processes will be created?
   
3. If we assign the `p`-value to a pointer and modify it, will it affect the program's output?
   
4. What happens to the process if `wait(NULL)` is not used (orphan or zombie)?


# Part 2 (T/F)

1. When a new thread is created, the operating system allocates separate memory for it.
2. A multi-core system always guarantees parallel execution of an application.
3. In a Process Control Block (PCB), the Program Counter is responsible for counting the timer of the real-time clock.
4. A system program is not a part of the kernel.
5. A system call is a software generated interrupt.
6. Device driver is a hardware component.
7. Device controller has a local buffer.
8. A system call changes the mode from kernel to user mode.
9. The OS manages resource allocation.
10. We should always keep policy separate from mechanism.


# Part 3 (MC)

1. Which of the following is NOT a multithreading model?
	1. Many to many
	2. One to many
	3. One to one
	4. Many to one
2. Any dynamically allocated values are stored in which of the following location?
	1. Data
	2. Stack
	3. Heap
	4. Text
3. Which is NOT a part of the process state
	1. New
	2. Ready
	3. Sleeping
	4. Running
4. Which of the following is NOT a benefit of Microkernel Structure
	1. Easy to extend
	2. Easy to port
	3. More reliable
	4. Less performance overhead
5. Which of the following is NOT a primary function of an OS?
	1. File system management
	2. Process scheduling
	3. Code compilation
	4. None of the above