# Intro

AWS- cloud computing platform by Amazon

Cloud Computing- on-demand delivery of IT resources — like compute power, storage, databases, and more — over the internet, with pay-as-you-go pricing

Availability- minimize downtime and ensure uninterrupted service by eliminating single points of failure and enabling rapid recovery from faults

Fault tolerance- ability of a system to continue operating correctly with no service interruption, even when one or more components fail. 0 downtime

Disaster recovery (DR)- strategy and set of processes used to restore systems, applications, and data after a catastrophic failure that renders the primary environment unavailable. Eg: Data center outage, Cloud region failure, Cyberattack (ransomware), Natural disasters, Large-scale human or operational error.

Elasticity- ability of a system to automatically scale resources up or down in real time based on current workload demand

Scaling

- ability of a system to handle increased load by adding resources
- Vertical scaling- increases or decreases the capacity of a single machine by adding or removing CPU, RAM, or disk
- Horizontal scaling- increases or decreases capacity by adding or removing machines that work together
- Active–Active- Multiple regions or clusters are live at the same time, all serving traffic
- Active–Passive- Only one region serves traffic at any time. The other region(s) are standby.


# AWS Well Architected framework

1\. Operational Excellence

- perform operations as code
- make frequent, small, reversible changes
- refine operations procedures frequently
- anticipate failure
- learn from all operational failures

2\. Security

- Implement a strong identity foundation
- Maintain traceability
- Apply security at all layers
- Automate security best practices
- Protect data in transit and at rest
- Keep people away from data
- Prepare for security events

3\. Reliability

- Automatically recover from failure
- Test recovery procedures
- Scale horizontally to increase aggregate workload availability
- Stop guessing capacity
- Manage change through automation

4\. Performance Efficiency

- Democratize advanced technologies
- Go global in minutes
- Use serverless architectures
- Experiment more often
- Consider mechanical sympathy

5\. Cost Optimization

- ability to run systems to deliver business value at the lowest price point

6\. Sustainability

- Understand your impact
- Establish sustainability goals
- Maximize utilization
- Anticipate and adopt new and efficient hardware and software
- Use managed services
- Reduce downstream impact of your cloud workloads


# AWS Cloud Adoption Framework (CAF)

1\. Business

- Align cloud adoption with business outcomes
- Financial benefits, value realization, growth strategy

2\. People

- Skills, training, roles, and culture
- Change management and enablement

3\. Governance

- Risk management, compliance, cost control
- Policies, controls, and decision-making

4\. Platform

- Infrastructure, networking, architecture
- Landing zones and foundational services

5\. Security

- Identity, access management, data protection
- Threat detection and compliance

6\. Operations

- Monitoring, incident response, automation
- Day-to-day cloud operations

Benefits

- Reduce business risk- Improved reliability, security, performance
- Increase operational efficiency- Lower costs, higher productivity
- Enable business growth- New products, markets, and innovation
- Improve sustainability and transparency- Efficient resource usage and visibility


## AWS Migration Strategies (7 Rs)

1. Retire- Decommission applications no longer needed
2. Retain- Keep applications on-prem (not ready to migrate)
3. Rehost- Lift and shift with no changes. Move applications to AWS without modifying architecture
4. Relocate- Move large numbers of servers together. Move large infrastructure stacks without redesigning apps.
5. Repurchase- Drop and shop. Replace existing application with a SaaS solution.
6. Replatform- Lift, tinker, and shift. Make minor optimizations without changing core architecture.
7. Refactor- Redesign application to use cloud-native architecture.


# Cloud Economics

Total Cost of Ownership (TCO)

1. Capital Expenses (CapEx)
2. Operational Expenses (OpEx)
3. Labor
4. Software Licensing


# AWS Shared Responsibility Model

- AWS uses the Shared Responsibility Model to clearly define who is responsible for what in cloud security.
- AWS is responsible for security of the cloud.
- Customers are responsible for security in the cloud
- The level of customer responsibility changes based on whether a service is managed or unmanaged

AWS manages and secures:

- Physical data centers
- Global infrastructure (Regions, Availability Zones, edge locations)
- Hardware (servers, storage devices, networking equipment)
- Underlying network and physical security
- Infrastructure software (hypervisor, service control plane)
- Managed service infrastructure (compute, storage, database platforms)

Customers are responsible for:

- Operating system configuration and patching (where applicable)
- Network and firewall configuration (security groups, NACLs)
- Application security
- Identity and Access Management (IAM)
- Data security: Encryption (in transit and at rest), Data integrity and authentication, Backup and recovery
- Protecting network traffic (SSL/TLS, certificates)


# AWS access management capabilities

- Every AWS account starts with a root user that has unrestricted access.
- Root user best practices:
  - Enable MFA
  - Secure and rotate credentials
  - Do not use for daily tasks
- Root user is required only for specific actions (e.g., account settings, billing access recovery)


# AWS Security Components


## IAM (Identity and Access Management)

- Securely control access to AWS services and resources
- Create and manage users, roles, and permissions
- Follow the principle of least privilege. Grant only the permissions required to perform a task

Components

- Users- Represents a person or app needing access
- Groups- A collection of users with the same permissions (e.g. AdminGroup, Developers)
- Roles- Temporary credentials assumed by users, services, or external accounts — without a password
- Policies- JSON documents that define what actions are allowed or denied

<!-- -->

- Use IAM roles when:
  - Temporary access is required
  - AWS services need permissions
  - Cross-account access is needed
- Roles are preferred over users for applications and services.

<!-- -->

- Amazon Cognito provides identity management for external users.
- User Pool — Manages user sign-up and sign-in.
- Identity Pool — Provides temporary AWS credentials, including for unauthenticated users.

Network Access Control Lists (NACL)

- Control traffic entering and leaving subnets
- Operate at the subnet level
- Stateless: inbound and outbound rules must both be explicitly defined
- Can explicitly allow and deny traffic
- Only apply when traffic crosses the subnet boundary
- Eg: Compliance requires that only corporate office IPs can access workloads in a subnet

Security Groups (SG)

- acts like a virtual firewall for your EC2 instance, RDS database, or Elastic Load Balancer.
- It controls inbound and outbound traffic at the instance level.
- Inbound rules → What’s allowed to come in
- Outbound rules → What’s allowed to go out
- Operate at the resource level (EC2, RDS via ENI)
- Stateful: return traffic is automatically allowed
- Cannot explicitly deny traffic (implicit deny only)
- Can reference other security groups
- Best for instance-level protection

Security services

- AWS WAF- Filters web traffic based on IPs, headers, URLs. Protects against common attacks (SQL injection, XSS). Used when protecting web-facing applications
- AWS Marketplace- Source for third-party security and compliance tools. Used when native AWS services are insufficient
- AWS Knowledge Center- FAQs and troubleshooting


# Methods of Deploying and Operating in AWS

Access Methods

- AWS Management Console
- AWS CLI
- AWS SDKs
- Infrastructure as Code (IaC)


## Cloud Deployment Models

1\. Public Cloud

- Shared infrastructure, publicly accessible cloud platform
- Example: Amazon Web Services, Microsoft Azure, Google Cloud

2\. Private Cloud

- Dedicated cloud infrastructure for a single organization
- Is not shared with other customers
- Can be on-premises
- Example: AWS Outposts
- Must still meet cloud characteristics (on-demand, scalable, metered)

3\. Hybrid Cloud

- Combination of public cloud + private cloud

4\. Hybrid Environment

- On-premises data center + public cloud (AWS)

5\. Multi-Cloud

- Using multiple public cloud providers (e.g., AWS + Azure)


## Public vs Private Services

AWS Public Services

- Accessible over the public internet
- Reside in AWS public zone
- Example: Amazon S3
- Users access them via the internet unless restricted

AWS Private Services

- Run inside AWS private zone
- No direct internet access by default
- Examples: Amazon VPC, Amazon EC2 (by default)
- Internet access requires explicit configuration (routing, gateways)


## Connectivity Options to AWS

Public Internet

- Default, lowest cost
- Used for public services (e.g., S3, public EC2)
- Less predictable performance

Site-to-Site VPN

- creates a secure connection between your data center or branch offices and your AWS Cloud resources
- Encrypted tunnel over the internet
- Uses: IPsec (Internet Protocol Security)

AWS Direct Connect

- Dedicated private network connection
- Physical fibre optic cables
- High bandwidth, low latency, predictable performance
- Higher cost


# AWS Global Infrastructure


## Resilience Models of AWS Services

Globally Resilient Services

- Operate across multiple Regions
- A Region can fail and the service still works
- Examples: AWS Identity and Access Management, Amazon CloudFront, Amazon Route 53

Regionally Resilient Services

- Operate within one Region
- Data replicated across multiple AZs
- AZ failure tolerated, Region failure is not
- Examples: Amazon EFS, AWS Batch

Availability Zone (Zonal) Services

- Tied to one AZ only
- AZ failure = service failure
- Examples: Amazon EBS, Amazon EC2 (instance-level)


## AWS Region Types

Region

- A geographical area
- Contains 2 or more Availability Zones
- Governed by regional laws and compliance rules
- Data does not leave the Region unless you move it
- Regions are geographically isolated
- Used for: Disaster recovery, Latency optimization, Regulatory compliance

Availability Zones (AZs)

- One or more physically separate data centers
- Independent power, cooling, and networking
- Connected via high-speed, low-latency links
- Primary unit of high availability
- Enables: Fault isolation, Redundancy, Resilient architectures

Edge Locations

- AWS global endpoints
- Used for content delivery and traffic acceleration
- Primary Uses: Caching, Traffic routing, Performance optimization


# Compute Services


## Elastic Compute Cloud (EC2)

- Runs on EC2 hosts (physical servers managed by AWS)
- Availability Zone–resilient
- launch virtual machines (VMs) called instances in the cloud
- AMI (Amazon Machine Image)- OS + base config template. Templates used to launch EC2 instances
- Instance Type- Defines hardware (e.g., t3.micro, m5.large)
- Key Pair- SSH key to connect to your EC2 Linux instance
- Security Group- Acts like a virtual firewall for your instance
- Elastic IP- Static public IP address for your instance
- EBS (Elastic Block store)- Block storage volume (virtual hard disk) attached to the instance. It stores OS files, databases, app data
- Auto Scaling Group (ASG)- adds or removes EC2 instances from a group based on Traffic load, Schedule, Manual changes

### Host Types

Shared Hosts

- Hardware shared with other customers
- Logical isolation enforced by AWS
- Pay per instance

Dedicated Hosts

- Entire physical host reserved for one customer
- Required for licensing or compliance needs
- Pay for the host regardless of usage

### Storage Options

Instance Store

- Physically attached to the host
- Temporary (ephemeral)
- Data lost if instance stops or moves

Elastic Block Store (EBS)

- Persistent block storage

### Networking

- Each instance gets a primary Elastic Network Interface (ENI)
- ENIs are bound to a subnet and an Availability Zone
- Multiple ENIs supported per instance

### Instance Types

- General Purpose- Default workloads, balanced resources
- Compute Optimized- High CPU workloads (ML, gaming, media)
- Memory Optimized- In-memory databases, analytics
- Storage Optimized- High IOPS, data warehousing
- Accelerated Computing- GPU / FPGA workloads

### EC2 Metadata vs User Data

Instance Metadata

- Instance ID
- IP addresses
- IAM role info
- Accessed via 169.254.169.254

Instance User Data

- Startup scripts
- Automation tasks
- Not used for instance information


## Elastic Beanstalk

- PaaS (Platform as a Service) that lets you deploy web applications quickly without manually managing: EC2 instances, Load balancers, Auto-scaling, Monitoring, Application versions
- You upload your code, and Elastic Beanstalk: Automatically provisions EC2, sets up networking, Installs environment runtime (Node.js, Python, Java, etc.), deploys your app and keeps it running


## Lambda

- serverless compute service
- run backend code without managing servers
- based on some trigger (eg. HTTP request, S3 upload, or cron job)
- Event-driven execution
- Max execution time is 15 min


## Batch

- run batch computing workloads on AWS
- It automatically schedules, manages, and scales compute resources for batch jobs, optimizing resource allocation based on job requirements
- Good for: Processing large-scale, parallel workloads in areas like scientific computing, financial risk analysis, media transcoding, big data processing, machine learning training, and genomics research


## Lightsail

- offering virtual private servers (VPSs), storage, databases, and networking at a predictable monthly price.
- It’s ideal for small businesses, basic workloads, and developers seeking a straightforward AWS experience without the complexity of the full AWS Management Console.
- Good for: Basic web applications, low-traffic websites, development and testing environments, small business websites, blogs, and learning cloud services
- No auto scaling
- Similar to Digital Ocean, Linode


## Outposts

- hybrid cloud solution that extends AWS infrastructure and services to on-premises data centers
- It provides a consistent experience between on premises and the AWS Cloud, offering compute, storage, and networking components
- AWS cloud hardware installed in your data center


# Container Services


## Elastic Container Registry (ECR)

- fully managed container image registry
- Store, version, and manage Docker container images
- Push and pull images from EC2, ECS, EKS, and Fargate


## Elastic Container Service (ECS)

- container orchestration service
- Run and manage Docker containers on AWS
- Automatically deploy, scale, and manage containerized applications
- Use EC2 or Fargate to host your containers


## Elastic Kubernetes Service (EKS)

- managed Kubernetes service that allows you to run Kubernetes clusters on AWS without manually installing or operating your own control plane (API server, etcd, scheduler, etc.)


## Fargate

- serverless compute engine for containers
- You can run containers using Amazon ECS or Amazon EKS without provisioning, configuring, or managing EC2 instances


# Database Services


## Aurora

- managed relational database engine, compatible with MySQL and PostgreSQL
- Cluster-based architecture
- One primary, multiple read replicas
- Compute and storage are separated
- Shared distributed storage (not instance-local)
- 5x faster than MySQL on RDS and 3x faster than PostgreSQL on RDS
- Benefits: Faster provisioning, high availability

Aurora Serverless

- No instance provisioning
- Scales automatically using Aurora Capacity Units (ACUs)
- Can scale down to zero
- Pay only for storage when idle

Aurora Global Database

- Cross-Region replication
- Storage-level replication (\< 1 second)
- Improves: Disaster recovery (low RPO/RTO), Global read performance


## RDS (Relational Database Service)

- managed relational database service that lets you run traditional SQL databases in the cloud
- Supports MySQL, PostgreSQL, Oracle, SQL Server, MariaDB, Amazon Aurora
- AWS manages hardware, OS, patching, backups
- Instance-based architecture
- Storage is attached to the DB instance
- AZ-scoped

Multi-AZ (High Availability)

- Synchronous replication
- Primary + standby in different AZs
- Automatic failover
- Improves availability, not read performance

Read Replicas (Scalability)

- Asynchronous replication
- Read-only
- Improves read performance
- Can exist in same or different AZ / Region


## DynamoDB

- serverless NoSQL database that stores data in key-value and wide-column format
- Multi-AZ

DynamoDB Accelerator (DAX)

- In-memory cache for DynamoDB
- Microsecond latency
- Designed for high-read, eventually consistent workloads


## DocumentDB

- fully managed, scalable NoSQL document database service by AWS, designed to be compatible with MongoDB APIs
- JSON-like document database
- Instance based architecture
- Use cases- content management systems, catalog and inventory management, and user profile and personalization systems


## Neptune

- fully managed graph database service designed for workloads where relationships are first-class and traversals across connected data must be fast and deep
- Eg: Social networks, Recommendation engines, Fraud detection, Knowledge graphs


## Elastic Cache

- managed in-memory caching service
- supports Redis and Memcached


## Amazon Managed Blockchain (AMB)

- fully managed service that makes it easy to create, manage, and scale blockchain networks using open-source blockchain frameworks—without having to operate the underlying infrastructure.
- It is designed for multi-party systems where participants need a shared, tamper-evident ledger but do not fully trust a single central authority.


## MemoryDB

- fully managed, Redis-compatible, in-memory database
- Data is durable


## Keyspaces

- serverless, fully managed wide-column NoSQL database compatible with Apache Cassandra


## Timestream

- time-series database optimized for time-stamped data


## Quantum Ledger Database (QLDB)

- fully managed, immutable, cryptographically verifiable ledger database


# Storage Services


## Types

1\. Object Storage

- Data is stored as objects
- Each object = data + metadata + unique ID
- use cases: Images, videos, documents, Backups and archives, Static website assets, Data lakes
- Eg: S3

2\. File Storage

- Data stored as files in directories
- Hierarchical filesystem
- Shared access across machines
- File system protocols: NFS (Network File System), SMB (Server Message Block)
- A file system like EFS is mounted onto your compute instance (EC2, ECS, EKS) so it appears as a local directory. When you mount a filesystem, you attach a remote storage system to a directory in your OS.
- Eg: EFS, FSx

3\. Block Storage

- Data stored as raw blocks
- OS formats blocks with a filesystem
- block-level storage volumes that attach to EC2 instances like physical hard drives
- use cases: Databases, Operating system disks, Transactional workloads
- Eg: EC2 instance store, Elastic Block Store (EBS)


## Simple Storage Service (S3)

- object storage service that allows you to store and retrieve any amount of data, from anywhere, at any time, via the internet
- Objects are stored in buckets
- Global service, data stored in a specific Region
- Public AWS service (AWS public zone)
- Unlimited storage
- Max Object size is 5 TB
- Durability: 99.999999999% (11 nines)
- Scalability: Practically unlimited
- Data replicated across multiple AZs (at least 3)
- Buckets require globally unique names
- S3 Versioning- Prevents accidental overwrite or deletion. Enables object recovery
- S3 Lifecycle- Rules-based mechanism that automatically manages objects over time, transitioning them to cheaper storage classes or deleting them when they are no longer needed
- S3 bucket policy- resource-based JSON policy attached directly to a bucket that controls who can do what on that bucket and its objects.

Storage Classes

- Standard- Frequently accessed data
- Intelligent-Tiering- Automatically moves objects between tiers. Data with unknown or changing access patterns
- Standard Infrequent Access- Infrequently accessed but quick to retrieve (e.g., backups)
- One Zone Infrequent Access- Re-creatable, infrequently accessed data (e.g., processed files, logs). Single AZ
- Glacier Instant Retrieval- Archive data needing millisecond access (e.g., image archives, medical data)
- Glacier Flexible Retrieval- Archival with occasional access (e.g., compliance, financial records)
- Glacier Deep Archive- Long-term archives rarely accessed (e.g., old compliance files, video footage). Lowest cost. Retrieval ≤ 12 hours. Best for long-term archives


## Elastic File System (EFS)

- Linux only
- scalable, serverless, NFS (Network File System) based file storage system for use with AWS compute services like EC2, ECS, and Lambda
- Fully managed and auto-scaling
- Provides a shared file system for multiple instances
- Accessible via NFS protocol
- Ideal for apps needing shared state or persistent storage


## FSx (Managed File Servers)

FSx for Windows

- Amazon FSx
- Windows file system (SMB)
- Active Directory integration
- SSD-based, high IOPS
- Daily backups to S3

FSx for Lustre

- Linux + high-performance computing
- Sub-millisecond latency
- Used for: Machine learning, Video processing, Financial modeling


## EBS (Elastic Block store)

- block storage service that provides persistent, high-performance storage volumes for use with Amazon EC2 instances
- AZ scoped
- Used as: EC2 boot volumes, Database storage
- Data persists even if EC2 stops
- Can be attached to multiple EC2, based on volume type

Types

- General Purpose SSD (gp3/gp2)- Most workloads, boot volumes
- Provisioned IOPS SSD- Critical databases
- Throughput Optimized HDD- Big data, logs (not bootable)
- Cold HDD- Lowest cost, infrequent access (not bootable)


## Storage Gateway

- Connects on-premises storage to AWS
- Communicates over the internet

Types

- File Gateway- Files stored as objects in S3
- Volume Gateway- Block storage over iSCSI
- Tape Gateway- Virtual tape library


## Backup and Archival

S3 for Backups

- Low cost
- Extremely durable
- Lifecycle rules move data to cheaper tiers

AWS Backup

- Centralized backup management
- Works across AWS services and on-prem
- Eliminates per-service backup scripts
- Supports compliance and auditing


# Networking Services


## Virtual Private Cloud (VPC)

- provision a logically isolated, private network within the AWS cloud
- private by default
- We can create separate VPC for each environment (dev, staging, prod) and avoid using AWS default VPC in general

### Types

Default VPC

- One per Region
- Preconfigured by AWS
- Includes public subnets (one per AZ), IGW (Internet Gateway) attached, default routing

Custom VPC

- Fully user-defined
- Private by default
- Recommended for production workloads

### Components

- CIDR (Classless Inter-Domain Routing) Block- Defines IP address range (e.g., 10.0.0.0/16)
- Subnet- Smaller IP range inside a VPC (e.g., 10.0.1.0/24)
- Route Table- Rules for routing traffic between subnets or out to internet
- Internet Gateway (IGW)- Enables public internet access. 2 way communication between the internet and VPC. Enables communication between public subnets and the internet
- NAT Gateway- Allows private subnets to access the internet securely. Does not allow inbound traffic
- Network Access Control List (NACL)- Firewall at subnet level

### Subnets

- logical subdivision of a VPC CIDR block and it exists in exactly one Availability Zone (AZ-scoped)
- Public subnets contain resources that need to be accessible by the public, such as an online store’s website.
- Private subnets contain resources that should be accessible only through your private network, such as a database
- In a VPC, you can define rules to allow resources in different subnets to communicate with each other. For example, you might have an application that uses Amazon EC2 instances in a public subnet communicating with databases that are located in a private subnet.
- Each subnet is associated with one route table at a time.
- 6 subnets per VPC is recommended. 1 app public subnet, 1 app private and 1 db private in 2 different AZs each.

### Internet Connectivity

Internet Gateway (IGW)

- One IGW per VPC
- Required for public subnets
- Adding a route to IGW (0.0.0.0/0) makes a subnet public
- IGW performs static NAT

NAT Gateway

- Enables outbound-only internet access for private resources
- Prevents inbound internet-initiated traffic
- Common use case: OS and package updates

### Network Security Controls

Network ACLs (NACLs)

- Operate at subnet level
- Stateless
- Explicit inbound and outbound rules required
- Control traffic crossing subnet boundaries

Security Groups

- Operate at resource / ENI level
- Stateful
- Return traffic is automatically allowed
- Act as instance-level firewalls

### VPC Communication Models

VPC Peering

- Private IP communication between VPCs
- Works across accounts and Regions
- No transitive routing

VPC Endpoints

- Access AWS services without internet or NAT
- Gateway Endpoints: S3, DynamoDB
- Interface Endpoints: All other services (DNS-based)

AWS PrivateLink

- Securely expose services to other VPCs/accounts
- No VPC peering or IGW required
- Used for service-to-service private access

### Hybrid Connectivity

AWS VPN

- Encrypted virtual connection over the public internet
- Used for hybrid cloud setups
- Faster to set up, lower cost

AWS Direct Connect

- Dedicated physical connection
- Higher bandwidth, lower latency


## Virtual Private Gateway (VPG)

- It is the gateway through which on-premises networks or other private networks connect to your VPC using encrypted tunnels


## AWS Client VPN

- managed VPN service
- allows individual users (laptops, desktops, mobile devices) to securely connect to AWS resources and on-premises networks over the internet
- used to access private resources on AWS


## Elastic Load Balancer (ELB)

- service that automatically distributes incoming traffic across multiple targets like: EC2 instances, Containers (ECS), Lambda functions, IP addresses
- Load balancer nodes run per AZ for high availability
- Types
  - Application Load Balancer (ALB)- HTTP/HTTPS traffic (layer 7)
  - Network Load Balancer (NLB)- TCP/UDP traffic (layer 4)
  - Gateway Load Balancer (GWLB)- Third-party applications (layer 3 and 4)
- Used for: Microservices, Kubernetes ingress, High-performance backend services


## API Gateway

- Proxy requests to AWS Lambda, EC2, ECS, or any HTTP backend
- Apply request validation, request/ response transformation, authentication, monitoring, rate limiting, and caching
- Used for: Serverless apps, Public SaaS APIs, Monetized APIs, B2B integrations


## Route53

- cloud-based DNS service
- Roles
  - DNS resolution- Translates domain names (like example.com) into IP addresses
  - Domain registration- buy and manage domain names
  - Health checks & routing- Routes users based on location, health, latency, etc.


## CloudFront

- CDN (Content Delivery Network)
- caches and serves static or dynamic content from AWS edge locations worldwide, reducing latency and improving performance
- Serves content from nearest edge location


## Global Accelerator

- Improves TCP/UDP traffic performance
- No caching
- Uses static IPs
- Enables fast regional failover


# Application Integration Services


## Simple Queue Service (SQS)

- message queue service
- Send, store, and receive messages between components of a system
- Polling types- Short and Long

Types

- Standard- At-least-once delivery, best-effort ordering, duplicates possible
- FIFO- Exactly-once delivery, strict ordering, no duplicates


## SNS (Simple Notification Service)

- Publish/Subscribe (pub sub) Messaging
- allows you to send notifications (messages) to multiple subscribers (consumers) via a topic
- create SNS topic and add subscribers


# Business applications Services


## SES (Simple Email Service)

- email-sending service


## Connect

- Cloud contact center
- Voice and chat support


# Security, identity, and compliance


## Macie

- AI / ML–powered security service
- Uses machine learning to discover: PII (Personally Identifiable Information), Sensitive data in Amazon S3
- Helps with compliance and data protection


## Secrets Manager

- secure way to manage (store, retrieve, rotate) sensitive information like passwords, API keys, and database credentials


## Key Management Service (KMS)

- managed cryptographic service that enables you to create, control, and use encryption keys to protect data across AWS services and applications
- secure: Data at rest (databases, disks, object storage), Application secrets and configuration values, Sensitive payloads (PII, financial data, credentials)


## Certificate Manager (ACM)

- provision, manage, and deploy SSL/TLS certificates
- Encrypt data in transit


## Inspector

- automated security vulnerability management service
- It continuously scans your AWS workloads to identify software vulnerabilities, missing patches, and unintended network exposure, and then produces prioritized security findings
- Scans: EC2 instances, Container images in Amazon ECR, AWS Lambda functions


## GuardDuty

- threat detection service
- It continuously monitors your AWS environment to detect malicious activity and unauthorized behavior using machine learning, anomaly detection, and threat intelligence
- Scans: VPC Flow Logs – suspicious network traffic, AWS CloudTrail – unusual API calls, DNS logs – malware command-and-control activity, EKS audit logs – Kubernetes threats


## Detective

- security investigation and forensics service
- analyze and understand security incidents by automatically correlating data from multiple AWS sources


## Security Hub

- security posture management service
- It aggregates, normalizes, and prioritizes security findings from across AWS and third-party tools
- Aggregates Amazon GuardDuty, Amazon Inspector, Amazon Macie, AWS Config, Third-party security tools


## Artifact

- provides AWS compliance documentation and legal agreements
- Compliance Reports- SOC 1, SOC 2, SOC 3. ISO 27001, 27017, 27018. PCI DSS, CSA STAR, Regional certifications (e.g., IRAP, MTCS)
- Agreements- Business Associate Addendum (BAA) for HIPAA. GDPR Data Processing Addendum (DPA). Financial services and regulatory addenda


## AWS Compliance

- set of programs, controls, certifications, and assurances that demonstrate how AWS meets global regulatory, security, and privacy requirements


## AWS Shield

- Distributed Denial of Service (DDoS) protection service that safeguards applications running on AWS


## AWS Config

- configuration monitoring and compliance service that continuously tracks AWS resource configurations and their changes over time


## AWS Audit Manager

- compliance automation service that helps you continuously collect evidence for audits


## Trusted Advisor

- account-level best-practice advisory service that continuously evaluates your AWS environment and provides recommendations across multiple domains
- Pillars- Cost Optimization, Security, Fault Tolerance, Performance, Service Limits


## Access Analyzer

- security analysis service that identifies unintended access to AWS resources, especially external access


# Analytics


## Redshift

- Column-based, petabyte-scale data warehouse
- Designed for analytics, not transactions
- OLAP (Online Analytical Processing)
- Column storage
- PostgreSQL-based (but not OLTP)
- Massively Parallel Processing (MPP)
- Redshift Spectrum- Query data directly in S3 without loading it


## Athena

- Queries data directly in Amazon S3
- Uses SQL
- No infrastructure to manage


## Kinesis

- Real-Time Data Streaming
- enables real-time ingestion, processing, and analysis of streaming data such as logs, clickstreams, IoT telemetry, and events

Components

- Kinesis Data Streams – custom real-time consumers
- Kinesis Data Firehose – load streams into S3, Redshift
- Kinesis Data Analytics – SQL on streams


## Glue

- fully managed ETL service used to discover, prepare, and transform data for analytics and ML
- Serverless ETL jobs
- Crawlers to auto-discover schemas
- Central Data Catalog
- Spark-based processing
- Use Cases: Preparing data for Athena, Redshift, S3 data lakes. Schema discovery. Data transformation pipelines


## QuickSight

- serverless BI and visualization tool used to create dashboards and reports
- ML-powered insights


## OpenSearch Service

- search, log analytics, and real-time operational monitoring
- used for full-text search, application logs, metrics, and observability dashboards


## EMR

- Elastic MapReduce
- Large scale and complex data processing
- Managed Hadoop ecosystem
- Supports: Apache Spark, Hadoop, Hive
- Used for: ETL, Analytics, ML workloads


# Migration & Transfer


## Snow Family

- set of physical devices provided by AWS to move, store, and process data at the edge

Types

- Snowcone- Small data (≈8 TB)
- Snowball Edge- TBs to PBs + compute
- Snowmobile- PBs to exabytes


## Database Migration Service (DMS)

- Low-downtime database migration
- Continuous replication
- Source → Target databases
- Supports: Homogeneous migrations and Heterogeneous migrations (with SCT)

Schema Conversion Tool (SCT)

- Converts schema between engines
- Used alongside DMS


## DataSync

- Online data transfer for files and objects
- On-prem ↔ AWS storage
- Uses internet or Direct Connect


## Transfer Family

- file transfer service that lets you run SFTP, FTPS, and FTP endpoints directly backed by Amazon S3 or Amazon EFS, without managing servers
- used for bank integrations, B2B file exchange, batch jobs, and legacy system interoperability


## Migration Evaluator

- Build a business case for moving to AWS.
- Analyzes your on-premises environment (servers, storage, databases).
- Estimates current TCO (Total Cost of Ownership) vs AWS costs.
- Produces ROI, savings, and modernization impact reports.


## Application Discovery Service

- Discover what you actually have and how it’s connected.
- Discovers: Servers (physical & virtual), Installed software, Network dependencies between systems
- Collects real usage data, not assumptions.


## Migration Hub

- Central command center for migration tracking
- Aggregates data from: Application Discovery Service, Application Migration Service, Database Migration Service


## Application Migration Service (MGN)

- Lift-and-shift (rehosting) servers to AWS with minimal downtime.
- Replicates on-prem or cloud servers continuously into AWS.
- Creates EC2 instances that are bootable and production-ready.
- Supports Windows and Linux.


# Machine Learning (ML) and Artificial Intelligence (AI) Services


## AI Services

- fully managed, pre-trained APIs that provide intelligence without requiring ML knowledge. You do not train models—you simply call the service
- Use when you do not want to build or train models

Services

- Rekognition- computer vision service, image & video analysis (faces, text, objects)
- Lex- Chatbots & conversational interfaces
- Polly- Text-to-speech
- Translate- Language translation
- Kendra- AI-powered enterprise search service that allows users to search across structured and unstructured data using natural language
- Bedrock- build, customize, and deploy generative AI applications using leading foundation models (FMs) through a single API


## ML Services

- ML services help you build, train, tune, and deploy machine learning models using your own data, without managing infrastructure
- Custom model

### Services

SageMaker

- Build, train, and deploy ML models at scale
- Manages infrastructure, training jobs, and deployment
- Uses data from: Amazon S3, Amazon RDS, Amazon Redshift

CodeWhisperer

- ML-powered code generation and security scanning


## ML Frameworks & Infrastructure

- This layer provides raw compute, storage, and ML frameworks to build ML systems from scratch, offering maximum flexibility and responsibility.
- Use when you need full control.
- Open-source frameworks: TensorFlow, PyTorch, Apache MXNet


# Management and governance


## Cloudformation

- Infrastructure as Code (IaC) service
- define and provision AWS resources using code

Components

- Stack- A collection of AWS resources managed as a single unit
- Template- A YAML or JSON file defining the resources
- Resources- AWS services (e.g., EC2, S3, RDS, VPC) declared in the template
- Parameters- Inputs to customize the stack
- Outputs- Values returned after stack creation (e.g., VPC ID, DNS)
- Mappings- Static values depending on region, env, etc.
- Conditions- Control resource creation with logic
- StackSet- Deploy stacks across multiple AWS accounts or regions


## CloudWatch

- monitoring and observability service
- It collects and visualizes metrics, logs, and events from different AWS services
- Logs- Logs are discrete, timestamped records of events emitted by applications, services, or infrastructure. Each log entry represents something that happened
- Metrics- numeric measurements aggregated over time. Overall health & trends (CPU, latency, Memory usage, request rate, error rate)
- Traces- entire lifecycle of a single request as it flows through systems

Components

- Metrics- Numerical data like CPU%, memory, disk usage, etc.
- Logs- Log files from Lambda, ECS, EC2, etc.
- Alarms- Monitor a metric and trigger an action when a threshold is crossed
- Events- Rule-based triggers (e.g., cron jobs, EC2 state change)


## CloudTrail

- auditing service that records every API call and activity made in your AWS account


## X-Ray

- Application tracing
- End-to-end request analysis


## EventBridge

- serverless event bus service that enables event-driven architectures on AWS.
- It ingests events from AWS services, custom applications, and SaaS providers, evaluates them against rules, and routes matching events to downstream targets for processing.
- Near real-time reaction to system changes


## AWS Organizations

- centralized account management and governance service that lets you manage multiple AWS accounts as a single organization, apply guardrails, and control billing and security at scale


## Control Tower

- managed landing zone service that sets up and governs a multi-account AWS environment


## Service Catalog

- allows organizations to define and control which AWS resources users are allowed to provision, and how they are provisioned


## AWS License Manager

- track, manage, and enforce software license usage across AWS and on-prem environments


## AWS Health Dashboard

- service health and event notification system that provides real-time, personalized visibility into AWS service events that may impact your specific resources


# Customer Engagement

- AWS Activate- Startup credits, tools, training
- AWS IQ- Access to AWS-certified experts
- AWS Managed Services- Enterprise-grade infrastructure operations
- AWS Support- Multiple plans. 24/7 access. Documentation, tools, and technical guidance


# Developer tools

- AWS AppConfig- feature flag and configuration management service
- AWS Cloud9- A browser-based IDE hosted inside AWS
- AWS CloudShell- A browser-based shell with AWS CLI preinstalled
- AWS CodeArtifact- Private package repository, store internal packages
- AWS CodeBuild- Serverless build service. Run tests, build docker images, compiles code
- AWS CodeCommit- Git repository hosted on AWS
- AWS CodeDeploy- Automates deployment to EC2, On-prem, Lambda
- AWS CodePipeline- CI/CD orchestration tool
- AWS CodeStar- A UI wrapper to set up Repo, Pipeline, IAM, Project templates


# End user computing

- Amazon AppStream- Streams applications to users
- Amazon WorkSpaces- Fully managed virtual desktops. Windows, Amazon Linux, Ubuntu
- Amazon WorkSpaces Web- Secure browser access to internal and SaaS apps


# Frontend web and mobile services

- AWS Amplify- Full-stack frontend development and hosting. Web and mobile apps
- AWS AppSync- Managed GraphQL APIs. Integrates with DynamoDB, Lambda, HTTP APIs


# Internet of Things (IoT)

- IoT Core- Secure device connectivity. Data ingestion and processing
- IoT Greengrass- Extends cloud capabilities to edge devices. Local processing. Offline operation. Secure communication with AWS IoT Core


# Cloud Financial Management


## Cost Optimization Design Principles

1\. Right-Sizing

- Choose the correct instance size and type based on actual workload needs.
- Avoid over-provisioning (e.g., using large EC2 instances when smaller ones suffice).
- Select the cheapest instance type that meets performance requirements.
- Continuously reassess as workloads evolve.

2\. Increase Elasticity (Pay for What You Use)

- Use resources only when needed.
- Scale up during demand spikes and down during low usage.
- Prefer many smaller instances over fewer large ones where appropriate.
- Use: Amazon EC2 Auto Scaling, AWS Auto Scaling, AWS Lambda


## Pricing Models

1\. On-Demand Instances

- No long-term commitment.
- Pay per second/hour.
- Best for: Short-term workloads, Unpredictable usage, Flexible start and end times, Applications that cannot tolerate disruption

2\. Reserved Instances (RI)

- 1-year or 3-year commitment.
- Save up to 72% vs On-Demand.
- Payment options: All upfront, Partial upfront, No upfront
- You pay whether instances are running or not.
- Best for: Steady-state workloads, Long-term applications
- Types
  - Standard RI- highest discount
  - Convertible RI- flexibility to change: Instance families, Operating systems, Tenancy
  - Scheduled Reserved Instances- Discounted rates for predictable recurring schedules
  - Regional RI- Discount applies across any Availability Zone in the region
  - Zonal RI with Capacity Reservations- Guarantees capacity in a specific AZ

3\. Spot Instances

- Use unused EC2 capacity.
- Save up to 90% vs On-Demand.
- Price fluctuates based on spare capacity.
- Instances can be interrupted at any time.
- Best for: Fault-tolerant workloads, Flexible applications, Batch jobs, Extremely cost-sensitive workloads

4\. Savings Plans

- Commit to consistent compute usage (not instance-specific).
- Applies to: EC2, AWS Lambda, ECS
- Flexible and simpler than RIs.

5\. Dedicated Hosts

- Physical server dedicated to one customer.
- Pay for the host, not individual instances.
- Used for: Compliance, Licensing requirements

6\. Dedicated Instances

- Instances run on single-tenant hardware.
- Billed per instance-hour.

7\. Capacity Reservations

- Guarantees EC2 capacity in a specific AZ.
- Can be combined with Reserved Instances.


## Cost Optimization Best Practices

- Define and enforce cost allocation tagging- Identify which teams/resources generate costs.
- Use an effective account structure- Design accounts based on business goals.
- Define metrics and target goals- Measure progress continuously.
- Enable cost visibility for teams- Give teams access to cost data. Ownership drives cost-aware decisions.
- Create a Cloud Center of Excellence (CCOE)- Dedicated team for AWS best practices, New service adoption, Continuous cost optimization


## Services

AWS Cost Explorer

- High-level, interactive cost visualization
- Supports: Trend analysis. Filtering by service, account, tags. Drill-down analysis
- Best for: Executive summaries, Interactive financial views

AWS Cost and Usage Reports (CUR)

- Most granular billing data
- Breakdowns by: Hour, day, month. Service. Resource. Tags
- Can be exported to: Amazon Athena, Amazon Redshift, Amazon QuickSight
- Best for: Detailed financial analysis, Custom reporting, Chargeback/showback models

AWS Budgets

- Create: Cost budgets, Usage budgets, Reserved Instance budgets
- Set threshold-based alerts
- Can trigger: Notifications, Automated actions
- Includes: Billing alarms, Free Tier alarms

AWS Trusted Advisor

- Identifies: Underutilized EC2, Idle EBS volumes
- Provides cost optimization recommendations
- Requires monitoring baseline usage first

AWS Pricing Calculator

- Estimates: Migration costs, Monthly run costs
- Based on: Expected usage, Selected services

AWS Billing Conductor

- Custom billing service
- Supports: Chargeback, Showback
- Designed for: AWS Solution Providers, Large enterprises


## Cost Optimization Techniques

Cost Allocation Tags

- Tags help attribute costs to: Teams, Projects, Environments
- Used to: Filter views in Cost Explorer, Segment data in CUR, Mandatory best practice for cost visibility

EBS Cost Optimization

- Monitor: Provisioned IOPS vs used IOPS
- If paying for high-performance volumes without using them: Switch to lower-cost volume types
- Can reduce cost without performance impact

Storage Lifecycle & Cost Reduction

- Lifecycle Automation- Automatically delete old data to reduce storage costs
- Tools: Amazon Data Lifecycle Manager, AWS Backup

Amazon S3 Cost Optimization

- Multiple tiers with trade-offs between: Storage cost, Retrieval time, Retrieval cost
- S3 Lifecycle Configuration- Automatically transitions objects after a defined time
- S3 Intelligent-Tiering- Automatically moves each object to the most cost-effective tier based on access patterns


# Support Options


## AWS Basic Support

- Automatically included for all AWS customers
- Provides: Customer service, 1-on-1 responses for account and billing questions, AWS support forums, Service Health Dashboard, Documentation, whitepapers, and best practice guides
- No technical support cases
- 24/7 access to the above non-technical resources


## AWS Developer Support

- Most cost-effective plan that offers: Technical support
- Unlimited number of technical support cases
- Includes: Access to the seven core AWS Trusted Advisor checks, Access to the Personal Health Dashboard, Personalized alerts when AWS services or customer resources are impacted
- Intended for: Individual developers, Early-stage workloads


## AWS Business Support

- Designed for production workloads
- 24/7 technical support from AWS engineers
- Use case guidance (which AWS services and features best fit your needs)
- Full AWS Trusted Advisor (Cost optimization, Security improvements, Reliability and performance checks)
- AWS Support API (Programmatic case management, Programmatic Trusted Advisor access)
- Third-party software support (OS support on EC2, Performance support for popular third-party software)
- Optional: Infrastructure Event Management (IEM) for an additional fee


## AWS Enterprise On-Ramp

- A newer tier positioned between Business and Enterprise
- Offers some enterprise-style benefits at lower cost
- Still not as comprehensive as full Enterprise Support


## AWS Enterprise Support

- Designed for large, mission-critical environments
- Includes everything in Business Support
- 24/7 concierge-style support
- A designated Technical Account Manager (TAM). Coordinates proactive and preventative guidance. Acts as a single point of contact
- Proactive programs such as: Well-Architected Reviews, Operational Reviews
- These proactive programs are exclusive to Enterprise Support
- Primary goal: Long-term customer success on AWS


## Additional AWS Technical Resources

Documentation & Knowledge Sources

- Primary source for deployment instructions: AWS documentation
- Primary source for best practices: AWS Well-Architected Framework
- Community support for specific questions: AWS re:Post

Training & Skill Development

- AWS Training and Certification
- AWS Skill Builder
- Digital courses
- Hands-on labs

Operational & Escalation Support Paths

- AWS Abuse team
- Billing and account support
- Premium support channels
- Technical Account Managers (TAMs)
- AWS Partner Network- Consulting partners, Technology partners
