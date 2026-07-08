Garbage Collection (GC) in Java is an automatic memory management process performed by the JVM. It helps in removing unused and unreachable objects from heap memory, which improves memory utilization and application performance. JVM automatically handles object cleanup.

### Basics
- **Object Creation :** Objects are created in the heap memory when a program runs.
- **Reference Checking:** The garbage collector checks whether objects are still referenced by the program.
- **Identify Unreachable Objects**: Objects without any active reference become unreachable and eligible for garbage collection.
- **Memory Cleanup:** The JVM removes unreachable objects and frees heap memory automatically.
- **Reuse of Memory**: Freed memory can now be reused for creating new objects.

An object becomes unreachable if it does not contain any reference to it. There are generally four ways to make an object eligible for garbage collection.
- Nullifying the reference variable (obj = null).
- Re-assigning the reference variable (obj = new Object()).
- An object created inside the method (eligible after method execution).
- Island of Isolation (Objects that are isolated and not referenced by any reachable objects) (More on this below).

#### 1. Minor Garbage Collection

Minor Garbage Collection occurs in the Young Generation of heap memory. It removes short-lived and unreachable objects that are recently created.

- Works on Young Generation memory ([[Java Virtual Machine(JVM)#3. How the Heap is Structured (Setting up for Garbage Collection)|more]]).
- Faster compared to Major GC.
- Removes temporary objects quickly.

#### 2. Major or Full Garbage Collection

Major Garbage Collection occurs in the Old Generation of heap memory. It removes long-lived objects that are no longer referenced.

- Works on Old Generation memory ([[Java Virtual Machine(JVM)#3. How the Heap is Structured (Setting up for Garbage Collection)|more]]).
- Slower than Minor GC.
- Cleans long-lived unused objects.

#### Requesting Garbage Collection

- Once an object is eligible for garbage collection, it may not be destroyed immediately. The garbage collector runs at the JVM's discretion and you cannot predict when it will occur.
- We can also request JVM to run Garbage Collector. There are two ways to do it: first using `System.gc()` and second using `Runtime.getRuntime().gc()`
- Using `System.gc()`: This static method requests the JVM to perform garbage collection.
- Using `Runtime.getRuntime().gc()`: This method also requests garbage collection through the Runtime class.
- Before destroying an object, the garbage collector calls the `finalize()` method to perform cleanup activities. This is deprecated since Java 9.

### Deep Dive 
#### 1. GC Roots (How Reachability is Actually Determined)

An object is collected if it lacks an "active reference." But where do references start? They start at **GC Roots**. The JVM pauses and traces references starting from:

- Local variables in active thread stacks.
- Static variables in loaded classes.
- JNI (Java Native Interface) references.

If the JVM traces the reference tree from these roots and cannot reach an object, that object is marked for death, even if it references other dead objects (the **"Island of Isolation"**).

#### 2. The Mark-and-Sweep Algorithm (How Memory is Reclaimed)

This is how memory is actually reclaimed.

- **Mark:** The GC traverses the object graph starting from GC Roots, flipping a boolean "alive" bit on every object it can reach.
- **Sweep:** It scans the heap memory from beginning to end. Any object without the "alive" bit is overwritten.
- **Compact (Crucial):** If you just delete objects, memory becomes heavily fragmented (like a Swiss cheese block). Modern GCs push all the surviving objects together to one end of the heap, ensuring contiguous free space for new, large objects.

#### 3. Minor GCs - Eden, Survivor Spaces, and Promotion

The Young Generation is actually divided into **Eden**, **Survivor 0 (S0)**, and **Survivor 1 (S1)**.

- **Birth in Eden:** All new objects are allocated in the **Eden Space** (part of the Young Gen).
- **Minor GC:** When Eden fills up, a Minor GC is triggered. It stops the world (more on this below), finds the few objects that are still alive, and _copies_ them to **Survivor Space 0 (S0)**. The entire Eden space is then instantly wiped clean. 
- **The Survivor Ping-Pong:** On the next Minor GC, the JVM looks at Eden _and_ S0. It copies all surviving objects into **Survivor Space 1 (S1)**. S0 and Eden are wiped. On the next cycle, it copies from S1 back to S0.
- **Tenuring (Promotion):** Every time an object survives a ping-pong cycle, its "age" increments. Once it hits a threshold (usually 15 cycles), the JVM decides this object is long-lived and moves it to the **Old Generation**.

> [!IMPORTANT]
> When an object is copied from Eden to Survivor Space 0 (S0), **its physical memory address changes entirely**. Consequently, the JVM must track down every single variable, field, and array in your application that was pointing to the old address and update them to point to the new address. The surviving objects are moved to S0, remaining objects in Eden are not deleted, rather the pointer that tells JVM where free space is available simple moves to the beginning, and the objects left in Eden are simply overridden when a new object is created.

#### 4. Stop-The-World (STW) Pauses

This is the most important concept for **application latency**. When the Garbage Collector runs, it has to freeze all application threads so that objects don't move around or change references while the GC is trying to count them. This is called a **Stop-The-World pause**. A major goal of modern Java engineering is tuning the JVM to minimize the length of these STW pauses so users don't experience lag spikes. This is primarily caused due to **Major GCs**.

#### 5. Major GCs
Eventually, the Old Generation fills up with promoted objects. This triggers a **Major GC** (or Full GC).

Because the Old Generation is large and full of objects that actually _are_ alive, the GC cannot just bulldoze it. It must use a **Mark-Sweep-Compact** algorithm:

1. **Mark:** Traverse the entire object graph and mark what is alive.
2. **Sweep:** Delete the dead objects.
3. **Compact:** Slide all the living objects together to prevent memory fragmentation.

Compacting a massive Old Generation requires shifting gigabytes of data in memory and updating all the pointers. So the GC has to freeze all application threads so that objects don't move around or change references while the GC is trying to count them. This causes long **Stop-The-World (STW)** pauses, which is what causes sudden latency spikes or API timeouts in production.

#### 5. The GC Implementations

As an engineer, you configure the JVM to use a specific GC algorithm based on your system's SLA.

##### A. Parallel GC (The Throughput King)

- **How it works:** It uses multiple threads to perform both Minor and Major GCs. When a GC happens, it stops the application completely and throws maximum CPU power at cleaning the heap as fast as possible.
- **The Trade-off:** High total throughput, but unpredictable and potentially long STW pauses.
- **When to use it:** Batch processing jobs, background data crunching, or offline reporting where user latency doesn't matter, but overall processing speed does.

##### B. G1 GC (Garbage-First) - _The Modern Standard_

- **How it works:** Default since Java 9. Instead of a massive monolithic Young and Old generation, G1 divides the heap into thousands of small, equal-sized **Regions** (e.g., 2MB each). Some regions act as Eden, some as Old.
- **The Magic:** G1 tracks which regions contain the most garbage. When it pauses the application, it targets _only_ the regions that will yield the most free memory in the shortest time (Garbage First). You can give G1 a soft latency target (e.g., `-XX:MaxGCPauseMillis=200`), and it will try to adjust its region collection to never pause longer than 200ms.
- **When to use it:** Most standard microservices, REST APIs, and monolithic web applications.

##### C. ZGC (Z Garbage Collector) - _The Bleeding Edge_

- **How it works:** Introduced as production-ready in Java 15. ZGC is a highly scalable, concurrent collector. It performs the expensive Mark and Compact phases _concurrently_ while your application threads are still running. It achieves this using "Colored Pointers" (storing metadata in the memory address itself) and "Load Barriers" (intercepting the application when it tries to access an object that is currently being moved).
- **The Magic:** STW pauses are strictly **under 1 millisecond**, even on massive 16-Terabyte heaps.
- **When to use it:** Ultra-low latency requirements, reactive microservices, high-frequency trading, or systems with massive memory caches (like Cassandra or Kafka nodes).

One of the major reasons why STWs take so long is because GCs have to update object references before resuming. ZGC somewhat circumvents this by allowing reference updates on the fly by only stopping thread trying to access the object being moved.

> [!NOTE]- Colored Pointers. Skippable. Click to expand.
> Modern CPUs do not actually use all 64 bits to address memory. Depending on the architecture, they typically only use the first 42 to 44 bits, which is still enough to address up to 16 Terabytes of RAM. This leaves about 20 bits at the top of the pointer completely unused.
> 
> Instead of putting Garbage Collection metadata (like whether an object is alive or has been moved) inside the object's header in the Heap, **ZGC stores this metadata directly inside those unused bits of the memory address itself.** These metadata bits are called "colors."