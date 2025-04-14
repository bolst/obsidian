

Consider the following code

```c
#include <stdio.h>
#include <unistd.h>

int main() {
	printf("Start\n");
	pid_t pid1 = fork();
	printf("Running\n");
	pid_t pid2 = fork();
	printf("End\n");
	return 0;
}
```

1. What will be the total number of "End" outputs?
   
2. Modify the code so that the topmost parent only prints "Start" and never prints "Running" or "End".






Consider the following code

```c
#include <stdio.h>
#include <unistd.h>

int main() {
	int x = 5;
	if (fork() == 0) {
		x = 10;
		printf("x = %d\n", x);
	} else {
		printf("x = %d\n", x);
	}
	return 0;
}
```

1. Write the output of the child process
   
2. Write the output of the parent process



Consider the following code

```java
import java.util.concurrent.*;
class Test implements Runnable {
	public void run() {
		System.out.println("Executing Task:");
	}
}

public class Main {
	public static void main(String[] args) {
		ExecutorService executor = Executors.newFixedThreadPool(4);
		
		for (int i = 1; i <= 6; i++) {
			executor.execute(new Test());
		}
		
		executor.shutdown();
	}
}
```

1. How many threads are created in total?
   
2. What happens if `shutdown()` is not called?



Consider the following code?

```c
#include <stdio.h>
#include <pthread.h>

void* print_message(void* arg) {
	printf("Thread Running\n");
	return NULL;
}

int main() {
	pthread_t thread, task;
	pthread_create(&thread, NULL, print_message, NULL);
	pthread_create(&task, NULL, print_message, NULL);
	pthread_join(thread,NULL);
	pthread_join(task,NULL);
	printf("Main Function\n");
	return 0;
}
```

1. Write the output, assuming all threads execute successfully



2. Would the output pattern (possibly) change if we do not use the `pthread_join()` function call?



Identify true/false for the following:
1. Threads within the same process share the same address space and resources.
2. In a multiprogramming system, multiple programs execute at exactly the same time on a single processor.
3. Kernel mode allows unrestricted access to all machine instructions.
4. In a monolithic OS structure, all OS components are integrated into a single large program.
5. A system call allows user programs to directly manipulate hardware without OS intervention.


Choose the correct option in each of the following:
1. Which OS structure consists of multiple layers, each building upon the previous one?
	1. Monolithic
	2. Layer
	3. Microkernel
	4. Virtual machine
2. A system call is used to:
	1. Execute a program without an OS
	2. Request services from the OS
	3. Convert high-level code into machine code
	4. Communicate with external hardware
3. Which of the following is NOT a user goal in OS design?
	1. Easy to learn
	2. Reliable
	3. Easy to implement
	4. Convenient to use
4. Which of the following is NOT considered a user interface for an OS?
	1. Command-line interface
	2. Touchscreen interface
	3. Web browser interface
	4. Batch
5. The role of system calls in an OS is to
	1. Allow direct execution of user applications
	2. Provide an interface to request OS services
	3. Convert machine language to assembly language
	4. Control the screen resolution of the display


Consider the following processes

| Process | Arrival Time | Burst Time |
| ------- | ------------ | ---------- |
| P1      | 0            | 10         |
| P2      | 1            | 4          |
| P3      | 2            | 5          |
| P4      | 3            | 2          |
| P5      | 4            | 8          |

1. Draw a Gantt Chart using FCFS



2. Write the waiting time for each process using FCFS
	1. P1:
	2. P2:
	3. P3:
	4. P4:
	5. P5:

3. Draw a Gantt Chart using SJF (non-preemptive)
   
   
4. Write the waiting time for each process using SJF (non-preemptive)
	1. P1:
	2. P2:
	3. P3:
	4. P4:
	5. P5:

5. Draw a Gantt Chart using RR with $q=5$


6. Write the turnaround time for each process using RR with $q=5$
	1. P1:
	2. P2:
	3. P3:
	4. P4:
	5. P5:


7. Which has a lower average waiting time out of FCFS and SJF?
   
8. Which of the algorithms is prone to the Convoy Effect?
   
9. Which has a better average turnaround time out of SJF and RR?