# Readers_Writers_Problem_Starve-Free_pseudocode




           
  
//Basic Semaphore Implementation:
struct semaphore {
    int value;
    blockedQueue* blocked_queue = new blockedQueue();
    
    semaphore(int n) {
        value = n;
    }

};

void wakeUp(process* p) {
    p->state = true;
}
//  in reality a system call is used instead of this function
//  i have used this function as an analogy of the syscall

void signal(semaphore* sem) {
    ++sem->value;

    if(sem->value <= 0) {
        process* nextProcess = sem->blocked_queue->pop();
        wakeUp(nextProcess);    
        //  wakeUp the next process such that it's ready for entering critical section
    }
}

void wait(semaphore *sem, int id) {
    --sem->value;

    if(sem->value < 0) {
        sem->blocked_queue->push(id);
    }
}
