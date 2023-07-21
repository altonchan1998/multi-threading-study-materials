# Thread Creation
1. new Thread(new Runnable() {})
2. inherit thread class

# Thread Coordination
## Thread interrupt
1. threadA.interrupt() -> set the interrupted flag of threadA to true
   - Need to define the behaviour according to the interrupted flag
   - for sleep/join etc. method that are defined to throw InterruptedException natively, the interrupted flag would be set to false when InterruptedException is caught, DO NOT DO NOTHING IN THE CAUGHT BLOCK, at least  Thread.currentThread().interrupt(); to restore the flag to true.
## daemon thread
   - mark the flag as background thread which won't prevent the application from exiting when the main thread is terminated.
## thread.join(:maxDuration)
   - The main thread need to wait the joined thread to die before execute the code afterwards.
   - Can pass the max duration to wait before return from the thread.
   - After max duration, join() return from the thread but won't terminate the thread.

# Data Sharing Between Threads
## Atomic Operation
   - program operations that run completely independently of any other processes.
   - no intermediate state.
   - non-atomic operation against shared data between multi-threads might lead to defects (refer to 04 case study)
   - all reference assignments are atomic -> getter and setter are safe
   - all assignments to primitive types are atomic, except long and double ({32bit, 32bit} operations)
   - assignments to long and double if declared volatile -> guarantee to be performed by a single hardware operation
## Critical section
   - section of code that could be accessed and modified by multi-threads
## synchronized keyword
   - to restrict access to a critical section or entire method to a single thread at one time
   - function level: applied per object (e.g. 2 synchronized functions defined in 1 object -> when thread A calling either one of the functions, thread B cannot access any of the synchronized functions)
   - code block level with lock object: more flexibility, when the synchronized block being accessed, other synchronized function or block with other lock object won't be locked.
   - Best practice: minimize the amount of code in the critical section -> more code can be executed concurrently by threads.
   - synchronized block is reentrant: a thread cannot prevent itself from entering a critical section (e.g. thread A accessing synchronized method A in which access synchronized method B, it will be ok.)
   - solve race condition and data race with performance penalty
## volatile keyword
   - solve race condition for read/write for long and double
   - solve data races by guarantee the code execution order
## excessive synchronization
   - when too many code is marked as synchronized -> multi-thread is meaningless as no code is simultaneously running. 
## Race Condition
   - condition when multi threads access a shared resource
   - -> if any thread is modifying the resource
   - -> timing of treads' scheduling may cause incorrect result
   - -> due to non-atomic operations performed on the shared resource
## Data Race
   - compiler and cpu may execute the instructions out of order to optimize performance and utilization
   - -> unexpected incorrect results
   - solutions:
   1. synchronization of methods -> only 1 thread able to access the shared variable at anytime -> no race possible but with a performance penalty
   2. declare share variable with volatile -> ensure the code come before an access to volatile variable will be executed before the access instruction
## ***Every shared variable which modified by at least 1 thread should be guareed by a synchronized block or declared with volatile
## Fine-grained Locking Strategy
   - separate locks for every single shared resources
   - may lead to dead lock issue
## Coarse-Grained Locking Strategy
   - one single lock for all shared resource
   - e.g. synchronized method -> one lock share for all method declared
   - simple but may thus have performance penalty
## Dead Lock
   - every threads are waiting for other thread to release the lock to do the next move
   - conditions:
   - mutual exclusion - only 1 thread can have exclusive access to a resource
   - hold and wait - at least 1 thread is holding a resource and wait for another resource
   - non-preemptive allocation: a resource is released only after the thread is done using it
   - circular wait - a chain of at least 2 threads each one is holding one resource and waiting for another resource
   - solution: 
   - use a strict order on lock acquisition (recommended), easy for small number of locks
   - deadlock detection - watchdog (restart when deadlock occur)
   - thread interruption (cannot with synchronized)
   - tryLock operations (cannot with synchronized)

## ReentrantLock
   - require explicit locking and unlocking
   - need to be careful with unlocking the lock
   - use try finally to make sure the lock will be unlocked
   - more control over the lock and more lock operations allowed
   - new ReentrantLock(true) to make the lock fair (fairly be acquired by the threads)
   - lockInterruptibly() -> interrupt the thread which locked the lock and lock it in current thread
   - used for watchdog for deadlock detection and recovery
   - used for waking up slept threads to do clean and close the application gracefully
   - tryLock()
   - return true and acquires lock if available
   - return false and won't sleep if lock is unavailable
   - used when suspending a thread on wait lock (lock() method) is unacceptable

## ReentrantReadWriteLock
   - Unlike synchronized lock and reentrantLock, it allows multi readers to access a shared resource concurrently
   - used when read operations are predominant
   - used when read operation are not fast
   - used when mutual exclusion of reading threads impacts the performance negatively
   - mutual exclusion between readers and writers by 
     - no thread can acquire read lock if write lock is acquired 
     - no thread can acquire write lock if at least one thread holds a read lock

## Semaphore
  - used to restrict the no. of users to a particular resource or group of resource
  - while locks only allow 1 user per resource
  - it can restrict any given no. of users to a resource
  - it can be released by any thread even those thread not acquired it
  - inter-thread communication for producer consumer
  - new Semaphore(0) means there is no permit at beginning
  - semaphore.release() will create a permit even there is 0 permit at the first place

## lock.condition()
  - condition.await() - unlock the lock and sleep unit condition.signal()
  - condition.signal() - wake up only one of the threads which waiting for the condition variable
  - for the thread that wakes up, it has to reacquire the lock associated with the condition variable
  - if no thread is waiting for the condition variable, signal() won't do anything
  - condition.signalAll() - wake up all threads that waiting for the condition variable

##  Object.wait() / Object.notify() / Object.notifyAll()
  - wait() - cause the current thread to sleep, until other thread wake it up
  - notify - wake up only one of the threads that waiting for the object 
  - Object.notifyAll() - wake up all the threads that waiting for the object
  - to call these methods, we need to acquire the monitor of that object by using synchronized on that object
