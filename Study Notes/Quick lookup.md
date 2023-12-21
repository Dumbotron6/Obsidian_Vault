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

## Algorithms
##### Two pointers, In place algorithm, Counter or Flagging
[[Algorithms/Arrays#Two pointer approach|Two pointers]]
[[Algorithms/Arrays#In place algorithms|In place]]
[[Algorithms/Arrays#Counters or Flagging approach|Flagging]]
##### Fast and slow pointers
In a cyclic linked list, two pointers can be used, with one moving faster than the other (ex. `first=first.next; second=second.next.next;`). In a cyclic linked list, the fast pointer will eventually and always catch up to the slow pointer.