# What is system design?

System design is the process of defining an architecture for a software system, which could be a news feed, Google search, chat system, etc.

# Basic setup

Let us consider a single server setup.

![[Attachments/image_1.png]]

* Web server is where our application is hosted. It contains the HTML pages or backend code.
* Domain names are how the user accesses the website.
* DNS stands for ==**Domain Name Systems**==. They are paid services provided by third parties. Web servers are accessible via IP (Internet Protocol) addresses. A DNS provides a human readable name (domain) to the user. For example, www.google.com will have an IP address which will be given to the user when the web browser contacts the DNS.
* ==**Hypertext Transfer Protocol**== (HTTP) requests are sent to the web server from the web browser after obtaining the IP address.

The basic flow is as follows
1. User types in a url in the browser. The browser contacts the DNS for the IP address of the url.
2. Browser now directly sends the HTTP request to the IP address of the web server where the application is hosted.
3. The application processes the requests and sends back a HTML page or a JSON response.

Traffic to web servers come from two sources.
Web applications use a combination of server side languages (Java, Python, etc.) to handle business logic, storage, etc., and client side languages (HTML, Javascript) to present the data
Mobile applications use HTTP protocols for communication and commonly uses ==**JavaScript Object Notation**== (JSON) as the response format.

As the application grows, we use two servers and use them for different purposes. We setup one server to handle the web/mobile traffic (web tier) and one for the database (data tier). The web tier handles the business logic and processing of data, the data tier is responsible for actually storing the data in the database.

![[Attachments/image_2.png]]

### Database types

There are two types of databases- ==**Relational (SQL) and Non-relational (NoSQL)**==.
Relational databases or ==Relational Database Management System (RDMBS)== are the traditional databases (like MySQL, Oracle database, etc.) where data is stored in rows and columns in tables. Join operations can be performed across the tables.
Non-relational databases (like Cassandra, Amazon DynamoDB) do not generally support joins across tables. Data is stored in four categories: key-value stores, graph stores, column stores, and document stores.

Non-relational databases are preferred if:
* The application requires super-low latency.
* our data are unstructured, or you do not have any relational data.
* You only need to serialize and deserialize data (JSON, XML, YAML, etc.).
* You need to store a massive amount of data.

## Scaling

As our application grows and the number of users increase, the traffic and data will also increase. Our web server will not be able to handle all that load. To enable smooth running of the application, we need to scale it. There are two types.

### Vertical scaling
Vertical scaling aka scale-up means the process of adding more power (CPU, RAM, etc.). This is good when traffic is low. It has two main disadvantages.
1. Memory and CPU cannot be increased after a certain point in a single server.
2. There is no failover and redundancy. If the server goes down, the application goes down with it.

### Horizontal scaling
Horizontal scaling aka scale-out means adding more servers. For large scale applications, this is preferred. The advantage if one server goes down, the entire application does not go down. If many users access the website simultaneously, the webserver will reach it's load limit and the user will experience slowness or fail to connect entirely. Having multiple servers help mitigate this problem. This is done using load balancer.

#### Load balancer

A load balancer evenly distributes incoming traffic among webservers where the applications are hosted.

![[Attachments/image_3.png]]

When a load balancer is setup, the DNS provides the IP address of the load balancer for the application. This IP is known as the public IP which is given to the user. With this setup, the webservers can not be reached by the client directly. The load balancer contains the private IP of the web servers. A private IP is an IP address reachable only between servers in the same network; however, it is unreachable over the internet. The load balancer communicates with web servers through private IPs.

With this setup, the failover issue is solved.
* If server 1 goes down, the load balancer redirects all traffic to server 2.
* If the traffic is high, more number of web servers need to be added to the web server pool. This way, the load balancer can distribute the high traffic between the servers.

#### Database replication

Although failover for the webserver is handled via a load balancer, the same cannot be said for the database. To handle it, database replication has to be done.

Essentially, data is replicated across multiple databases with a master/slave or leader/follower relationship.

![[Attachments/image_4.png]]

A master database generally only supports write operations. A slave database gets copies of the data from the master database and only supports read operations. All the data-modifying commands like insert, delete, or update must be sent to the master database. Most applications have a higher number of reads compared to writes. So the number of slave databases is usually higher than the master.

This setup has a few advantages:
* Better performance: Since the write operations happen in master DB and the read operations are done across multiple slave DBs, more queries can be processed in parallel.
* Reliability: If one DB is destroyed, the data is still preserved as the other DBs are stored across multiple locations.
* High availability: If one DB goes down, the application does not go gown.

If there is only one slave DB and it goes down, the master temporarily takes over the read operations until the slave is restored. If there are multiple slaves, the queries are redirected to the healthy slave DBs.

If the master goes down, one of the slave DBs is promoted to master temporarily and a new slave DB will replace the old one for data replication immediately. Promotion from slave to master is complex in production systems as the data is the slave DB might not be up to date with that of the master. The missing data needs to be updated by running data recovery scripts. #lookupmore 

![[Attachments/image_5.png]]

>[!summary]
> * A user gets the IP address of the load balancer from DNS.
> * A user connects the load balancer with the public IP address.
> * The HTTP request is routed to either Server 1 or Server 2.
> * A web server reads user data from a slave database.
> * A web server routes any data-modifying operations to the master database. This includes write, update, and delete operations.

## Caching

A cache is a temporary storage area that stores the result of expensive responses or frequently accessed data in memory so that subsequent requests are served more quickly. If an application access the database frequently, the performance is affected.

A *cache tier* is a temporary data store layer. When the web server needs data, it checks the cache first. If the data exists there, it is returned to the web server. If data does not exist, the database is accessed to fetch the data and returned to the web server and also added to the cache. This caching strategy is called *read-through cache*. There are other strategies. #lookupmore 

### Considerations

* Data that is read frequently but modified infrequently needs to be stored in cache. Cache data is stored in volatile memory ([[../Quick lookup#Volatile memory|^]]) so it is not recommended to store persistent data ([[../Quick lookup#Persistent data|^]]). It the server restarts, all data in the cache is lost.
* Expiration policy: Expiration date needs to be set for the data that is stored in cache. This will help in obsolete data being stored in cache.
* Consistency: This involves keeping the data store and the cache in sync. Inconsistency can happen because data-modifying operations on the data store and cache are not in a single transaction.
* Failures: Multiple cache servers across different data centers are recommended to avoid single point of failure or SPOF ([[../Quick lookup#Single Pont of Failure or SPOF|^]]).
* Eviction Policy: Once the cache is full, any requests to add items to the cache might cause existing items to be removed. This is called cache eviction. Least-recently-used (LRU) is the most popular cache eviction policy. Other eviction policies, such as the Least Frequently Used (LFU) or First in First Out (FIFO), can be adopted to satisfy different use cases.

## Content delivery network (CDN)

A CDN is a network of geological locations that is used to store and deliver static content such as HTML, JavaScript, etc. CDN servers cache static data.
Dynamic content caching is also possible. #lookupmore 

When a user visits a website, a CDN server closest to the user will deliver static content. The farther away the user is from the CDN, the slower the website will load. Similar to cache, if the data is not present in the CDN, it requests it from the origin i.e., the web server. The data includes which includes optional HTTP header *Time-to-Live (TTL)* which describes how long the image is cached.

### Considerations

* CDNs are run by third-party providers, and you are charged for data transfers in and out of the CDN.
* Cache expiry: The cache expiry time should neither be too long nor too short. If it is too long, the content might no longer be fresh. If it is too short, it can cause repeat reloading of content from origin servers to the CDN.
*  CDN fallback: If there is a temporary CDN outage, clients should be able to detect the problem and request resources from the origin.
*  Invalidating files: You can remove a file from the CDN before it expires by performing one of the following operations:
	* Invalidate the CDN object using APIs provided by CDN vendors.
	* Use object versioning to serve a different version of the object. To version an object, you can add a parameter to the URL, such as a version number. For example, version number 2 is added to the query string: image.png?v=2.

>[!todo] Advantages
>1. Static assets (JS, CSS, images, etc.,) are no longer served by web servers. They are
fetched from the CDN for better performance.
>2. The database load is lightened by caching data.

## Stateless architecture #lookupmore 

#reference [Stateful vs Stateless](https://www.spiceworks.com/tech/cloud/articles/stateful-vs-stateless/)

### Stateful

Stateful architecture or application describes a structure that allows users to store, record, and return to already established information and processes over the internet. It entails transactions that are performed using past transactions as a reference point. In stateful applications, the current transaction can be affected by the previous ones.
It is like a conversation where the next dialogue can be built upon the previous.

A stateful architecture maintains the state of every session. Here, the state (such as session data) is stored in the web tier. So each user state is stored in only one server. If the client sends a request to another server, it will fail. Adding or removing servers becomes an issue here.

![[Attachments/image_6.png]]

### Stateless

In this stateless architecture, HTTP requests from users can be sent to any web servers, which fetch state data from a shared data store (which will be a persistent data store). State data is stored in a shared data store and kept out of web servers. A stateless system is simpler, more robust, and scalable. NoSQL databases are commonly used for this purpose as it is easy to scale.

![[Attachments/image_7.png]]

After the state data is removed out of web servers, auto-scaling of the web tier is easily achieved by adding or removing servers based on traffic load.

![[Attachments/image_8.png]]

## Data centers

As a website grows and the number of users increase, high availability is important. For this purpose, data centers are needed. Data centers are geographically separated areas with clusters of webservers.

When a request is sent from the client, the request is geoDNS-routed, also known as geo-routed, to the closest data center. geoDNS is a DNS service that allows domain names to be resolved to IP addresses based on the location of a user.

There are several important points to keep in mind.
* Traffic redirection: GeoDNS can be used to direct traffic to the nearest data center depending on where a user is located.
* Data synchronization: Users from different regions could use different local databases or caches. In failover cases, traffic might be routed to a data center where data is unavailable. A common strategy is to replicate data across multiple data centers.
* Test and deployment: With multi-data center setup, it is important to test your website/application at different locations. Automated deployment tools are vital to keep services consistent through all the data centers

>[!important]
>With so many components in place, it is important that we decouple([[../Quick lookup#Tightly and Loosely coupled or Decoupled|^]]) them out of the system so that they can be scaled independently.

## Message queue

