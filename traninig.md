# General Information about the course

## Objectives
* Good practices on architecturing
* High availability systems
* Serverless technologies
* Scaling
* Automating
* Well-architected framework

## Core AWS knowledge
* Cloud computing: on-demand delivery of IT resources and application via the internet; pay-as-you-go
* Stop thinking about infrastructure, instead use it as software.

# About the cloud

## Models of cloud computing
* IaaS: infra as a service
* PaaS: platform as a service
* SaaS: software as a service

## Cloud deployments models
* All-in: everything in the cloud
* Hybrid: some and some

## What is AWS?
* Cloud computing
* Core services: compute, storage, delivery, database, networking
* Many more services.

# AWS Infra

## Availability zones
* Set of data centers. 
* Interconnected trough other AZ through *private links*
* It's recommended to replicate for reliability. 
* **BEST PRACTICE**: Two AZ

## Regions
* Made up or two or more AZ 
* 16 regions worlwide (and growing)
* You can control replication across regions too
* Communications between regions is through *public internet*

## Edge Locations
* Mostly for Content Delivery Network and DNS
* 60+ edge locations

## Unmanaged vs Managed services
* Unmanaged: you have to deal with stuff. For example, a DB in an EC2
* Managed: AWS takes care of stuff. For example, RDS

# Security

## Shared Responsibility Model
* AWS takes some responsibility on security, the other part is on the client.
* Client needs to take care of: application, platform, identity and access, OS, network, firtewall, etc
* AWS takes care of storage, db, networking, compute and infrastructure security. **PHYSICAL SECURITY**
* AWS: intrusion detection, delete information from failed hard drives, personel security checks, etc

# Cloud Adoption Framework 
* Guidelines for running AWS enviorenments
* Available somewhere on Amazon

# Best practices
* Ensure that your arch can handle changes in demand.
* Remove manual processes. + stability, + consistency, + efficiency
* Think of servers as temporary resources
* Reduce interdependencies (decouple)
* Services, not servers
* Match the techonology to the workload
* Implement redundancy to avoid having a single failure point
* Appropiate size, good scaling and take advantage of different princing options
* Use caching
* Secure your infra everywheree

# VPCs

* Virtual private network
* VPCs are logically isolated from other virtual networks
* Many AWS resources (ex EC2) are launched into VPCs
* VPCs are configurable (ip ranges, routing, gateways, security settings)
* VPCs can include resources in more than one AZ

# Main services

## EC2
* General Computing
* Broad set of compute instance types

## S3
* Cloud storage: scale seamlessly and provide 99.99999999999% durability.
* Object-level storage: as many objects as you want (objects=files)
* Data is stored redundantly
* Object uploads/deletes can trigger notifications, workflows or scripts
* Automatic encryption available
* **Amazon Glacier:** very long term storage

## Elastic Block Store (EBS)
* Create individual storage volumes and attach them to EC2 instances.
* Can be backed up automagically to S3 with snapshots 
* Volumes are replicated withen its AZ
* What for? Boot volumes, data storage, database hosts, enterprise apps
* Think of them as volumnes for your EC2 instances, nothing more, nothing less
### Block Storage vs Object Storage
* object storage saves the WHOLE file, block storage saves a file by blocks
* Think about what happens if you want to change 1KB of a 1GB file. With block storage, change ONLY that block.


## Amazon DBs: RDS and  DynamoDB
### Relational Database System (RDS)
* Managed services
* Scalable
* Automatic and redundancy
* Supported engines: Amazon Aurora, Postgre, MySQL, MariaDB, ORACLE, SQL Server

#### When to use RDS or a relational DB?
* Complex transactions or complex queries
* Medium-to-high query/write rate
* No more than a single worker node
* High durability

#### When no to use RDS or a relational DB?
* Massive read/writes
* Sharding due to high data size or throughput demands
* Simple GET/PUT requests and queries that a NOSQL DB can handle
* RDBMS customization

### DynamoDB
* Consisten, single-digit milisecond latency at any scale
* No table size or throughput limits
* Only runs on SSD
* Document and key-value store models supported
* IDeal for mobile, web, gaming, ad tech and IoT apps

## AWS Identity and Access Managment (IAM)
* Manage access and authentication
* Completely free as in beer
* Users, groups and roles. Apply policies to them.
* There's always a root account which is unrestricted

### IAM Permissions
* All permissions are disabled by default
* Denied explicitly overrieds any allow on other policies
* Always try to follow the **least privilege** principle

### IAM Policies
* Are applied to users, groups or roles
* A policy may be applied to multiple entities
* A single entity may have several policies (up to 10)
* **BEST PRACTICE:** Instead of attaching a policy to several IAM users, put the users in a group and attach the policy there instead

### IAM Users
* Users are entities
* Provides a way to interact with AWS
* No default security credentials
* Users are not neccesarily people

### IAM Groups
* Collection of IAM users
* Groups cannot be nested
* A user can belong to several groups
* There are no default groups

### IAM Roles
* Temporary access, used to *delegate* access to AWS resources
* Avoids creating users all the time
* Also good to provide aws resources access to aws services

# First thing to do with a new AWS account
1. Stop using the root account
2. Require MFA for your root account and possibly all users
3. Enable CloudTrails (logs all API requests)
4. Enable Config
5. Enable Billing Report

--------------------------------------------

### Lunch & Lab 1

--------------------------------------------

# Designing your architecture

## How to choose a region?
1. Sovereignty and compliance requirements.
2. Proximity of the region from your users.
3. Costs: not every service costs the same in every region
4. Services: the region must have all the services you need

## How many AZ should you use?
* Most probably **2** per region.
* Why? Most cost-effective. Also, for Spot instances you get more price options, DBs can replicate

## VPCs
* Only one VPC is appropiate for limited cases: eg, high-performance computing
* Two solutions for multiple VPCs: multi-VPC pattern and multi-account pattern
* which pattern is best? Single IT team: multi-VPC pattern. Large org: multi-account
* For high isolation, a multi-account approach is recommended, as most services aren't in a VPC

## How to use subnets
* Recommendation: use subnets to define internet accesibility
* You should create at least two subnets in every AZ
* One should be private and the other one should be public
* Public subnets: include a routing table entry to an Internet Gateway (IGW) to support I/O from the Internet
* Private subnets: do not have a routing table entry to an internet gateway

## Route tables: directing traffic between VPCs
* Route tables: determine where network traffic is routed
* Security groups: associated with each instance. They are virtual firewalls. They're stateful.
* Can define source/target as CIDR block or as another security group

## Network ACLs
* Virtual firewall, but live in the subnet. Optional.
* They are stateless.

## Internet gateways
* Allow communication between instances in your VPCs and the internet

## Network Address Translation (NAT)
* Enable instances in the private network to initiate outbound traffic to the internet
* Prevents private instances from receiving inboud traffic from the internet
* Amazon provides a service for this: VPC NAT Gateway
* One can also use a simple EC2 instance as a NAT

# High Availability
* Recovery Time Objective (RTO): how quickly must system recover?
* Recovery Point Objective (RPO): how much data can i afford to lose?# Scalability 
* Ensure that architecture can handle changes in demand
* Cloud-based infrastructure enables you to quickly respond the changes in resource needs

## Vertical vs horizontal scaling
* Vertical scalling: change in the specifications of instances (eg, more CPU or RAM)
* Horizontal scalling: change in the _number_ of instances

## Cloudwatch
* Answers: How much of your infrastructure is actually being used? 
* Cloudwatch monitors your instances and provides readable, near real-time metrics
* Can send notifications and trigger auto scaling based on these metrics
* It has the ability to create and use custom metrics
* Provides logs with CloudWatch logs, they can be store in S3

## Autoscaling
* Launches or terminates instances based on specified conditions
* Can launch across AZ
* Provides an API

### What does it need?
* It needs a launch configuration (Which instance should it create?)
* An auto scaling group (AZ, subnet, min/max instances, etc)
* An auto scaling policy or scheduled actions (when should it autoscale?)

### Autoscaling group
* Defineds a desired, minimum and maximum capacity
* What is a good minimum/maximum capacity size?
* As we need at least two AZs with one instance each, we should at least have 2 instances as min size
* Much more difficult to calculate maximum size

### Considerations
* Avoid auto scaling trashing (avoid aggresive instance termination)
* Set the min/max capacity paramter values carefully
* Use lifecycle hooks (custom actions when launching or terminating)
* Statateful applications need additional configuration
* Keep in mind instances can take several minutes before being usable

## EC2 Autorecovery
* Replace impaired EC2 nstances automatically with EC2's auto recovery feature
* Supported by C3, C4, M3, M4, R3, R4, T2, X1

### Conditions 
* Loss of network connectiviy
* Loss of system power
* Software/hardware issues on host

## Scaling Data Stores
* Scale vertically
* Use read replica
* Cache if front of RDS
* Sharding (possible mostly with NoSQL databases)

## AWS Lambda and Event-Driven scaling
* Fully managed compute service that runs stateless code
* Mostly based on events, or also on a time-based interval
* The buzzword: **Serverless**

# Automating your infrastructure
* Where possible, **automate**
* Always think of servers as **temporary resources**

## Infrastructure as code
* Bring concepts from software into infrastructure
| Software          | Infrastructure      |
|-------------------|---------------------|
| Source Code       | JSON                |
| Interpreter       | AWS Service API     |
| Desired app state | Desired infra state |

### Advantages
* Repeatability
* Consistency
* Reusability
* Parallelization

### AWS Cloud formation
* Allows you to launch, configure and connect AWS resources with JSON or YAML

#### Template
* JSON/YAML file describing the resources
* Treat it as source, put it on a repo

#### AWS CloudFomration
* Interprets the template

#### Stack
* A collection of resources created by AWS Cloud Formation

## Other tools of automation
### Beanstalk
* Automated deployment and scaling service

### AWS OpsWorks
* Configuration managment service, similar to Puppet or Chef

### EC2 Run Command
* Simple way to automate admin tasks

# Decoupling your infrastructure

## Best practices

### Server interdependency
* DO NOT have fixed dependencies between servers
* DO have a load balancer and everyone pointers to it

### Design services, not servers
* DO NOT have apps running on persistent servers, or have apps communicating directly to each other.
* DO have message queues and apps independent of the servers

## Service-oriented architectures (Microservices)
* Microservices are small, independent processes.
* Each microservices handles ONE small task
* Each one of them use language-agnostic API
* Threat microservices as objects or interfaces: share the least possible amount of endpoints

## SQS: Communication between components
* Queue - push service

# Web apps and AWS
## How to get the most of S3?
* Choose region looking at proximity from users
* Pay attention to naming scheme: s3 automatically partitions your buckets according to prefixes of your files
* If always same prefix, you'll be accessing always the same partition. Use a hash as prefix to avoid this.
* Use caching

Well Architected Framework
==========================
* File paper available, look for "The Well Architected Framework"
* A set of questions to check if your arch conforms to best practices

## Pilars of the well-architected framework
1. Security
2. Reliability
3. Performance
4. Costs

## Security Pilar
* Build security in every layer of your infrastructure
* **Isolate** parts of your infrastructure
* **Encrypt** data in transit and at rest
* Enforce **access control** granularly using the principle of least privilege
* Use **MFA**
* Use **managed services**
* **Log** access of resources 
* **Automate** deployment to keep security consistent

### Principles of security pilar
* Protect information, systems and assets 
* Apply security at all layers
* Enable traceability
* Implement a principle of least privilege
* Focus on securing your system
* Automate security best practices

### DDoS
* Attack which attempts to make your app unavailable to end users
* AWS protection consists on detection and mitigation
* But you are responsible for your application (front your app with AWS services, safeguard exposed resources, minimize attack surfaces)
* And you should have a plan to follow in case of attack

### KMS
* AWS Key Managment Service
* Two-tiered key hierarchy using envelope encrpytion

### Cloud HSM
* Dedicated encrpytion box

### Authentication
#### Options
* Run Microsoft AD within AWS Directory Service
* Connect AWS resources with an existing on-premises MS AD (Ad Conector)
* Set up a ni directory in AWS Cloud (Simple AD)

### Security Token Services
* Generates token to authenticate to AWS

# Reability Pilar
* Test recovery services
* Automatically recover from services
* Scale horizontally to increase aggregate system availability
* Stop guessing capacity
* Manage change in automation

# Performance Pilar
* Use caching
* Mind your instance type

# Cost-optmized
* Adopt a consumption models
* Benefit form economies of scale
* Key services: use tags, auto scaling, spot instances
