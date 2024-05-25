### Networking

Amazon Virtual Private Cloud (VPC) lets us provision logically isolated sections of AWS cloud where we can launch AWS resources. These resources can be public facing with access to the internet or private facing with no internet access usually for backend services like databases or application servers. This helps ensuring that the end users don’t get access to the backend.

These public and private groupings are known as subnets with a range of IP addresses in the VPC.

VPC allows us to define a range of IP addresses for our AWS resources like EC2 instances and ELBs.

Subnets are chunks of IP addresses that we define in out VPC.

We can control what traffic gets into the VPC. Some VPCs might have internet facing resources that are publicly available for anyone to access like a website. There might also be resources like a backend database that should only be reachable if someone is logged into our private network.

So customer facing websites can be in a public subnet while a database that has customer information can be in a private subnet.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2057.png)

An Internet Gateway (IGW) is required for public traffic to flow into and out of the VPC.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2058.png)

A Virtual Private Gateway allows us to create a VPN connection to establish a private network (which is approved for access to the VPC) through which traffic can flow.

VPNs are private and encrypted (meaning more security- it protects our traffic from other requests around it) but still use regular internet connection to access the VPC. So it is still bogged down by regular internet traffic- bandwidth is shared by many people using the internet. We need a shared connected that is dedicated and not used by anyone else so that we can get low latency with high security. This can be done using AWS DirectConnect. It establishes a dedicated and private fiber connection from our data centers.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2059.png)

A VPC can have various types of gateways for different types of subnets with resources.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2060.png)

AWS can cover all of the above. Distributed denial of service prevention is DDOS prevention.

IGWs exist in VPCs to control access to subnets. Public subnets have access to internet while private subnets do not.

Subnets can control traffic permissions. Packets are messages or units of data from the internet. Every packet that crosses the subnet is checked against a Network Access Control List (Network ACL). It is a virtual firewall.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2061.png)

ACL checks if the packet has permission (credentials) to enter or leave the subnet based on who it was sent from and how it is trying to communicate. Network ACLs check traffic going into and out of the subnet. ACLs can have deny rules to compare against and deny access. By default, ACLs allow all traffic.

While ACL checks traffic at subnet level, it does not check at EC2 instance level. Each EC2 might have its own rules for who can send and what port messages are allowed to be sent to. Instance level network security is controlled via Security Groups. Each EC2 has it’s own Security group. By default, all ports and IP addresses sending packets are blocked.

We can control what type of message each security group can accept. In the case of a website, we need to allow web based traffic or HTTPS messages to be sent through the instance and not OS or Admin requests. By default, the traffic entry is checked but not the traffic exit. 

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2062.png)

The difference between Security group and Network ACL is that security group is stateful while ACL is stateless.

Stateful- It has a memory of who to allow in or out.

Stateless- No memory but checks all packets that cross subnet border.

As an example, take EC2 A and EC2 B instances each within their own subnets. SG-A checks the leaving packet but remembers it. ACL-A checks the packet and approves it. ACL-B checks it and lets it in. SG-B checks and lets it in to EC2 B. Now the return packet is not checked by SG-B but checked by ACL-B and ACL-B. However, this time, SG-A remembers the packet that it sent out and doesn’t check the return packet. This is the difference between stateful and stateless.

Both network ACLs and security groups enable us to configure custom rules for the traffic in your VPC.

Domain Name Service (DNS) is like a translation service that translates website names into Internet Protocol (IP) addresses that computers can read. Amazon Route 53 is a DNS service. When a website that is hosted on AWS is entered in the browser, it connects to Route 53 to obtain the IP address of the website and it routes to that address.

DNS resolution involves a customer DNS resolver communicating with a company DNS server.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2063.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2064.png)

Route 53 can also be used to register, buy and manage domain names on AWS.

Route 53 provides IP address to direct to the nearest edge location deployed by CloudFront for low latency data access.

## Storage

Applications often need Block level storage. This type of storage is like a place to store files- files being a series of bytes that are stored in blocks on disk. When a file is updated, only that block is updated. This works for databases, enterprise software or file systems.

When an EC2 instance is launched, it might provide a local storage called Instance Store Volumes. These are attached to the host (Hypervisor AWS host) that EC2 instance runs on top of. When the instance is stopped or restarted, the data is deleted. When restarted, the instance runs on another host so previous instance store volume is not present.

Amazon Elastic Block Store (EBS) is a virtual hard drive that provides volumes that is not directly tied to the host. We can attached them to the EC2 instance. So data written to EBS volume can persist between starts and stops of EC2 instances. We define the size, type and configurations of the EBS volume and attach it to the instance. Now EC2 can be configured to write to the volume.

EBS allows incremental backups of the data called Snapshots. We can take regular backup.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2065.png)

Amazon Simple Storage Service (Amazon S3) is a data storage that allows us to store and retrieve an unlimited amount of data with a limit of 5TB per object/file.

Data is stored as objects and in buckets. For example, a file is the object and file directory is the bucket. Objects can be versioned to prevent accidental deletion. Meaning, we always retain the previous version of the object.

Multiple buckets can be created and stored across various classes or tiers of data. We can create permissions for the objects between different tiers like visibility, access etc. Tiering allows for different used cases like data that needs to be accessed frequently and long term retention data that is used for auditing.

Tiers-

Amazon S3 Standard- 99.9% durability meaning it has that much probability of data remaining intact after one year. AWS can sustain concurrent loss of data in 2 storage facilities (AZs) as it is stored in at least 3 (2 copies of data).

S3 standard can host static websites- HTML files. HTML files and static web assets can be stored into a bucket and designated as a static website. Bucket URL can be entered to access the website.

Amazon S3 Standard-Infrequent Access (S3 Standard-IA) is used for data that is accessed less frequently but requires rapid access when needed. So backups can be stored in this tier.

Amazon S3 Glacier is a class or tier that allows us to retain data for several years for auditing purposes that we don’t require to be accessed rapidly. S3 Glacier can be used for data archival. We can just move data to Glacier or create vaults and populate them with archives. Glacier Vault Lock Policy can be used to define terms for long term data storage like compliance requirements(?). We can specify controls such as Write once/Read many (WORM) in a vault lock and lock it from future edits.

We have 3 options for retrieval which range from minutes to hours(?). Data can be uploaded directly to Glacier or using S3 Lifecycle Policies. It is a policy that can be created to move data automatically between tiers. For example, data has to be stored in S3 Standard in 30 days and in S3 IA for 90 days and afterwards to S3 Glacier. With Lifecycle policies, we can create that configuration.

There are also a few other tiers.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2066.png)

Changes to the object are identified using the unique identifier. Amazon S3 One Zone IA is like S3 IA but data is only stored in one IA. S3 Intelligent Tier automatically moves data between tiers based on frequency of access. Glacier Instant Retrieval is used to for instant access of archived data. Glacier Flexible Retrieval has slower retrieval of data. Glacier Deep Archive has the slowest data retrieval speed with time ranging from 12 to 48 hours. The price differs between all tiers due to different access frequencies and retrieval speeds.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2067.png)

Serverless means no EC2 instances are needed.

For a websites where customers uploads a picture and similar animal pictures are shown, many files need to be stored, indexed and retrieved instances. S3 is convenient in this case.

Object storage- any file that is edited is reuploaded entirely. Block storage stores the files in blocks, so only the edited part is reuploaded. For example, a 80 GB video file being edited(?).

For editing the 80 GB video file EBS is convenient. So for complete objects or occasional changes, S3 is perfect. For complex read, write or change operations, EBS is perfect.

Amazon Elastic File System (EFS) is a managed file system. It is used for storing files that need to be shared. For example, multiple servers run analytics on large data stored in a shared file system. Traditionally, it is hosted on premises and scaling and replication has to be handled. EFS does that. Multiple instances can access data in EFS at the same time.

EBS vs EFS

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2068.png)

EBS is like a hard drive where databases can be stored or applications can be hosted.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2069.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2070.png)

Note: A managed system is a system where things like infrastructure, OS, patching, backups etc are handled be a service rather than manually doing them.

A Relational Database Management System (RDBMS) is best used to store data when there is a relation between various tables such as Customer and Customer subscriptions. AWS supports various RDBMS such as MySql, PostgreSQL, Oracle, Microsoft SQL server etc. Usually, these are stored in on premises data centers. These can be migrated to cloud. This is called Lift-and-shift migration. DB is migrated and run on EC2. We can control the OS, memory, CPU, storage capacity etc. Database migration servers can also be used to migrate the data.

Amazon Relational Database Servers (Amazon RDS) is a managed service. It supports all major database engines (like MySql).

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2071.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2072.png)

Data can be migrated/deployed to Amazon Aurora. It is a managed relational database. Data replication factor is 6 at minimum. It also has continuous backup to S3 and point-in-time recovery.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2073.png)

Amazon DynamoDB is a serverless database meaning we don’t need to manage the underlying instances or infrastructure powering it. We just create tables. Data is organized into items and items have attributes. Coffee is the item and ingredients are the attributes. DynamoDB manages the underlying storage no matter the number of items in the table. Auto-scaling happens.

Relational databases require a well defined schema such as tables and their relations. The schema is also rigid (types cannot be changed). They use Structured Query Language (SQL). For datasets that are less rigid and accessed at a very high rate, they are not optimal. The best approach would be to use Non relational databases (NoSQL). DynamoDB is a non relational database. NoSQL databases have simple schemas without tables relating to each other.

The attributes may vary between each item (some attributes may or may not be present or have different data types) so complex queries cannot be run. Instead we run queries a small set of attributes designated as keys. Due to this, the queries are simpler and joins cannot be used. So response time is quick and it is highly scalable.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2074.png)

See notes on Cassandra for more details.

Historical analytics is an important part of any business process. Any traditional relational database can be overwhelmed when we run try to access historical data with multiple joins across various tables. This is also in part due to data already being read and written constantly.

For historical analytics, data is best stored in data warehouses. For example, how many candies were sold in the last hour is historical as the data is fixed. How many candies are currently left in the inventory is operational analytics as that data is constantly changing. Historical essentially means fixed data and not necessarily the timeframe. Historical data is used for Business Intelligence (BI) solutions.

Amazon RedShift is data warehousing as a service. RedShift nodes have massive sizes ranging from TB to PB (Peta Bytes). It is also very fast for querying.

Amazon Database Migration Service (DMS) helps migrate data from on premises databases to cloud. The source database remains fully operational during the migration. Source and target databases don’t have to be of the same type.

Migration between same types of databases like MySql to Amazon RDS MySql are called homogeneous. The source can be on on premises, EC2 instance or RDS. Target can be EC2 or RDS. Here, the schema structures, data types and database code is same so migration is easier. We just create a migration task using DMS and start it.

When source and target are of different types, it is called heterogeneous. Schema structures, data types and database code are different. So first we need to convert them using AWS schema conversion tool. This will match the schema structure and database code. The next step is to create and start a migration task.

Other used cases:

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2075.png)

Test migration is when we want to copy production data to test or debug.

Consolidation is consolidating data from multiple sources into one central database.

Replications is copying data to multiple targets for disaster recovery, geographical data separation etc.

Amazon DocumentDB is a content management system. It is compatible with MongoDB. It can be used to store documents, catalogues, user profiles etc(?).

Amazon Neptune is a graph database. Best used case would be for a social network- who is connected to who i.e highly connected datasets. Also can be used to recommendation engine and fraud detection(?).

Amazon Managed Blockchain is for blockchain solutions. Blockchain is a distributed ledger system that lets multiple parties run transactions and share data without a central authority(?).

Amazon Quantum Ledger Database (Amazon QLDB) is for storing immutable data like bank records or supply chain(?). Data cannot be changed or removed.

Database accelerators. Caching layer on top of databases can improve read times from milliseconds to microseconds. Amazon ElastiCache is used for this. It supports two types of data stores: Redis and Memcached. Amazon DynamoDB Accelerator (DAX) is a native caching layer for DybamoDB.

Continued in [[AWS CP 6-7]]