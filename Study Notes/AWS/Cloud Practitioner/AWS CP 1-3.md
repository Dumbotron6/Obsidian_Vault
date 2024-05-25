Usually, systems operate on a client-server model. Client makes a request and server sends the data. In AWS, the server is called Amazon Elastic Cloud Compute (EC2).

Scaling up is easier in AWS. The number of instances/servers can be increased or decreased on demand in AWS.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2029.png)

When selecting a cloud strategy, a company must consider factors such as required cloud application components, preferred resource management tools, and any legacy IT infrastructure requirements.

The three cloud computing deployment models are cloud-based, on-premises (aka private cloud), and hybrid.

Infrastructure as a Service (IaaS), Platform as a Service (PaaS), and Software as a Service (SaaS) are the three types of cloud computing services.

Iaas is usually computers, networking features and storage. Paas is things like operating system, maintenance and patching. Saas is the actual application or any existing software to assist development.

EC2 runs on top of physical host machines using virtualization technology. One host machine runs multiple instances or virtual machines. A hypervisor running on the host is responsible for sharing the underlying hardware between instances. This is known as Multitenancy. The hypervisor isolates the instances from each other even if they share instances. Networking aspects (such as public or private request being made, accessibility) can also be controlled in EC2.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2030.png)

Giving the instance more memory and CPU capacity on demand is known as Vertical scaling.

Proving servers and scalable virtual machines is known as Compute as service (Caas) model.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2031.png)

General purpose has a balance of compute, memory and networking resources. They can handle diverse work loads like web servers or code repositories.

Compute optimized are used for compute intensive tasks like gaming servers or high performance computing (HPC) or scientific modeling. They can also be used for batch processing many transactions in a single group. **Compute optimized instances** are ideal for compute-bound applications that benefit from high-performance processors.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2032.png)

Memory optimized are used for memory intensive tasks.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2033.png)

Accelerated computing is used for floating point number calculations(?), graphics processing or data pattern matching. They use hardware accelerators.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2034.png)

Storage optimized are used for high performance for locally stored data.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2035.png)

## **Scalability**

**Scalability** involves beginning with only the resources you need and designing your architecture to automatically respond to changing demand by scaling out or in.

AWS provides a functionality called Amazon EC2 auto scaling.

If an instance that is performing an operation fails/ goes down, another instance with similar properties is spun up by EC2 so that there is no interruption in service.

Dynamic scaling responds to rising demand. Predictive scaling automatically schedules right amount of instances based on predicted demand.

Both can be combined to scale faster.

Two ways of scaling- Scaling up and Scaling out.

Scaling up (vertical) - Adding more power to the instance.

Scaling out (horizontal) - Adding more instances to handle requests.

When configuring an auto scaling group, the minimum, maximum and desired number of instances can be specified.

Decoupling means separating each process and allowing them to operate separately. For example, 5 requests is the maximum operation 1 can handle while operation 2 can handle 10. In this case, we need 2 instances for operation 1 and 1 instance for operation 2. Ex. Restaurant order taking and making the order.

## Load Balancing

If there are multiple instances running, performing the same operation, the number of requests coming in must be evenly distributed across instances so that no instance is bogged down by a high number while the others are idle. To manage this, a host is required to count the number of requests at each instance and direct/route the traffic. This is known as load balancing.

There are multiple load balancer applications and we can use any on AWS but we have to manage them- update, manage, scale, handle failover and availability.

AWS provides a service called Elastic Load Balancing (ELB). ELB handles additional throughput as the traffic grows and additional instances are created. The additional instances let ELB know that they are available and ELB distributes traffic accordingly. Once instances scale in, ELB waits for pending requests in the additional instances to end and then sends no more requests to those instances.

ELB can handle both external and internal traffic.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2036.png)

Without load balancer, each new backend instance would have to tell each frontend instance that it exists. ELB solves this by being an intermediary. ELB is regional- a single URL that each instance uses.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2037.png)

## Messaging and Queuing

As an example, if operation 1 takes a request, processes it, and passes it to operation 2. If the passing of the data is direct, it is not efficient. O2 might be unavailable and O1 has to wait till O2 becomes available. In this time, O1 cannot function and the request might be dropped after a time. This is a tightly coupled architecture.

The ideal way would be to place the message(request) on a buffer(a queue) that O2 can start processing when it is available.

In a tightly coupled architecture, if a single component fails, it causes issues for other components of the application fails. If O2 fails, O1 cannot keep the requests indefinitely. The request sent to O2 will fail.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2038.png)

Amazon provides two services- Amazon Simple Queue Service (SQS) and Amazon Simple Notification Service (SNS).

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2039.png)

Data contained within a message is called payload. Messages are placed in SQS without losing them until they are processed.

SNS can be used to send messages between services or send notifications to end users. This is using a Pub-Sub (Publish-Subscribe) model. We can create an SNS topic which is a channel for messages to be delivered. We configure subscribers to that topic and find published messages.

A publisher can publish a message to a topic and there can by multiple subscribers.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2040.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2041.png)

Microservices help ensure that the whole application does not fail when one component goes down. These components might include databases, servers, the user interface, business logic, and so on.

An example of SNS can be newsletters. Customers can receive promos, news and trivia by subscribing to them based on individual preference.

A2A- Application to Application. A2P- Application to Person.

Serverless- We cannot see or access underlying infrastructure or instances. Management (like provisioning, scaling, availability, maintenance) of underlying environment is taken care of by a third party, in this case AWS. No management of instances (or virtual machines) like in EC2. Just code and configuration.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2042.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2043.png)

AWS Lambda is a serverless compute option.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2044.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2045.png)

AWS Lambdas are used to run operations that take less than 15 minutes. When a trigger happens, the code in the lambda runs in a managed environment (we do not worry about availability or scaling). One example operation is on demand expense report processing service.

Containers allow us to manage underlying system unlike lambdas. Amazon Elastic Container Service(ECS) and Amazon Elastic Kubernetes Service(EKS) are container services provided by Amazon. Both are Container orchestration tools.

A container in this case is a Docker container. Docker is a platform that uses operating system level virtualization(?) to deliver software in containers.

A container is a package for our code where we package the application, its dependencies and any configurations that it needs to run(?).

The containers run on EC2 instances and in isolation from each other. This is similar to how virtual machines work(?) but here, the host is an EC2 instance.

When Docker containers run on AWS, we need processes to start, stop, restart and monitor them across multiple EC2 instances together called a cluster.

The process of doing these tasks is called container orchestration.

ECS helps run containerized applications at scale without having to run an orchestration software. EKS does the same thing but with different tools and features.

ECS and EKS can run on EC2 instances run by us or on Amazon Fargate- a serverless compute platform.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2046.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2047.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2048.png)

Container visualization.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2049.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2050.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2051.png)

ECS helps with the above problem.

### Availability

AWS has its data centers in groups called regions. Each region (ex. London) has multiple data centers. Regions are connected to each other using high speed fiber network. Each region is isolated from each other unless explicit permission is granted by the user for data to be moved.

This helps in data being subject to the local laws of the region.

Selection of region depends on four factors- 

1. Compliance (ex. data privacy law)
2. Proximity (higher proximity results in lower latency- time taken for data to be sent)
3. Feature availability (not all data centers have all features available until hardware is installed)
4. Pricing (location based)

An availability zone is a data center or group of data centers with redundant power, networking and connectivity. Running multiple EC2 instances ensures our application/data is in multiple AZs so if one fails, the other can pick up the slack. At least 2 EC2s in 2 AZs in a region is recommended. ELB is a regional construct that manages our EC2s in a region. Regionally Scoped Services are are services like ELB provided at no additional cost(?).

Content Delivery Network (CDN) is a concept of caching or storing a copy of data locally for low latency access. So if the data center is in a far off region, CDN can help in quick access of data.

Amazon’s CDN is called Cloudfront. Cloudfront uses Edge locations to achieve CDN. Data can be pushed from a region to multiple edge locations around the world. Edge locations are data centers at various geographical locations quicker access to our resources.

Edge locations also run Domain Name Service (DNS) called Amazon Route 53 to help route users to correct web locations with low latency.

Amazon Outposts are data centers run in the customer’s building if requested.

AWS services use Application Programming Interface (APIs) to provide interactions with its services. APIs are predetermined ways of interaction. To provision or launch an instance, create a lambda function etc APIs are used.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2052.png)

AWS Management Console is a browser interface.

AWS CLI can be used to write custom scripts for API calls.

AWS SDKs are used by various programming languages to interact with AWS resources.

AWS Elastic Beanstalk allows us to provision EC2 environments. Application code and configurations can be provided to beanstalk service which builds our environment for us. Environment configurations can be saved for redeployment. Ex. Instance provision, auto scaling, ELB management.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2053.png)

AWS CloudFormation is an ‘infrastructure as code tool’ that allow us to define AWS resources in a declarative way using JSON or yml text documents called CloudFormation templates. This defines the ‘what’ while the ‘how’ is handled by AWS. It will take care of API calls for provisioning and configuration.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2054.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2055.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2056.png)

Continued in [[AWS CP 4-5]]