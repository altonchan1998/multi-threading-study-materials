## Thread Creation
1. new Thread(new Runnable() {})
2. inherit thread class

## Thread interrupt 
1. threadA.interrupt() -> set the interrupted flag of threadA to true
   - Need to define the behaviour according to the interrupted flag
   - for sleep/join etc. method that are defined to throw InterruptedException natively, the interrupted flag would be set to false when InterruptedException is caught, DO NOT DO NOTHING IN THE CAUGHT BLOCK, at least  Thread.currentThread().interrupt(); to restore the flag to true.
2. daemon thread: mark the flag as background thread which won't prevent the application from exiting when the main thread is terminated.
3. 