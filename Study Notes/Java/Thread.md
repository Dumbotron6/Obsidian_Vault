### Process vs Thread
A process is an independent program in execution, while a thread is a lightweight sub-process. It is a program that is currently in execution within an operating system. Each process has a unique Process ID (PID) for identification.

A Java thread is the smallest unit of execution within a program. It is a lightweight subprocess that runs independently but shares the same memory space as the process, allowing multiple tasks to execute concurrently. Each thread has its own Thread ID (TID) for identification.

We can create threads in java using two ways:
- Extending Thread Class
- Implementing a Runnable interface

Implementing Runnable is preferred. Because `Runnable` is just a task, you can instantiate it **once** and pass that single instance into multiple different threads. This is incredibly useful when you want multiple threads to work on or modify the same exact data. We can set names to threads using the constructor and calling `super(name)` or using `setname(name)`.

> [!EXAMPLE]- Explanation with example. Click to expand.
> ```java
> class TicketCounter implements Runnable {
>     private int availableTickets = 5; // Shared resource
> 
>     @Override
>     public void run() {
> 	    // Prevents race conditions. Explanation on this in below sections in the doc.
>         synchronized(this) {
>             if (availableTickets > 0) {
>                 System.out.println(Thread.currentThread().getName() + " booked a ticket.");
>                 availableTickets--;
>                 System.out.println("Tickets left: " + availableTickets);
>             } else {
>                 System.out.println(Thread.currentThread().getName() + " found no tickets left.");
>             }
>         }
>     }
> }
> 
> public class Main {
>     public static void main(String[] args) {
>         // 1. Create ONE instance of the task
>         TicketCounter sharedCounter = new TicketCounter();
> 
>         // 2. Create MULTIPLE threads pointing to that same single instance
>         Thread passenger1 = new Thread(sharedCounter, "Passenger A");
>         Thread passenger2 = new Thread(sharedCounter, "Passenger B");
>         Thread passenger3 = new Thread(sharedCounter, "Passenger C");
> 
>         // 3. Start the threads
>         passenger1.start();
>         passenger2.start();
>         passenger3.start();
>     }
> }
> ```
> If `TicketCounter` had extended `Thread`, you wouldn't be able to pass it into another thread. You would have to do this:
> ```java
>	TicketCounter thread1 = new TicketCounter();
>	TicketCounter thread2 = new TicketCounter();
> ```
> Because they are two completely different objects, `thread1` would have its own 5 tickets, and `thread2` would have its own 5 tickets. They wouldn't be sharing a single pool of tickets anymore.
>
>By using `Runnable`, the data stays inside the `Runnable` instance, and the `Thread` objects simply act as the engines that execute it. Therefore, passenger1, passenger2 and passenger3 will share `availableTickets` and the count will decrease each time.

### Multithreading
Multithreading in Java is a feature that enables a program to run multiple threads simultaneously, allowing tasks to execute in parallel and utilize the CPU more efficiently.

- Threads allow parallel execution of tasks.
- A process can have multiple threads.
- Each thread runs independently but shares the same memory.

### Running Threads in Java

The Thread class is used to create and control threads in Java. Each object of this class represents a single thread of execution. There are two methods used for running Threads in Java:

- **run() Method:** Contains the code for the thread. Calling it directly behaves like a normal method call.
- **start() Method:** Launches a new thread and internally calls run() concurrently. We use start() to launch a new thread, which then calls the run() method in parallel. If we call run() directly, it works like a normal method call and no new thread is created.


> [!EXAMPLE]- Example. Click to expand.
> ``` java
> // Thread class implementation
class ThreadImpl extends Thread{
>     
>     @Override
>     public void run(){
>         
>         // Output: Thread Class Running
>         System.out.println("Thread Class Running");
>     }
> }
> 
> // Runnable interface implementation
> class RunnableThread implements Runnable{
>     
>     @Override
>     public void run(){
>         
>         // Output: Runnable Thread Running
>         System.out.println("Runnable Thread Running");
>     }
> }
> 
> public class Geeks{
>     
>     public static void main(String[] args){
>         
>         // Create and start Thread class thread
>         ThreadImpl t1 = new ThreadImpl();
>         t1.start();
> 
>         // Create and start Runnable interface thread
>         RunnableThread r = new RunnableThread();
>         Thread t2 = new Thread(r);
>         t2.start();
> 
>         // Wait for both threads to complete
>         try {
>             t1.join(); // Wait for t1
>             t2.join(); // Wait for t2
>         } catch (InterruptedException e) {
>             e.printStackTrace();
>         }
>     }
> }
> ```

### Life Cycle of a Thread

During its thread life cycle, a Java thread transitions through several states from creation to termination. The **thread scheduler** ([[../Quick lookup#Thread scheduler|more]]) controls state transitions. It also handles which ([[../Quick lookup#1. Which Thread to Run? (Priority & Preemption)|more]]) scheduler to run and when ([[../Quick lookup#2. How Long Does It Run? (Time Slicing)|more]]).

- New State
- Runnable State
- Blocked State
- Waiting State
- Timed Waiting State
- Terminated State
![[Thread.webp]]

- **`NEW`**: The thread is created using the `new` keyword but `start()` has not yet been invoked. Its code has not begun running.
- **`RUNNABLE`**: The thread is actively executing within the JVM, though it may be momentarily waiting for operating system resources like CPU allocation.
- **`BLOCKED`**: The thread is paused, waiting to acquire a monitor lock that is currently held by another thread.
- **`WAITING`**: The thread is waiting indefinitely for another thread to perform a specific action, triggered by methods without timeouts:
    - `Object.wait()`
    - `Thread.join()`
    - `LockSupport.park()`  
- **`TIMED_WAITING`**: The thread is waiting for a specified period. It returns to runnable once the time expires or it is notified. Triggered by:
    - `Thread.sleep(long millis)`
    - `Object.wait(long timeout)`
    - `Thread.join(long timeout)`
    - `LockSupport.parkNanos()` / `parkUntil()`
- **`TERMINATED`**: The thread has completely finished executing its `run()` method.

> [!exampe]- Example for states with output. Click to expand.
> ```java
> class TicketBooking implements Runnable {
>     @Override
>     public void run() {
>         
>         try {
>             
>             // Timed waiting
>             Thread.sleep(200); //Causes bookingThread to sleep for 200
>         } catch (InterruptedException e) {
>             e.printStackTrace();
>         }
> 
>         System.out.println("State of mainThread after bookingThread.join() is called: " +
>                 TicketSystem.mainThread.getState()); //WAITING
> 
>         try {
>             
>             // Another timed waiting
>             Thread.sleep(100); 
>         } catch (InterruptedException e) {
>             e.printStackTrace();
>         }
>     }
> }
> 
> public class TicketSystem implements Runnable {
>     public static Thread mainThread;
>     public static TicketSystem ticketSystem;
> 
>     @Override
>     public void run() {
>         TicketBooking booking = new TicketBooking();
>         Thread bookingThread = new Thread(booking);
> 
>         System.out.println("State after creating bookingThread: " + bookingThread.getState()); //NEW
> 
>         bookingThread.start();
>         System.out.println("State after starting bookingThread: " + bookingThread.getState()); //RUNNABLE
> 
>         try {
>             Thread.sleep(100); //Causes mainThread to sleep for 100
>         } catch (InterruptedException e) {
>             e.printStackTrace();
>         }
> 
> 		//mainThread sleep has ended but bookingThread sleep still has 200.
>         System.out.println("State after sleeping bookingThread: " + bookingThread.getState()); //TIMED_WAITING
> 
>         try {
>             
>             // Moves mainThread to waiting state
>             //Forces mainThread to wait till bookingThread finishes execution.
>             bookingThread.join();
>         } catch (InterruptedException e) {
>             e.printStackTrace();
>         }
> 
>         System.out.println("State after bookingThread finishes: " + bookingThread.getState()); //TERMINATED
>     }
> 
>     public static void main(String[] args) {
>         ticketSystem = new TicketSystem();
>         mainThread = new Thread(ticketSystem);
> 
>         System.out.println("State after creating mainThread: " + mainThread.getState()); //NEW
> 
>         mainThread.start();
>         System.out.println("State after starting mainThread: " + mainThread.getState()); //RUNNABLE
>     }
> }
> ```
> ![[Thread-2.webp]]
> 

> [!SUMMARY]- Illustration of moving to TIMED_WAITING. Click to expand.
> ![[Thread-1.webp]]

> [!IMPORTANT]
> In the above example, `mainThread` waits till `bookingThread` is completed to resume execution. That is what `bookingThread.join()` does. It tells the caller thread `mainThread` to wait till `bookingThread` execution is completed.

> [!NOTE]
> Both being ready to run and actively running are combined into the **`RUNNABLE`** state.
> 
> However, from an operating system and conceptual standpoint, a thread transitions from "ready to run" to "running" **when the OS thread scheduler allocates a CPU core (a time slice) to it**.

#### BLOCKED state
A thread enters the **`BLOCKED`** state when it is alive and ready to run, but it cannot proceed because it is waiting to acquire a **monitor lock** (also known as an intrinsic lock or synchronized lock).

In simple terms: it is waiting for another thread to finish using a `synchronized` block or method.

##### The State Transition Flow
- **How it enters `BLOCKED`**: Thread A attempts to enter a section of code marked `synchronized`, but Thread B is already inside that section holding the lock. Thread A is forced into the `BLOCKED` state.
    
- **How it exits `BLOCKED`**: When Thread B exits the `synchronized` block and releases the lock, the OS/JVM picks a blocked thread (like Thread A) and moves it back to the `RUNNABLE` state so it can try to grab the lock.

> [!EXAMPLE]- Example for synchronized method. Click to expand.
> ```java
> public class BlockedExample {
>     public synchronized static void syncMethod() {
>         while(true) {
>             // Thread 1 will get stuck here forever, holding the lock
>         }
>     }
> 
>     public static void main(String[] args) throws InterruptedException {
>         Thread t1 = new Thread(() -> syncMethod());
>         Thread t2 = new Thread(() -> syncMethod());
> 
>         t1.start();
>         Thread.sleep(100); // Give t1 time to start and grab the lock
>         
>         t2.start(); // t2 tries to enter syncMethod, but t1 has the lock
>         Thread.sleep(100); // Give t2 time to try and fail
> 
>         // This will print: BLOCKED
>         System.out.println("t2 state: " + t2.getState()); 
>     }
> }
> ```

> [!EXAMPLE]- Example for synchronized code block. Click to expand.
> ```java
> class BankAccount {
>     private int balance = 100;
>     // A dedicated object used purely as the "padlock"
>     private final Object lock = new Object(); 
> 
>     public void withdraw(String threadName, int amount) {
>         // 1. Unsynchronized Code: Anyone can check the balance concurrently
>         System.out.println(threadName + " is checking the balance...");
> 
>         // 2. Synchronized Block: Only ONE thread can enter this block at a time
>         synchronized (lock) { 
>             System.out.println(threadName + " acquired the lock.");
>             if (balance >= amount) {
>                 try { 
>                     Thread.sleep(100); // Simulating processing time
>                 } catch (InterruptedException e) { e.printStackTrace(); }
>                 
>                 balance -= amount;
>                 System.out.println(threadName + " successfully withdrew $" + amount);
>                 System.out.println("Remaining Balance: $" + balance);
>             } else {
>                 System.out.println(threadName + " failed to withdraw. Insufficient funds.");
>             }
>         } // <-- The lock is AUTOMATICALLY released right here!
> 
>         // 3. Unsynchronized Code: Threads can execute this concurrently
>         System.out.println(threadName + " has finished their transaction session.");
>     }
> }
> 
> public class SyncBlockExample {
>     public static void main(String[] args) {
>         BankAccount account = new BankAccount();
> 
>         // Two threads trying to withdraw $70 at the same time
>         Thread t1 = new Thread(() -> account.withdraw("Thread-1", 70));
>         Thread t2 = new Thread(() -> account.withdraw("Thread-2", 70));
> 
>         t1.start();
>         t2.start();
>     }
> }
> ```

A block is released when the synchronized code block is completed, a `return` is encountered within the block, or when an exception happens inside the block. 

##### Behind the Scenes: The JVM "Safety Net"
Under the hood, when you write a `synchronized` block, the Java compiler translates it into bytecode using two specific instructions: `monitorenter` and `monitorexit`.

To ensure the lock is _always_ released (even during a crash), the compiler structurally wraps your code in an implicit `try-finally` block that looks conceptually like this:

```java
// What you write:
synchronized(lock) {
    executeBusinessLogic();
}

// What the JVM actually executes behind the scenes:
monitorenter(lock);
try {
    executeBusinessLogic();
} finally {
    monitorexit(lock); // This is GUARANTEED to run, no matter what
}
```

#### WAITING
`BLOCKED` and `WAITING` triggered by entirely different mechanisms:

| **Feature**         | **BLOCKED**                                                         | **WAITING (or TIMED_WAITING)**                                                                         |
| ------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Primary Cause**   | Waiting for a **lock** to be released by another thread.            | Waiting for a **signal** (notification) from another thread.                                           |
| **Trigger Methods** | Attempting to enter a `synchronized` block/method.                  | Calling `Object.wait()`, `Thread.join()`, or `LockSupport.park()`.                                     |
| **How it wakes up** | Automatically moves to `RUNNABLE` as soon as the lock becomes free. | Requires another thread to explicitly call `notify()`, `notifyAll()`, or for the joined thread to die. |

> [!EXAMPLE]- Code example. Click to expand.
> ```java
> public class CoffeeShopExample {
>     // The shared lock and condition flag
>     private static final Object counter = new Object();
>     private static boolean coffeeReady = false;
> 
>     public static void main(String[] args) throws InterruptedException {
>         
>         // 1. Customer Thread (The Waiter)
>         Thread customer = new Thread(() -> {
>             synchronized (counter) {
>                 System.out.println("Customer: Ordering a coffee...");
>                 
>                 while (!coffeeReady) {
>                     try {
>                         System.out.println("Customer: No coffee ready. Going to WAITING state...");
>                         // Releases the 'counter' lock and pauses indefinitely
>                         counter.wait(); 
>                     } catch (InterruptedException e) {
>                         e.printStackTrace();
>                     }
>                 }
>                 
>                 // Wakes up here once notified and lock is re-acquired
>                 System.out.println("Customer: Ah, thank you! *Drinks coffee*");
>                 //Reset the state so the loop works for the NEXT order
>                 coffeeReady = false;
>             }
>         });
> 
>         // 2. Barista Thread (The Waker)
>         Thread barista = new Thread(() -> {
>             synchronized (counter) {
>                 System.out.println("Barista: Shaking espresso, steaming milk... (takes 2 seconds)");
>                 try {
>                     Thread.sleep(2000); // Simulate brewing time
>                 } catch (InterruptedException e) { e.printStackTrace(); }
>                 
>                 coffeeReady = true;
>                 System.out.println("Barista: Coffee is ready! Notifying customer...");
>                 
>                 // Wakes up the thread waiting on the 'counter' lock
>                 counter.notify(); 
>                 
>             } // Lock is officially released here when the block ends
>         });
> 
>         customer.start();
>         Thread.sleep(500); // Ensure customer starts and waits first
>         barista.start();
>     }
> }
> ```

- **Customer Grabs the Lock:** The `customer` thread enters the `synchronized(counter)` block.
- **Customer Waits:** Inside, it sees `coffeeReady` is false. It executes `counter.wait()`.
    - _Crucial Detail:_ The moment `wait()` is called, the customer thread **instantly releases the lock** and enters the `WAITING` state. Because it released the lock, the barista thread is now allowed to run.
- **Barista Steps In:** The `barista` thread enters its `synchronized(counter)` block, spends 2 seconds brewing, and sets `coffeeReady = true`.
- **The Wake-Up Call:** The barista executes `counter.notify()`. This tells the JVM: _"Hey, wake up one thread that is waiting on this counter object."_ 
- **The Handoff:** The `customer` thread wakes up, transitions from `WAITING` back to `RUNNABLE`, waits for the barista to finish the synchronized block to re-acquire the lock, and successfully finishes its execution.

> [!IMPORTANT]
> In Java, a thread **is not allowed** to make another thread wait or wake up unless it currently owns the lock for that specific object. So both the `customer` and `barista` share the same lock, `counter`.
> 
> If you try to call `wait()` or `notify()` on an object without being inside a `synchronized` block for that _exact_ object, Java will instantly throw an `IllegalMonitorStateException` and crash the thread.

##### Lock release and checking using loops
The `while (!coffeeReady)` loop is one of the most critical patterns in Java multithreading. It is used to protect against a dangerous behavior called a **Spurious Wakeup**, and to handle situations where multiple threads are competing for the same resource.

If you replaced the `while` loop with a simple `if` statement, your code would eventually suffer from random, hard-to-debug glitches.

In computer science and concurrent programming, this specific pattern is called the **Guarded Suspension Pattern**.

It is also frequently referred to by the concrete actions it takes: the **Wait-and-Notify Loop** or **Condition Loop**

##### 1. Spurious Wakeups (The Phantom Alarm)
For reasons rooted in how computer operating systems and hardware chips handle threads, **a waiting thread can occasionally wake up for absolutely no reason at all**, even if no other thread called `notify()`. This is known as a _spurious wakeup_.

- **With an `if` statement:** If a spurious wakeup occurs, the thread wakes up inside the `if` block, exits it, and immediately proceeds to execute the code below it—meaning the customer tries to drink coffee that _doesn't exist yet_, crashing your logic.
- **With a `while` loop:** If the thread wakes up unexpectedly, the loop forces it to re-evaluate the condition (`!coffeeReady`). Seeing that the coffee is still not ready, it immediately goes back to sleep.

##### 2. The "Stolen Signal" Problem (Multiple Consumers)
Imagine a scenario where you have **two customers** waiting for coffee, and the barista makes just **one cup** and calls `notifyAll()`.

- **If Customer 2 used an `if` statement:** Once Customer 1 finishes and releases the lock, Customer 2 blindly steps forward and tries to drink coffee, but Customer 1 already drank it! The data is corrupted.
- **If Customer 2 used a `while` loop:** Once Customer 1 releases the lock, Customer 2 grabs the lock, loops back to check `while (!coffeeReady)`, realizes the coffee is gone, and safely puts itself back to sleep until the next cup is brewed.

We could have just used the `while(!coffeeReady)` and not used `counter.lock()` at all, but this would **keep the loop running indefinitely** till `coffeeReady` becomes true. This is an unnecessary waste of resources. This is known as *Spin Locking*. By adding `wait()`, the thread completely steps off the CPU and goes to sleep. It consumes **0% CPU resources** while waiting. It only wakes up when it is notified via `notify` or `notifyAll`.

##### notifyAll()
`notifyAll()` becomes incredibly useful—and often mandatory—in applications where **multiple threads are waiting on the exact same lock, but they are waiting for different conditions.**

If you have more than one thread in a `WAITING` state, using `notify()` is dangerous because it only wakes up **one random thread**. If the JVM randomly picks a thread that isn't ready to process(say Thread1) the current state, that thread will just go back to sleep, and the entire system can freeze (a state known as a *nested monitor lockout or signal omission*). Another thread that is ready(say Thread2) won't resume it's process because it was never notified.

###### notifyAll Example
Imagine a single shared `Warehouse` vault that can hold a maximum of 10 items. We have three types of threads interacting with it:

1. **Producer 1 (The Bulk Producer):** Wants to deposit 5 items at once.
2. **Producer 2 (The Small Producer):** Wants to deposit 1 item.
3. **Consumer (The Buyer):** Wants to take 3 items out.

All three threads are bound by the **Guarded Suspension Pattern** (using a `while` loop with `wait()`) inside the same `synchronized(vault)` lock. If we use `notify` instead of `notifyAll`

- The JVM randomly picks a thread from the waiting room to wake up. It randomly chooses **Producer 1**.
- Producer 1 wakes up, enters the `while` loop, and checks its condition: _"Is there space for 5 items?"_ 
* The vault only has room for 4 items ($10 - 6 = 4$). Producer 1's condition is false.
- Producer 1 drops right back into the `WAITING` state.

Because `notify()` was used, **Producer 2 was never alerted**, even though there _was_ enough room for its 1 item! The Consumer has gone to sleep, both Producers are asleep, and the program freezes forever.

If the Consumer had called **`vault.notifyAll()`** instead:

1. **Both** Producer 1 and Producer 2 are instantly woken up and moved to the `RUNNABLE` queue.
2. They compete to re-acquire the lock. Let's say Producer 1 grabs it first.
3. Producer 1 checks its loop condition, realizes 5 items won't fit, and goes back to sleep (releasing the lock).
4. Producer 2 immediately grabs the released lock, checks its loop condition, realizes 1 item _will_ fit, deposits its item, and safely exits the block.


> [!SUMMARY] Summary: When to use `notifyAll()`
>
> - Use `notify()` **only** if all waiting threads are doing the exact same thing (e.g., a pool of identical workers waiting for identical tasks) and a signal can be safely handled by _any_ single thread.
> - Use `notifyAll()` if your waiting threads have **different conditions** or purposes, ensuring that the thread actually capable of making progress gets the chance to run.

> [!EXAMPLE]- Code. Click to expand.
> ```java
> import java.util.ArrayList;
> import java.util.List;
> 
> class Warehouse {
>     private final List<String> vault = new ArrayList<>();
>     private final int MAX_CAPACITY = 10;
> 
>     // Pre-fill the warehouse with 10 items
>     public Warehouse() {
>         for (int i = 0; i < 10; i++) {
>             vault.add("Item");
>         }
>     }
> 
>     // 1. Producer Thread Logic
>     public void deposit(String name, int amount) {
>         synchronized (this) { // <--- Threads must acquire the warehouse lock here
>             System.out.println(name + " wants to deposit " + amount + " items. Current stock: " + vault.size());
> 
>             // Guarded Suspension Pattern: Loop until there is enough space
>             while (vault.size() + amount > MAX_CAPACITY) {
>                 System.out.println(name + " sees NOT ENOUGH SPACE. Dropping lock and entering WAITING...");
>                 try {
>                     this.wait(); // <--- PRODUCER 1 RELEASES THE LOCK HERE AND SLEEPS
>                 } catch (InterruptedException e) { e.printStackTrace(); }
>                 
>                 // When awakened, the thread must wait to re-acquire the lock before executing this line
>                 System.out.println(name + " woke up and re-acquired the lock! Re-checking space...");
>             }
> 
>             // Code only reaches here if the while loop condition is false (i.e., it fits!)
>             for (int i = 0; i < amount; i++) {
>                 vault.add("Item");
>             }
>             System.out.println(name + " successfully deposited " + amount + " items! Total stock: " + vault.size());
>         } // <--- Lock is automatically released here upon normal exit
>     }
> 
>     // 2. Consumer Thread Logic
>     public void withdraw(String name, int amount) {
>         synchronized (this) {
>             System.out.println(name + " is withdrawing " + amount + " items...");
>             for (int i = 0; i < amount; i++) {
>                 if (!vault.isEmpty()) vault.remove(0);
>             }
>             System.out.println(name + " finished. Total stock left: " + vault.size());
>             
>             System.out.println(name + " is calling notifyAll()...");
>             this.notifyAll(); // <--- Wakes up BOTH Producer 1 and Producer 2
>         } 
>     }
> }
> 
> public class WarehouseSimulation {
>     public static void main(String[] args) throws InterruptedException {
>         Warehouse warehouse = new Warehouse();
> 
>         Thread producer1 = new Thread(() -> warehouse.deposit("Producer-1 (Bulk)", 5));
>         Thread producer2 = new Thread(() -> warehouse.deposit("Producer-2 (Small)", 1));
>         Thread consumer = new Thread(() -> warehouse.withdraw("Consumer", 3));
> 
>         // Start producers first so they get stuck waiting
>         producer1.start();
>         Thread.sleep(100); // Small pause to guarantee Producer 1 goes first
>         producer2.start();
>         Thread.sleep(100);
> 
>         // Start consumer to free up space and shout notifyAll()
>         consumer.start();
>     }
> }
> ```

Note that above code can lead to *spurious wakeups*. But that is exactly why we use **Guarded Suspension Pattern**. If a Producer 1 spuriously wakes up, it checks the condition, which fails, and Producer 1 goes back to waiting.

Also note that the lock object used is the `Warehouse` instance itself, hence it has  `synchronized(this)`. It simply prevents Producer 1, Producer 2 and Consumer from acting on the capacity at the same time.

### Q&A
In java, assume all my threads are waiting. If I wait on a thread, is the core prevented from spawning another thread? What happens when I run into thread limit and I want to spawn a new thread?

**Answer from Gemini:**
It is a very common misconception to conflate a blocked thread with a blocked CPU core. The short answers to your questions are: **No, waiting on a thread does not block the CPU core**, and if you hit the thread limit, **your application will crash with an `OutOfMemoryError`**.

Here is a breakdown of exactly what happens at the hardware and JVM levels in both scenarios.

##### 1. Does waiting on a thread prevent the core from doing other work?
**No.** To understand why, you have to look at how Java threads interact with the operating system (OS).

Standard Java threads (Platform Threads) map 1:1 to native OS threads. When you tell a thread to wait (e.g., using `Object.wait()`, `Thread.sleep()`, `Thread.join()`, or by making a blocking I/O call), here is what actually happens:

- **Context Switching:** The OS thread scheduler sees that your thread cannot make any more progress right now. It immediately removes your thread from the CPU core's "run queue" and puts it into a `WAITING` or `BLOCKED` state.
- **Freeing the Core:** The CPU core is now instantly freed up. The OS scheduler will immediately assign a different, active thread (either from your Java application or a completely different program running on your computer) to that core.
- **The Logical Catch:** You mentioned, _"Assume all my threads are waiting... is the core prevented from spawning another thread?"_ If literally **all** of your application's threads are in a `WAITING` state, no Java code is currently executing. Because no code is executing, there is no thread awake to actually run the `new Thread().start()` command. However, the CPU core itself is perfectly fine and is happily executing other background tasks for your operating system.

##### 2. What happens when you hit the thread limit?
If you try to spawn a new thread and the system cannot accommodate it, the JVM will throw a highly specific error and crash the thread attempting to do the spawning:

`java.lang.OutOfMemoryError: unable to create new native thread`

This happens because standard Java threads are incredibly resource-heavy. Hitting the "thread limit" usually isn't an arbitrary number set by Java; it is a hard physical or OS-level constraint. It occurs for one of two reasons:

- **RAM Exhaustion (Stack Space):** Every standard Java thread allocates a block of memory for its call stack (typically 1MB per thread on a 64-bit JVM). If you try to spawn 4,000 threads, the JVM needs to ask the OS for 4GB of contiguous RAM just for thread stacks. If your system runs out of memory, the OS denies the request.
- **OS Limits:** Operating systems have strict limits on how many processes/threads a single user or application can create to prevent rogue programs from taking down the system (for example, the `ulimit -u` setting on Linux, or the `pid_max` limit).

##### A Modern Solution: Virtual Threads
If your architecture relies on spawning thousands of threads that spend most of their time waiting (e.g., waiting for database queries or network responses to return), you are exactly the target audience for **Java Virtual Threads** (introduced in Java 21 via Project Loom).

Virtual threads are managed entirely by the JVM, not the OS. They take up mere bytes of memory instead of megabytes. When a virtual thread "waits," the JVM simply parks it on the heap and uses the underlying OS thread to run a different virtual thread. This allows you to spawn millions of concurrent threads without ever hitting the native OS thread limit or running into an `OutOfMemoryError`.

### Computer vs Platform vs Virtual Threads
##### 1. Computer Threads (Native / OS Threads)
At the lowest level, a computer thread (or OS thread) is the fundamental unit of CPU utilization managed by the Operating System's scheduler.

- **How it works:** The OS allocates a portion of CPU time to each thread. When the OS switches execution from one thread to another, it performs a **context switch**. This requires saving the current thread's state (registers, program counter) into a Thread Control Block (TCB) and loading the state of the next thread.
- **The Cost:** Native threads are heavyweight. They require a transition into the kernel to create or destroy. More importantly, they consume a significant amount of memory for their call stack (typically 1MB per thread on 64-bit systems).
- **The Bottleneck:** Because of this memory overhead and the CPU cost of context switching, a standard server can only support a few thousand active OS threads before exhausting RAM or spending more CPU cycles switching threads than actually executing code.

##### 2. Platform Threads
Platform threads are the traditional abstraction provided by a language runtime (like the JVM) to interact with OS threads. In Java, whenever you use standard `java.lang.Thread` or an `ExecutorService`, you are using platform threads.

- **The Mapping:** A platform thread acts as a thin wrapper around a native OS thread. They have a **1:1 relationship**. When you create a platform thread, the OS creates a native thread.
- **The Blocking Problem:** In a typical web application architecture (the "thread-per-request" model), a thread handles an incoming HTTP request, makes a database call, and waits for the response. While waiting for that I/O operation, the platform thread is **blocked**. Because it maps 1:1 to the OS thread, the underlying OS thread is also blocked and cannot do any other work.
- **The Reactive Workaround:** Historically, to handle massive concurrency without exhausting the OS thread limit, developers had to adopt **reactive programming** (like Spring WebFlux, RxJava, or Project Reactor). This allowed a small pool of threads to handle thousands of requests asynchronously, but it came at the cost of highly complex, callback-heavy code that is notoriously difficult to debug and trace.

##### 3. Virtual Threads
Virtual threads represent a paradigm shift in how concurrency is handled, most notably introduced in Java 21 via Project Loom. They allow you to write standard, synchronous, blocking code, but achieve the massive scalability of reactive programming.

- **The Mapping:** Virtual threads have an **M:N relationship** with OS threads. Millions of virtual threads (M) can be scheduled and executed on a small pool of standard platform threads (N), which are referred to as "carrier threads."
- **How it works:** Virtual threads are managed entirely by the JVM, not the OS. They are incredibly lightweight, taking up only a few hundred bytes on the heap rather than a megabyte of contiguous RAM.
- **The Magic of Unmounting:** When a virtual thread makes a blocking call (like fetching data from Apache Cassandra or an external microservice), the JVM does not block the underlying carrier thread. Instead, it "unmounts" the virtual thread, saving its state to the heap. The carrier thread is instantly freed up to execute a completely different virtual thread. Once the I/O response returns, the original virtual thread is placed back in a queue to be remounted and resume execution.

> [!SUMMARY]
> 
> | **Feature**          | **Computer (OS) Threads**           | **Platform Threads**                | **Virtual Threads**                      |
> | -------------------- | ----------------------------------- | ----------------------------------- | ---------------------------------------- |
> | **Managed By**       | Operating System                    | Language Runtime (1:1 with OS)      | Language Runtime (JVM)                   |
> | **Memory Footprint** | Heavy (~1MB stack)                  | Heavy (~1MB stack)                  | Very Light (Bytes on the Heap)           |
> | **Creation Cost**    | High (Requires OS kernel call)      | High                                | Extremely Low                            |
> | **Scale**            | Thousands                           | Thousands                           | Millions                                 |
> | **Best Used For**    | Core OS processes, heavy CPU bounds | CPU-intensive algorithms, rendering | High-throughput, I/O-bound microservices |
> 