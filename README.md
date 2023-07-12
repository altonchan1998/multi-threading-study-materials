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
# Every shared variable which modified by at least 1 thread should be guareed by a synchronized block or declared with volatile