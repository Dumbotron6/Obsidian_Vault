### Security

AWS offers various security services. One such is below.

![Untitled](Cloud%20Practitioner/Attachments/Untitled.png)

In the Shared Responsibility Model, AWS takes care of some security aspects while the customer is responsible for others.

For example, an EC2 instance runs in a physical data center and it requires networking and a hypervisor. The security aspects for all of these are handled by AWS.

In the EC2 instance, we have an OS of our choice. We have the encryption key to access the OS- to logon or create user accounts etc. AWS has no backdoor access. So we (or our operations team) are responsible for keeping the OS patched. The security for our applications that run on the OS need to be handled by us. Same for the data accessed by the application. For example, pictures that require public access or healthcare information that requires private access.

So AWS is responsible for security of the cloud while the customer is responsible for what’s in the could.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%201.png)

### User Permissions and Access

When an AWS account is created, we are given an AWS root account user. This user is the owner of the AWS account and has full permissions to the account. Any resource can be accessed and controlled in the account.

Upon logging in, a Multi-Factor Authentication (MFA) can be enabled so that in addition to a login id and password, a randomized token is necessary for further logins.

AWS Identity and Access Management (AWS IAM) can be used to manage access for each user subsequently created. A newly created IAM user has no permissions by default. It can’t even login. The root user has to provide specific access to specific users. For example, a user has to be allowed to launching new EC2 instances, create new S3 bucket, access specific databases etc.

A user getting access to only what they need is called ‘Principle of least privilege’.

Granting or denying access to a user is done by associating an IAM policy with the user. An IAM policy is a JSON document that describes what API calls a user can or cannot make. A user can be granted access to only one S3 bucket instead of all buckets in the AWS account.

For example,

![Untitled](Cloud%20Practitioner/Attachments/Untitled%202.png)

Action can be any AWS API call. Resource would be AWS resource that specific API is for. Policies and Users can be organized into IAM groups. A policy can be attached to a group and all users will have all permissions in the policy. There is one other feature called roles.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%203.png)

Roles can be used to grant temporary permissions to users, AWS resources, external identities(?), applications and other AWS services. If a role is granted, the previous roles are abandoned. Instead of creating a user for every employee, all employees can be assigned a IAM role so that they can use their own corporate credentials to login to AWS. This is called Identity federation. IAM roles are ideal for situations in which access to services or resources needs to be granted temporarily, instead of long-term.

IAM is authentication and authorization as a service.

Root user is best used to create and mange IAM users. Every task is possible with root user but not recommended. The work is best delegated to users.

AWS Organizations can be used to centrally manage all AWS accounts in the company. It becomes harder to keep track of all accounts, their billing etc. as the company uses more and more accounts. AWS Organizations helps with this management. Company’s primary account can be used to monitor all member accounts. Accounts can be grouped for billing, compliance etc. into Organizational Units (OUs). For example we can group accounts that can only access AWS services for regulatory requirements or even Developer account(?).

![Untitled](Cloud%20Practitioner/Attachments/Untitled%204.png)

The primary account can use Service Control Policies (SCPs) to specify maximum permissions for member accounts.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%205.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%206.png)

AWS has built data centers, infrastructure and networking following industry best practices for security. AWS customers inherit those practices of policies, architecture and operational processes. AWS complies with a lot of assurance programs. So segments of commonly used compliances like GDPR are already completed. So we can focus on compliance within our architecture on top of AWS. We can request documentation from AWS (since it owns the datacenters) for compliance and security proof. AWS Artifact provides access to these reports. Compliance check is done be third parties.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%207.png)

Suppose that your company needs to sign an agreement with AWS regarding your use of certain types of information throughout AWS services. You can do this through AWS Artifact Agreements.

In AWS Artifact Agreements, you can review, accept, and manage agreements for an individual account and for all your accounts in AWS Organizations. Different types of agreements are offered to address the needs of customers who are subject to specific regulations, such as the Health Insurance Portability and Accountability Act (HIPAA).

Suppose that a member of your company’s development team is building an application and needs more information about their responsibility for complying with certain regulatory standards. You can advise them to access this information in AWS Artifact Reports.

AWS Artifact Reports provide compliance reports from third-party auditors. These auditors have tested and verified that AWS is compliant with a variety of global, regional, and industry-specific security standards and regulations. AWS Artifact Reports remains up to date with the latest reports released. You can provide the AWS audit artifacts to your auditors or regulators as evidence of AWS security controls.

The Customer Compliance Center contains resources to help you learn more about AWS compliance.

The objective of a Distributed denial-of-service (DDoS) attack is to shut down the ability of an application to function by overwhelming the system to the point it can no longer operate. In a normal application, the users send requests and get back responses. In a DDoS attack, the bad actor tries to overwhelm the capacity of the application so that the actual users are denied services. The attacker leverages other machines around the internet to unknowingly attack the infrastructure.

Some types of DDoS attacks are

UPD flood- The attacker calls a public service that returns massive data. Only the attacker fakes the return address to be our address. So our applications is overwhelmed with massive influx of data. This is solved by AWS Security Groups. They send only approved messages in.

HTTP level attacks- Large number of requests are sent to our application. For this AWS offers AWS Shield with AWS Web Application Firewall (WAF). This provides a firewall that prevents requests from suspicious addresses.

Slowroris attack- The attacker pretends to have a slow network so the other users are waiting in line to send requests. This is solved by ELB.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%208.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%209.png)

AWS offers encryption at rest and encryption at transit. DynamoDB offers encryption at rest which also integrates with AWS Key Management Service ( AWS KMS) for managing the encryption key that is used to encrypt the table data. A cryptographic key is a random string of digits used for locking (encrypting) and unlocking (decrypting) data. You can use AWS KMS to create, manage, and use cryptographic keys.

For data in-transit, Secure Socket Layer (SSL) connections to encrypt data and server certificates can be used to validate or authorize a client.

Amazon Inspector helps to improve security and compliance of the application by running automated security assessments against our infrastructure. It checks for deviations from security best practices, exposure of EC2 instances, vulnerabilities etc.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2010.png)

Amazon GuardDuty analyzes continuous streams of metadata generated from our network activity. GuardDuty continuously analyzes data from multiple AWS sources, including VPC Flow Logs and DNS logs.

### Monitoring

AWS CloudWatch allows to monitor the infrastructure and the applications running on AWS in real time. This is done by monitoring metrics (variables tied to resources). For example, the CPU utilization by EC2 instance or number of requests received. Custom metrics can be created. A CloudWatch alarm sets a threshold for a metric and the user is alerted or an action is triggered when the threshold is met. It is integrated with SNS so sms can be sent as an alert. A dashboard can be created to catalogue all metrics. We could create a CloudWatch alarm that automatically stops an Amazon EC2 instance when the CPU utilization percentage has remained below a certain threshold for a specified period.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2011.png)

MTTR- Mean Time to Resolution.

TCO- Total Cost of Ownership.

AWS CloudTrail records API calls for your account. The recorded information includes the identity of the API caller, the time of the API call, the source IP address of the API caller, and more. You can think of CloudTrail as a “trail” of breadcrumbs (or a log of actions) that someone has left behind them. Automatically detecting unusual account activity can be performed by AWS CloudTrail.

You can use API calls to provision, manage, and configure your AWS resources. With CloudTrail, you can view a complete history of user activity and API calls for your applications and resources.

Events are typically updated in CloudTrail within 15 minutes after an API call. You can filter events by specifying the time and date that an API call occurred, the user who requested the action, the type of resource that was involved in the API call, and more.

AWS Trusted Advisor is an automated service.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2012.png)

It checks against AWS best practices for these and recommends the best course of action.

Continued in [[AWS CP 8-10]]