The AWS Free Tier enables you to begin using certain services without having to worry about incurring costs for the specified period.

Three types of offers are available:

- Always Free
- 12 Months Free
- Trials

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2013.png)

The AWS Pricing Calculator lets you explore AWS services and create an estimate for the cost of your use cases on AWS. You can organize your AWS estimates by groups that you define. A group can reflect how your company is organized, such as providing estimates by cost center.

The default maximum number of accounts allowed for an organization is 4, but you can contact AWS Support to increase your quota, if needed. This is for consolidated billing. You can combine usage across accounts to receive volume pricing discounts.

In AWS Budgets, you can create budgets to plan your service usage, service costs, and instance reservations. The information in AWS Budgets updates three times a day.

In AWS Budgets, you can also set custom alerts when your usage exceeds (or is forecasted to exceed) the budgeted amount.

AWS Cost Explorer includes a default report of the costs and usage for your top five cost-accruing AWS services. You can apply custom filters and groups to analyze your data.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2014.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2015.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2016.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2017.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2018.png)

TAM advises on the below.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2019.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2020.png)

AWS Marketplace is a digital catalog that includes thousands of software listings from independent software vendors. You can use AWS Marketplace to find, test, and buy software that runs on AWS. Think of it as Google Playstore.

Migration

Migration to AWS may require input from people holding various roles such as developer, cloud architect, business architect etc. AWS Cloud Adoption Framework (CAF) helps with that.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2021.png)

CAF Action Plan provides guidance for cloud migration. It organizes the guidance into the above six areas of focus. Each perspective is used to identify gaps in our process. These gaps are then recorded as inputs. These inputs are used as the basis for creating an action plan.

Use the Business Perspective to create a strong business case for cloud adoption and prioritize cloud adoption initiatives. Ensure that your business strategies and goals align with your IT strategies and goals.

Use the People Perspective to evaluate organizational structures and roles, new skill and process requirements, and identify gaps. This helps prioritize training, staffing, and organizational changes.

Use the Governance Perspective to understand how to update the staff skills and processes necessary to ensure business governance in the cloud. Manage and measure cloud investments to evaluate business outcomes.

For Platform Perspective, use a variety of architectural models to understand and communicate the structure of IT systems and their relationships. Describe the architecture of the target state environment in detail.

For Security Perspective, use the AWS CAF to structure the selection and implementation of security controls that meet the organization’s needs.

The Operations Perspective helps you to enable, run, use, operate, and recover IT workloads to the level agreed upon with your business stakeholders.

There are various migrations strategies available.

Rehosting- This is also known as lift and shift. The application is moved as is to AWS.

Replatforming- The application is moved to AWS with a few cloud optimizations. The core code is not touched. For example, moving data from MySQL to RDS MySQL.

Retire- When planning for migration, applications that are no longer needed or redundant are shut down and no longer used.

Retain- The application will be deprecation but not immediately. It will run for a certain duration as necessary. These are not migrated to cloud and shut down on premises eventually.

Repurchasing- Legacy software is no longer used and new one is purchased. For example, old CRM software from vendor is shut down and new one is purchased in cloud native offerings. So code in the application might need to be tweaked to support the new software.

Refactoring- Massively restructuring the code to make use of cloud features. 

The AWS Snow Family is a collection of physical devices that help to physically transport up to exabytes of data into and out of AWS.

AWS Snow Family is composed of **AWS Snowcone**, **AWS Snowball**, and **AWS Snowmobile**.

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2022.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2023.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2024.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2025.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2026.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2027.png)

![Untitled](Cloud%20Practitioner/Attachments/Untitled%2028.png)

The AWS Well-Architected Framework helps you understand how to design and operate reliable, secure, efficient, and cost-effective systems in the AWS Cloud. It provides a way for you to consistently measure your architecture against best practices and design principles and identify areas for improvement.

- Operational excellence
- Security
- Reliability
- Performance efficiency
- Cost optimization
- Sustainability

**Operational excellence** is the ability to run and monitor systems to deliver business value and to continually improve supporting processes and procedures.

Design principles for operational excellence in the cloud include performing operations as code, annotating documentation, anticipating failure, and frequently making small, reversible changes.

**Reliability** is the ability of a system to do the following:

- Recover from infrastructure or service disruptions
- Dynamically acquire computing resources to meet demand
- Mitigate disruptions such as misconfigurations or transient network issues

**Performance efficiency** is the ability to use computing resources efficiently to meet system requirements and to maintain that efficiency as demand changes and technologies evolve.

**Sustainability** is the ability to continually improve sustainability impacts by reducing energy consumption and increasing efficiency across all components of a workload by maximizing the benefits from the provisioned resources and minimizing the total resources required.