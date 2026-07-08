## System Design
###### Volatile memory
Temporary memory. Data is not written by the application anywhere. If the server is restarted, the data is lost.

###### Persistent data
Data that needs to be stored. It is usually inserted into a database.

###### Single Pont of Failure or SPOF
A single point of failure (SPOF) is a part of a system that, if it fails, will stop the entire system from working.

###### Tightly and Loosely coupled or Decoupled
When systems are tightly coupled, one is highly dependent on the other. When one is modified or scaled, it cannot be done so without doing the same to the other.
When systems are loosely coupled or decoupled, the systems can operate independent of each other. Failure of one does not lead to failure of the other. They can be independently modified or scaled.

###### Synchronous and Asynchronous
Synchronous means in sequence. For example, the second operation cannot be done without completing the first.
Asynchronous means in parallel. For example, the second operation does not wait for the first to complete. The second starts regardless of the state of the first.

###### Database sharding
Sharding is the process of splitting data into several shards in the database. Each shard has its own unique data stored using unique id, for example employee id. A hash function (for example employee_id % 4) is used to determine which shard to store the data in.

###### GeoDNS
GeoDNS is a technology that is an implementation of DNS. It is used to identify geographically nearest CDN.

###### Global Server Load Balancing(GSLB)
It is a type of Load Balancer that operates at the DNS level. It distributes traffic between geographically separate data centers. It's different from a regular load balancer, which distributes traffic within a single data center to various web servers. GSLB is a more advanced superset of GeoDNS. GSLB considers server health and performance in addition to just geography.

## Algorithms
##### Two pointers, In place algorithm, Counter or Flagging
[[Algorithms/Arrays#Two pointer approach|Two pointers]]
[[Algorithms/Arrays#In place algorithms|In place]]
[[Algorithms/Arrays#Counters or Flagging approach|Flagging]]
##### Fast and slow pointers
In a cyclic linked list, two pointers can be used, with one moving faster than the other (ex. `first=first.next; second=second.next.next;`). In a cyclic linked list, the fast pointer will eventually and always catch up to the slow pointer.

## Java
##### Thread scheduler
When you run a program with multiple threads (tasks running at the same time), they all want CPU time to execute. However, a CPU can only do so much at once. The **Thread Scheduler** is the part of the Java Virtual Machine (JVM) that decides _which_ thread gets to run at any given moment and _how long_ it gets to run. It handles state transitions and order of execution.

###### 1. Which Thread to Run? (Priority & Preemption)
Every thread in Java has a **priority**, which is a number typically ranging from 1 (lowest) to 10 (highest). By default, a thread inherits the priority of the thread that created it.

- **The Rule:** The scheduler will almost always prefer to give CPU time to the thread with the **highest priority** that is ready to run.
- **Preemption:** If a low-priority thread is currently running, and a high-priority thread suddenly becomes ready (for example, it wakes up from a nap), the scheduler will often **kick out** the low-priority thread immediately to let the high-priority one run.

###### 2. How Long Does It Run? (Time Slicing)
What happens if there are five threads that all have the exact same priority? They all want to run, and they are all equally important.

To handle this, the scheduler uses a mechanism called **Time Slicing** (or Round-Robin scheduling).

- **The Rule:** The scheduler divides CPU time into tiny fractions of a second, called **timeslices** or **quanta** (often just a few milliseconds).
- It gives Thread A one timeslice, then forces it to pause. Then it gives Thread B a timeslice, pauses it, gives Thread C a timeslice, and so on. It cycles through them so rapidly that to you, the human watching the screen, it looks like all the tasks are running at the exact same time.