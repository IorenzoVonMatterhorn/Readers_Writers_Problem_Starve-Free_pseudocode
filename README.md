
## JATIN GROVER, 21114043

# STARVE-FREE SOLUTION TO READER'S WRITER'S PROBLEM

## BASIC SEMAPHORE IMPLEMENTATION:

```

struct Semaphore 

	int value
	Queue<process> lst //CONTAINS ALL THE BLOCKED PROCESSES

P(Semaphore s)
	s.value = s.value - 1
	if (s.value < 0) 
		lst.push(p)
		block()
	else
		return

V(Semaphore s)
	s.value = s.value + 1
	if (s.value <= 0)
		Process p = lst.pop()
		wakeup(p)
	else
		return

```

## INITIALIZATION OF SEMAPHORES:
```

semaphore* rd_mutex;

semaphore* check_mutex; 

semaphore* wrt_mutex;

int cnt = 0; 

```
# PSEUDOCODE:
The `check_mutex` semaphore is required to be obained before the reader or the writer accesses the `wrt_mutex` or before anyone (any reader) enters the critcal section directly. This solves the problem of starvation as if readers keep coming one after another, then this won't starve the writers as it used to above. Here, if a writer comes in between two readers, and even if some readers are still present in the critical section, the next reader if it comes after a writer, the writer would have already acquired the `check_mutex` and thus the reader can't acquire it and thus after the existing readers exit the critical section, the writer that was waiting would be at front of the `lst` for the `wrt_mutex` and thus acquires it. Now the writer can enter the critical section and the same process would repeat. Thus readers and writers are now at equal priority and none would starve. Doing this also preserves the advantage of readers not having to acquire the `wrt_mutex` everytime, when some other reader is already present. Thus an effiecient and starve-free solution to the Reader-Writer problem.


## READER'S END:

```
while(1):
    V(check_mutex, pid)
    // it stalls if the process pid is blocked by this semaphore
    V(rd_mutex, pid)

    ++cnt;

    if(cnt == 1)    
        V(wrt_mutex, pid)
    
        
    P(rd_mutex) // now readers that have acquired the entry_mutex can access the cnt variable

    P(check_mutex)
    //  This check_mutex semaphore can be acquired by either a reader or writer, whichever arrives first

` ****** CRITICAL SECTION ******`

    V(rd_mutex, pid)
    --cnt
    P(rd_mutex)

    if(rd_count == 0)
        P(wrt_mutex)

```
## WRITERS' END:
```
while(1):
    V(check_mutex, processId)
    //  the writer waits for the check mutex first
    //  it can acquire this mutex even if a reader is already present in critical section as long as if the reader has freed the mutex

    V(wrt_mutex, processId)
    // it will wait till the writer process if being blocked by this process
    //  once free it will acquire the wrt_mutex and enter the critical section

    P(check_mutex)
    //  once the writer is ready to enter the critical section, it frees the check_mutex
    //  this can now be acquired by any reader or writer which came first
    
        ` ***** CRITICAL SECTION ***** `
    P(wrt_mutex)
//wrt_mutex is freed back by the writer

```

# Evaluating the Solution based on various criteria:

## Mutual Exclusion:
  only a single writer process or multiple reader processes and no writer processes might be present in the critical section at the same moment for Mutual Exclusion to hold. It is ensured by the check_mutex semaphore which is not biased either to the reader or the writer processes and treats them equally. Entry in the process is decided on a First Come First Serve basis. Entry is given to a writer only when the critical section is free and to readers additionally when other readers are present. Thus, mutual exclusion is satisfied.

## Progress:
This pseudo-code has left no loophole in its implementation to allow a deadlock to occur. Also, the readers and writers take finite time with semaphores being released, every time processes are done with their execution in the critical section, giving chance for others to enter the critical section. Thus, the progress criterion is satisfied.

## Bounded Waiting:
Originally if readers came one after another, writers would starve, but now due to the entry_mutex semaphore, all get queued up and are released one after the other. Same is the case of the readers, that get a chance to enter the crtical section in a group whenever they reach the front of the queue. Thus, neither reader nor writer processes have to wait indefinitely, satisfying the bounded waiting criteria.

Since, all of the requirements are properly met, we can simply conlude that the solution in completely starve-free!



## Refrences:
> https://www.geeksforgeeks.org/semaphores-in-process-synchronization/
> https://arxiv.org/abs/1309.4507
> https://www.os-book.com/OS9/slide-dir/index.html
