# Readers_Writers_Problem_Starve-Free_pseudocode

JATIN GROVER, 21114043

BASIC SEMAPHORE IMPLEMENTATION:

```

struct Semaphore 

	int value
	Queue<process> lst //CONTAINS ALL THE BLOCKED PROCESSES

P(Semaphore s)
	s.value = s.value - 1;
	if (s.value < 0) 
		lst.push(p);
		block();
	else
		return;

V(Semaphore s)
	s.value = s.value + 1;
	if (s.value <= 0)
		Process p = lst.pop();
		wakeup(p);
	else
		return;

```

INITIALIZATION OF SEMAPHORES:
```

semaphore* rd_mutex;

semaphore* check_mutex; 

semaphore* wrt_mutex;

int cnt = 0; 

```
The `check_mutex` semaphore is required to be obained before the reader or the writer accesses the `wrt_mutex` or before anyone (any reader) enters the critcal section directly. This solves the problem of starvation as if readers keep coming one after another, then this won't starve the writers as it used to above. Here, if a writer comes in between two readers, and even if some readers are still present in the critical section, the next reader if it comes after a writer, the writer would have already acquired the `check_mutex` and thus the reader can't acquire it and thus after the existing readers exit the critical section, the writer that was waiting would be at front of the `lst` for the `wrt_mutex` and thus acquires it. Now the writer can enter the critical section and the same process would repeat. Thus readers and writers are now at equal priority and none would starve. Doing this also preserves the advantage of readers not having to acquire the `wrt_mutex` everytime, when some other reader is already present. Thus an effiecient and starve-free solution to the Reader-Writer problem.





Refrences:
https://www.geeksforgeeks.org/semaphores-in-process-synchronization/
