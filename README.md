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


READER'S END:

```
while(1):
    wait(check_mutex, pid)
    // it stalls if the process pid is blocked by this semaphore
    wait(rd_mutex, pid)

    ++cnt;

    if(cnt == 1)    
        wait(wrt_mutex, pid)
    
        
    signal(rd_mutex) // now readers that have acquired the entry_mutex can access the cnt variable

    signal(check_mutex)
    //  This check_mutex semaphore can be acquired by either a reader or writer, whichever arrives first

` ****** CRITICAL SECTION ******`

    wait(rd_mutex, pid)
    --cnt
    signal(rd_mutex)

    if(rd_count == 0)
        signal(wrt_mutex)

```
WRITERS' END:
```
while(1):
    wait(check_mutex, processId)
    //  the writer waits for the check mutex first
    //  it can acquire this mutex even if a reader is already present in critical section as long as if the reader has freed the mutex

    wait(wrt_mutex, processId)
    // it will wait till the writer process if being blocked by this process
    //  once free it will acquire the wrt_mutex and enter the critical section

    signal(check_mutex)
    //  once the writer is ready to enter the critical section, it frees the check_mutex
    //  this can now be acquired by any reader or writer which came first
    
        ` ***** CRITICAL SECTION ***** `
    signal(wrt_mutex)
//wrt_mutex is freed back by the writer

```


Refrences:
https://www.geeksforgeeks.org/semaphores-in-process-synchronization/
https://arxiv.org/abs/1309.4507
https://www.os-book.com/OS9/slide-dir/index.html
