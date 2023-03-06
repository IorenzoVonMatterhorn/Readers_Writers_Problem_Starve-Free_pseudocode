# Readers_Writers_Problem_Starve-Free_pseudocode


```
//BASIC SEMAPHORE IMPLEMENTATION:
struct Semaphore 

	int value
	Queue<process> q //CONTAINS ALL THE BLOCKED PROCESSES

P(Semaphore s)
	s.value = s.value - 1;
	if (s.value < 0) 
		q.push(p);
		block();
	else
		return;

V(Semaphore s)
	s.value = s.value + 1;
	if (s.value <= 0)
		Process p = q.pop();
		wakeup(p);
	else
		return;

```


Refrences:
https://www.geeksforgeeks.org/semaphores-in-process-synchronization/
