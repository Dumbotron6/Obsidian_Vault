### Multiprocessing Vs Multithreading

| Multiprocessing                                                    | Multithreading                                                                                        |
| ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------- |
| In multiprocessing, CPUs are added for increasing computing power. | While In multithreading, many threads are created of a single process for increasing computing power. |
| In multiprocessing, Many processes are executed simultaneously.    | While in multithreading, many threads of a process are executed simultaneously.                       |
| Multiprocessing are classified into Symmetric and Asymmetric.      | While multithreading is not classified in any categories.                                             |
| In multiprocessing, Process creation is a time-consuming process.  | While in multithreading, process creation is according to economical.                                 |
| In multiprocessing, every process ownes a separate address space.  | While in multithreading, a common address space is shared by all the threads.                         |

### Concurrency Problems
Multithreading in Java requires careful handling of shared resources to avoid issues.

Common concurrency problems arise when multiple threads access shared resources without proper synchronization, leading to inconsistent and unpredictable results.

* **Race condition:** A race condition occurs when two or more threads access shared data simultaneously, and the outcome depends on the order in which the threads execute. For example, two threads may increment a counter with the `for(int i = 0; i < 10; i++) counter++`. This may not result in 20 as intermediate results may be lost.
	* ***Solution:*** Use synchronized blocks or AtomicInteger to ensure thread-safe updates.
* **Deadlock:** A deadlock occurs when two or more threads are waiting for each other to release resources, causing all of them to be stuck forever.
	* ***Solution:*** Always get the locks in the same order in every thread. This way, threads won’t block each other.

> [!example]- Example. Click to expand.
> ```java
> class GFG {
>     private final Object lock1 = new Object();
>     private final Object lock2 = new Object();
> 
>     public void methodA(){
>         
>         // Acquire lock1
>         synchronized (lock1) { 
>             System.out.println("Thread 1: Holding lock 1");
>             
>             // Waits for lock2
>             synchronized (lock2) { 
>                 System.out.println("Thread 1: Holding lock 2");
>             }
>         }
>     }
> 
>     public void methodB(){
>         
>         // Acquire lock2
>         synchronized (lock2){ 
>             System.out.println("Thread 2: Holding lock 2");
> 
>             // Waits for lock1
>             synchronized (lock1){
>                 System.out.println("Thread 2: Holding lock 1");
>             }
>         }
>     }
> 
>     public static void main(String[] args) {
>         GFG example = new GFG();
>         new Thread(example::methodA).start(); 
>         new Thread(example::methodB).start();
>     }
> }
> ```

* **Visibility problem:** This occurs when one thread modifies a variable, but other threads do not see the updated value due to caching or lack of synchronization.
	* Thread may cache a running variable, so changes made to it may not be immediately visible.
	* Use volatile keyword to ensure visibility: `private volatile boolean running = true;`. This ensures that changes to running are immediately visible to all threads.

> [!example]- Example. Click to expand.
> ```java
> class GFG {
>     
>     // Thread-unsafe
>     private boolean running = true;
> 
>     void start() {
>         new Thread(() -> {
>             while (running) {}
>             System.out.println("Stopped!");
>         }).start();
>     }
> 
>     // Change may not be visible to other thread
>     void stop() {
>         running = false; 
>     }
> 
>     public static void main(String[] args) throws InterruptedException {
>         GFG t = new GFG();
>         t.start();
>         
>         // Short pause before stopping
>         Thread.sleep(100);
>         
>         // Thread may not see this without volatile
>         t.stop();         
>     }
> }
> ```
> 
> `while(running)` loop may never terminate as the change made by stop() may not be immediately visible to the thread.

* **Starvation:** Starvation occurs when a thread never gets CPU time(time slice) or access to a resource because other high-priority threads keep executing.
	* ***Solution:*** Use fair locks or proper thread scheduling mechanisms to prevent starvation.

### Thread Priority
Thread priorities are in the range 1-10. `t1.setPriority(Thread.MAX_PRIORITY)` sets a thread's priority to high. Although it does not guarantee a higher time slice by the CPU, it is a suggestion by the process to the CPU to give it a higher time slice. preference.

#### Locks and Priority
Thread priority does not determine which thread gets access to a lock during contention.

##### 1. How Locks Decide Who Wins
When multiple threads are waiting for a lock to be released, the mechanism that decides the "winner" depends entirely on the type of lock you are using, not the thread's priority level.

| **Lock Type**                         | **Who gets the lock next?**                                                                                                                                                         |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`synchronized` blocks / methods**   | **Arbitrary.** The JVM and the underlying OS decide. There is no guarantee of order, fairness, or priority. Any waiting thread could wake up and grab it.                           |
| **`ReentrantLock` (Unfair Mode)**     | **Random / Barging.** This is the default. A new thread arriving at the exact moment the lock is freed can "barge" in and steal it from threads that have been waiting for minutes. |
| **`ReentrantLock(true)` (Fair Mode)** | **Strict FIFO.** The lock operates as a queue. The thread that has been waiting the longest gets the lock next. Priority is completely ignored.                                     |

##### 2. What Does Thread Priority Actually Do?
If priority does not help a thread get a lock, what is the point of `Thread.setPriority()`?

Thread priority is simply a **suggestion** to the Operating System's CPU scheduler regarding _CPU time allocation_, not lock acquisition. When you set a thread to `MAX_PRIORITY`, you are essentially telling the OS, "When this thread is awake and ready to run, please give it CPU time before other threads." Using thread priority is generally discouraged in modern development.

If your application requires a specific execution order or you need to guarantee that a specific thread gets access to a resource before others, you cannot rely on thread priority. You must handle the ordering explicitly in your code using concurrency utilities like `CountDownLatch`, `Semaphore`, or a `PriorityBlockingQueue`. #lookupmore 

### Thread Safety
#### Synchronization
Synchronization is already explained in previous sections([[Thread#BLOCKED state|more]]). There are three types, a synchronized method, a synchronized block and *static synchronization*. Static synchronization is used when static data or methods need to be protected in a multithreaded environment. It ensures that only one thread can access the class-level resource at a time.

* **Process synchronization** is a fundamental concept in operating systems that ensures multiple processes or threads can execute safely while sharing common resources. It uses mechanisms like semaphores, mutex locks, and monitors for coordination. #lookupmore 
* **Thread Synchronization** is used to coordinate and ordering of the execution of the threads in a multi-threaded program. There are two types of thread synchronization are mentioned below:
	* *Mutual Exclusion:* It is the mechanism of preventing resources from accessing the same resource at the same time using synchronized method/blocks to prevent race conditions.
	* *Cooperation:* This governs how threads communicate and hand off control. This is by using `wait`, `notify` and `notifyAll`.

#### Volatile
The volatile keyword in Java is used to ensure that changes made to a variable are immediately visible to all threads It prevents caching of the variable's value by threads.
- Ensures visibility of shared variables across threads by preventing caching issues.
- Does not provide atomicity, so it is not suitable for operations like increment (count++).

> [!example]- Example with explanation. Click to expand.
> ```java
> class SharedData {
>     volatile boolean flag = false;
> }
> 
> class MyThread extends Thread {
>     SharedData data;
> 
>     MyThread(SharedData data) {
>         this.data = data;
>     }
> 
>     public void run() {
>         while (!data.flag) {
>             // waiting
>         }
>         System.out.println("Flag changed!");
>     }
> }
> 
> public class Geeks{
>     public static void main(String[] args) throws Exception {
>         SharedData data = new SharedData();
>         MyThread t = new MyThread(data);
>         t.start();
> 
>         Thread.sleep(1000);
>         data.flag = true;
>     }
> }
> ```
> 
> ***Explanation:***
> - The thread keeps running in the while (!data.flag) loop.
>- After 1 second, the main thread sets flag = true.
>- Because flag is volatile, the change is immediately visible.
>- The loop exits, and the message "Flag changed!" is printed

##### When to Use volatile
- For boolean flags (e.g., stop thread signal)
- For status indicators
- When only one thread writes and others read
- When no compound operations are involved (like increment)

##### Volatile vs Synchronized

|Feature|volatile|synchronized|
|---|---|---|
|Purpose|Ensures visibility of variables|Ensures visibility + atomicity|
|Thread Safety|Partial (only visibility)|Full thread safety|
|Atomicity|Not guaranteed|Guaranteed|
|Locking|No locking|Uses intrinsic lock (monitor)|
|Performance|Faster (lightweight)|Slower (due to locking overhead)|
|Use Case|Flags, status variables|Critical sections, shared resources|

#### Atomic Variables
Atomic variables ensures data consistency without using synchronization or locks. They allow threads to act on the same variable with changes being consistent. Below are some commonly used atomic variable types in Java.
- AtomicInteger
- AtomicLong
- AtomicBoolean
- AtomicReference example `AtomicReference<String>` or `AtomicReferenct<Truck>`
- AtomicIntegerArray

### Daemon Threads
Daemon threads are background threads that provide support services to user threads. They do not prevent the JVM from exiting when all user threads have completed execution.
- Run in the background to support user threads
- Automatically terminate when user threads end
- Created using setDaemon(true) method
- A thread inherits the daemon status of the thread that creates it.

> [!example]- Example. Click to expand.
> ```java
> class DaemonThreadExample extends Thread{
>     
>     public void run(){
>         
>         while (true) {
>             System.out.println("Daemon thread running...");
>         }
>     }
> 
>     public static void main(String[] args){
>         
>         DaemonThreadExample t1 = new DaemonThreadExample();
>         
>         // Set as daemon thread
>         t1.setDaemon(true); 
>         t1.start();
> 
>         System.out.println("Main thread ends");
>     }
> }
> ```

#### Daemon Threads Vs User Threads

|Feature|User Thread|Daemon Thread|
|---|---|---|
|Purpose|Performs main application tasks|Handles background/support tasks|
|JVM Behavior|JVM waits for completion|JVM does not wait|
|Execution|Keeps application running|Stops when user threads end|
|Creation|Created by default|Must be set using setDaemon(true)|
|Example|Main thread, worker threads|Garbage Collector, background services|

### Topics to read more on
- `ReentrantLock` and `ReadWriteLock` (and how they provide more flexibility than `synchronized`).
    
- `ConcurrentHashMap` (and how its internal segmenting/locking works compared to a synchronized `Hashtable`).
    
- `CountDownLatch` and `CyclicBarrier`.
    
- `CompletableFuture` (which bridges nicely into the reactive/asynchronous programming concepts you use with WebFlux).