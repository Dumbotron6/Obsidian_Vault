NoSQL is a flexible approach to Database management.

1. Key-Value
2. Document
3. Tabular
4. Graph

NoSQL is non-relational, distributed and scalable.

Distributed- Running in a cluster of machines located at different locations.

Scalable- Able to store and query large amount of data and support high transaction throughput.

Partition tolerant- Able to work in presence of networked partitioning.

Highly available- Able to process requests even when some machines go down. Possible because of inbuilt data replication.

![Untitled](Attachments/Untitled.png)

A.C.I.D

SQL follows relational model- A collection of rows and columns that store specific set of structured data.

NoSQL uses both relational and non relational.

![Untitled](Attachments/Untitled%201.png)

When developing an application, a lot of time is spent on mapping in memory data structures and a relational database(SQL).

When large data needs to be processed, it is easier to do so from small clusters(which NoSQL makes use of) than a single large data storage. 

![Untitled](Attachments/Untitled%202.png)

Fixed schema- Cannot store boolean in place of an integer. This has it’s own plus and minus.

Scaling up- Adding a more powerful CPU/ increasing RAM to handle increased workload.

Scaling out- Adds more machines(servers) to resource pool.

![Untitled](Attachments/Untitled%203.png)

A NoSQL DB cannot achieve all three(?). Cassandra focuses on availability and partition tolerance. MongoDB and Kafka focuses on consistency and partition tolerance.

# Tabular

![Untitled](Attachments/Untitled%204.png)

UUID- Universal Unique Identifier. (A 128 bit label used for information- look it up)

![Untitled](Attachments/Untitled%205.png)

The UUID is the **Partition key.** As NoSQL is a distributed DB, data is stored in loads of different nodes. If two rows have same partition key, they’ll be stored together in one node.

Keyspace is a word for a logical grouping of tables.

![Untitled](Attachments/Untitled%206.png)

![Untitled](Attachments/Untitled%207.png)

![Untitled](Attachments/Untitled%208.png)

A Primary key is how we filter through the data to look for our required row.

A Primary key is a combination of one or more partition keys and zero or more clustering keys.

The above example uses one partition key and the below example uses composite partition key(i.e multiple).

![Untitled](Attachments/Untitled%209.png)

A partition key is for data placement apart from uniquely identifying the data and is always the first value in the primary key definition.

![Untitled](Attachments/Untitled%2010.png)

![Untitled](Attachments/Untitled%2011.png)

By default, data is stored in ascending order of clustering keys. That can be changed.

![Untitled](Attachments/Untitled%2012.png)

![Untitled](Attachments/Untitled%2013.png)

PRIMARY KEY(app_name, env, hostname) makes app_name as partition key and env, hostname as clustering keys.

PRIMARY KEY( (app_name, env), hostname) makes app_name, env as partition keys and hostname as clustering key.

![Untitled](Attachments/Untitled%2014.png)

Note on above: ALLOW FILTERING is used when not all partition keys are specified while filtering. Ex. select * from extable where b=’1’ allow filtering; Here, the partition key a is not specified.

#reference [Cassanda keys](https://www.baeldung.com/cassandra-keys), [Primary Key, Partition Key, Clustering Key - Part One - Scylla University](https://university.scylladb.com/courses/data-modeling/lessons/basic-data-modeling-2/topic/primary-key-partition-key-clustering-key/)

#lookupmore  indexing in cassandra.