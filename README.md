# AWS In Action

## IAM Section

- Identity and Access Management
- Global Service
- Root account created by default, shouldn't be used or shared

### Users & Groups

- Users are people within your organization, and can be grouped
- Groups only contain users, not other groups
- Users don't have to belong to a group, and can belong to multiple groups

### IAM Policies

- Policies: JSON documents
- Define permissions of the users
- Least privilege principle
- IAM policy structure:
    - Version
    - Id: (optional)
    - Statement: one or more individual statements (required)
        - Sid: (optional)
        - Effect: Allow/Deny
        - Action: lists of actions
        - Resource: lists of resources to which action applied to
        - Condition: (optional)

### Password Policy

- Minimum password length
- Require specific character types
- Password expiration
- Prevent password re-use

### Multi Factor Authentication - MFA

- Virtual MFA device
- Universal 2nd Factor (U2F) Security Key
- Hardware Key Fob MFA device
- Hardware Key Fob MFA device for AWS GovCloud (US)

### Access AWS

- AWS Management console
- AWS Command Line interface (CLI)
    - AWS Cloud Shell
- AWS Software Developer Kit (SDK)
- Access Key and Secret Access Key

### IAM Roles for Services

- Some AWS service will need to perform actions on your behalf
- We will assign permissions to AWS services with IAM role

### IAM Security Tools

- IAM Credential Report (account-level)
    - A report that lists all your account's users and the status of their various credentials
- IAM Access Advisor (user-level)
    - Show the service permissions granted to a user and when those services are last accessed
    - To revise your polices

### IAM Section - Summary

- Users: mapped to a physical user, has a password for AWS console
- Groups: contains users only
- Policies: JSON document that outlines permissions for users or groups
- Roles: for EC2 instances or AWS services
- AWS CLI: Manage AWS services using command-line
- AWS SDK: Manage AWS services using a programming language
- Access keys: Access AWS using CLI or SDK
- Audit: IAM credential Reports & IAM Access Advisor

## EC2

- Elastic Compute Cloud = Infrastructure as a Service
- Consist in the capability of :
    - Renting virtual machines (EC2)
    - Storing data on virtual drives (EBS)
    - Distributing load across machines (ELB)
    - Scaling the services using an auto-scaling group (ASG)

### EC2 sizing & configuration options

- Operating system (OS): Linux,Windows,MacOS
- CPU
- RAM
- Storage
    - Network-attached (EBS,EFS)
    - hardware (EC2 instance store)
- Network card
- Firewall rules: security group
- Bootstrap script: EC2 User Data

### EC2 User Data

- EC2 instance user data is the data that you specified in the form of a bootstrap script or configuration parameters,
  while launching your instance.
- Run only once at the instance first start

EC2 instance metadata
EC2 instance metadata is data about your instance that you can use to manage the instance.

### Classic Ports to know

- 22 = SSH (Secure shell) - log into Linux instance
- 21 = FTP (File Transfer Protocol) - upload files into a file share
- 22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
- 80 = HTTP
- 443 = HTTPS
- 3389 = RDP (Remote Desktop Protocol) - log into a windows instance
- ssh -i $pem ec2-user@$public-ip
- chmod 0400 $pem

### EC2 Instance Type

- General Purpose: M,T,A
    - Great for a diversity of workloads such as web servers or code repositories
    - Balance between Compute, Memory and Networking
- Compute Optimized: C
    - Good CPU
    - High performance processors
        - Batch processing workloads
        - Media transcoding
        - High performance web servers
        - High performance computing (HPC)
        - Scientific modeling & machine learning
        - Dedicated gaming servers
- Memory Optimized: R,X,Z
    - Good RAM
    - Fast performance for workloads that process large data sets in memory
- Storage Optimized: I,G,H
    - Great for storage-intensive tasks that require high, sequential read and write access to large data sets on local
      storage

### EC2 Instances Purchasing options

- On-Demand Instances
    - Pay for what you use
        - Linux/Windows - billing person second after the first minute
        - All other systems - billing per hour
- Reserved (1&3 years)
    - Reserved Instances
        - Regional or Zonal
    - Up to 72%
    - Payment options - No upfront/Partial upfront/All upfront
    - Can buy and sell in the Reserved Instance Marketplace
    - Convertible Reserved instances
        - Can change the EC2 instance type/instance family/OS/scope and tenancy
        - Up to 66% discount
- Saving Plans (1&3 years)
    - Commitment to an amount of usage, long workload
    - Up to 72% discount
    - Commit to a certain type of usage ($10/hour for 1/3 years)
    - Usage beyond EC2 Saving plans is billed at the on-demand price
    - Locked to a specific instance family & AWS region
    - Flexible across
        - Instance size
        - OS
        - Tenancy (Host,Dedicated,Default)
- Spot Instances
    - Short workloads, cheap, can lose instances (less reliable)
    - 90% discount
- Dedicated Hosts
    - Book an entire physical server, control instance placement
    - Address compliance requirement and use existing server-bound software licenses
    - Purchasing options
        - On-demand
        - Reserved (1&3 years)
- Dedicated Instances
    - No other customers will share your hardware
    - May share hardware with other instances in same account
    - No control over instance placement
- Capacity Reservation
    - Reserve capacity in a **specific AZ** for any duration
    - No time commitment, no billing discounts
    - Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ

### EC2 Spot instance

- Get up to 90% discount
- Define max spot price and get the instance while current spot price < max
    - 2 minutes time to stop/terminate
- Spot block
    - block spot during 1-6 hours without interruptions
- Spot request
    - one time / persist
    - valid from/until
    - Can only cancel request that are open/active/disabled
    - Cancelling a spot request does not terminate instances
    - Must first cancel a spot request, then terminate the associated Spot instances
- Spot fleets
    - Spot fleets = set of Spot instances + (optional) On-demand instances
    - The Spot fleet will try to meet the target capacity with price constraints
        - Define possible pools: instance type, OS, Availability Zone
        - Can have multiple launch pools
        - Spot fleet stops launching instances when reaching capacity or max cost
    - Strategies to allocate Spot instances
        - lowestPrice
        - diversified
        - capacityOptimized
        - priceCapacityOptimized
    - Spot Fleets allow us to automatically request Spot instances with the lowest price

### Security Group

- Only contain **allow** rules
- All outbound traffic is **authorized** by default
- All inbound traffic is **blocked** by default
- Security groups rules can reference by IP or by security group
- Regulate:
    - Access to ports
    - Authorized IP ranges - IPV4 and IPV6
    - Control of inbound network
    - Control of outbound network
- Can be attached to multiple instances
- Locked down to a region/VPC combination
- Can reference other security groups

## EC2 SAA level

### IP

- Networking has two sorts of IPs. IPv4 and IPv6
- Public IP
    - Machine can be identified on the internet
    - Must be unique across the whole web
    - Can be geo-located easily
- Private IP
    - Machine can be identified on a private network only
    - Must be unique across the private network
    - Two different networks can have same IPs
- Elastic IPs
    - Fixed public IP for your instance
    - Can mask the failure of an instance or software by rapidly remapping the address to another instance to your
      account
    - Only have 5 Elastic IP in your account
    - Try to avoid using it
        - Often reflect poor architectural decisions
        - Use a random IP and register a DNS to it instead
        - Load Balancer and don't use public IP

### Placement Groups

- Cluster - clusters instances into a low-latency group in a single AZ
    - Pros: Great network
    - Cons: If the rack fails, all instances fails at the same time
- Spread - Spread instances across underlying hardware (Max 7 instances per Group per AZ) - critical applications
    - Pros:
        - Can span across AZ
        - Reduced risk is simultaneous failure
        - EC2 instances are on different physical hardware
    - Cons:
        - Limited to 7 instances per AZ per Group
- Partition - Spread instances across many different partitions (which rely on different sets of racks) within an AZ.
  Scales to 100s of EC2 instances per group
    - Up to 7 partitions per AZ
    - Can span across multiple AZs in the same region
    - The instances in a partition do not share racks with the instances in the other partitions
    - EC2 instances get access to the partition information as metadata
    - Use cases: Hadoop, Cassandra, Kafka

### Elastic Network Interface (ENI)

- Logical component in a VPC that represents a virtual network card
- Can have the following attributes
    - Primary Private IPv4, one or more secondary IPv4
    - One Elastic IPv4 per private IPv4
    - One Public Ipv4
    - One or more security groups
    - A MAC address
- Can create ENI independently and attach them on EC2 instances for failover
- Bound to a specific AZ

### EC2 Hibernate

- RAM state is preserved to a file in the root EBS volume
- Instance boot is much faster
- The root EBS volume must be encrypted
- Use cases:
    - Long-running processing
    - Saving the RAM state
    - Service that take time to initialize
- Good to know:
    - Supported Instance families
    - Instance RAM size must less than 150GB
    - AMI
    - Root volume must be EBS, not instance store and large
    - Available for On-Demand, Reserved and Spot instances
    - An instance can NOT be hibernated more than 60 days

## EC2 instance storage

### EBS volume

- An Elastic Block store s a network drive
- Multiple-attache feature for some EBS
- Bound to a specific availability zone
- Have a provisioned capacity
- Delete on Termination attribute
    - Preserve root volume when instance is terminated
- EBS snapshot
    - Make a backup of your EBS at a point in time
    - Not necessary to detach volume to do snapshot, but recommend
    - Can copy snapshots across AZ or Region
    - Snapshots Features
        - EBS snapshot archive tier
            - 75% cheaper
            - Takes within 24 to 72 hours for restoring the archive
        - EBS recycle bin
            - Setup rule to retain deleted snapshots so you can recover them after an accidental deletion
            - Specify retention (1 day to 1 year)
        - Fast Snapshot Restore (FSR)
            - Force full initialization of snapshot to have no latency on the first use ($$$$)

### EBS Volume Types

- gp2/gp3 (SSD 1GB - 16TB): General purpose SSD volume that balances price and performance for a wide variety of
  workloads
    - Cost effective storage, low-latency
    - gp3:
        - Can increase IOPS up to 16000 and throughput up to 1000MiB/s independently
    - gp2:
        - Size of the volume and IOPS are linked
        - 3 IOPS per GB
- io1/io2 (SSD 4GB-16TB): Highest-performance SSD volume for mission-critical low-latency or high-throughput workloads
    - More than 16000 IOPS
    - Great for database workloads
    - Can increase PIOPS independently from storage size
    - Max PIOPS: 64000 for Nitro EC2 instances & 32000 for other
    - io2 have more durability and more IOPS per GiB
    - io2 Block Express (4GB - 64TB)
        - Sub-millisecond latency
        - Max PIOPS: 256000
    - Support EBS multi-attach
- st1 (HDD 125GB - 16TB): Low cost HDD volume designed for frequently accessed, throughput intensive workloads
    - Throughput Optimized HDD
    - Big data, data warehouses, Log processing
- sc1 (HDD same as above): Lowest cost HDD volume designed for less frequently accessed workloads
    - Cold HDD
    - For data is infrequently accessed
- **Only gp2/gp3 and io1/io2 can be used as boot volumes**

### EBS Mull-Attach - io1/io2 family

- Attach the same EBS volume to multiple EC2 instances in same AZ
- Each instance has full read/write permission to the high-performance volume
- Use case:
    - Higher application availability in clustered Linux applications
    - Applications must manage concurrent write operations
- Up to 16 EC2 Instances at a time
- Must use a file system that's cluster-aware

### EBS Encryption

- When create an encrypted EBS volume:
    - Data at rest is encrypted inside the volume
    - All the data in flight moving between the instance and the volume is encrypted
    - All snapshots are encrypted
- Encryption and decryption are handled transparently
- Encryption has a minimal impact on latency
- Copying an unencrypted snapshot allows encryption
- Snapshots of encrypted volumes are encrypted

### AMI

- Amazon Machine Image
- Customization of an EC2 instance
- Built for specific region
- Can launch EC2 instances from
    - A public AMI
    - Your own AMI
    - An AWS marketplace AMI

### EC2 instance store

- High performance hardware disk
- Better I/O performance
- Ephemeral, lose storage if stopped
- Good for buffer/cache/scratch data/temporary content
- Risk of data loss if hardware fails
- Backup and Replication are client responsibility

### Amazon EFS - Elastic File System

- Managed NFS (Network file system) that can be mounted on many EC2
- EFS works with EC2 instances in multi-AZ
- Highly available, scalable, expensive, pay per use
- Compatible with Linux based AMI
- Use NFSv4.1 protocol
- Use security group to control access to EFS
- Performance & Storage Classes
    - EFS Sale
        - Grow to PB network automatically
    - Performance mode (set at EFS creation time)
        - General Purpose
        - Max I/O
    - Throughput Mode
        - Bursting
            - Throughput scales with files system size
        - Provisioned
            - Set your throughput regardless of storage size
        - Elastic
            - Used for unpredictable workloads
    - Storage Classes
        - Storage Tiers (lifecycle management feature)
            - Standard
            - Infrequent access
        - Availability and durability
            - Standard/Regional
            - One Zone
        - Over 90% in cost savings

### EBS vs EFS

- EBS
    - one instance (except multi-attach io1/io2)
    - are locked at the AZ level
    - gp2: IU increases if the disk size increases
    - io1: can increase IO independently
    - To migrate EBS volume across AZ
        - Take snapshot and restore to another AZ
        - Backups use IO
    - Root EBS volumes get terminated by default if the EC2 terminated
- EFS
    - Mount 100s of instances across AZ
    - EFS share websites files
    - Only for Linux instances

### Amazon FSx

3rd party high-performance file system on AWS

- FSx for Lustre
- FSx for Windows File Server
- FSx for NetApp ONTAP

## Scalability & High Availability

- Scalability
    - Vertical Scalability
        - Increase instance size
        - Scale up/down
    - Horizontal Scalability (Elasticity)
        - Increase number of instances
        - Scale out/in
- High Availability
    - Running your application in at least 2 data centers (AZs)

### ELB (Elastic load balancer)

- Load balancing are servers that forward traffic to multiple servers downstream
- Why use Load Balancer
    - Spread load across multiple downstream instances
    - Expose a single point of access (DNS) to your application
    - Seamlessly handle failures of downstream instances
    - Do regular health checks to your instances
    - Provide SSL termination(SSL) for your websites
    - Enforce stickiness with cookies
    - High availability across zones
    - Separate public traffic from private traffic
- Why use an Elastic Load Balancer
    - Managed Load Balancer
        - AWS guarantees that it will be working
        - AWS takes care of upgrades maintenance, high availability
        - AWS provides only a few configuration knobs
    - It costs less to set up your own load balancer, but it will be a lot more effort on your end
    - It's integrated with many AWS offerings/services
- Health Checks
    - Health checks are crucial for load balancers
    - Let LB know if instances it forwards traffics to are available to reply to requests
    - Health check is done on a port and a route
- Types of load balancer on AWS
    - 4 kinds of managed Load Balancers
    - Classic Load Balancer - Deprecated
    - Application load balancer
        - HTTP, HTTPS, WebSocket
        - Static URL
    - Network Load Balancer
        - TCP, TLS(Secure TCP), UDP - layer 4
        - High performance
    - Gateway Load Balancer
        - Operate at layer3 - IP Protocol
        - Route traffic to firewalls
        - Intrusion detection
    - Some LB can be set up as internal(private) or external(public) ELBs
- Load Balancer security groups
    - Load Balancer Security Group
    - Application Security Group: Allow traffic only from Load Balancer

### Application Load Balancer (V2)

- Layer 7 (HTTP)
- Load balancing to multiple HTTP applications across machines (target groups)
- Load Balancer to multiple applications on the same machine (containers)
- Support for HTTP/2 and WebSocket
- Support redirects (From HTTP to HTTPS for example)
- Routing tables to different target groups
    - Routing based on path in URL
    - Routing based on hostname in URL
    - Routing based on Query String, Headers
- ALB are great fit for microservices & container-based application
- Has a port mapping feature to redirect to a dynamic port in ECS
- In comparison, we'd need multiple Classic Load Balancer per application
- Target Groups
    - EC2 instances
    - ECS tasks
    - Lambda functions
    - IP Addresses - must be private IPs
    - ALB can route to multiple target groups
    - Health checks are at the target group level
- Good to know
    - Fixed hostname
    - The application servers don't see the IP of the client directly
        - The true IP of the client is inserted in the header X-Forwarded-For
        - We can also get port (X-Forwarded-Port) and proto (X-Forwarded-Proto)

### Network Load Balancer (v2)

- NLB (Layer4) allow to
    - Forward TCP/UDP traffic to your instances
    - Handle million of requests per seconds
    - Less latency ~ 100ms (vs 400 ms for ALB)
- NLB has **one static IP per AZ** and supports assigning Elastic IP (helpful for whitelisting specific IP)
- NLB are used for extreme performance, TCP or UDP traffic
- Not included in AWS free tier
- Target Groups
    - EC2 instances
    - IP addresses - must be private IPs
    - Application Load Balancer
    - Health checks support the TCP, HTTP and HTTPS Protocols

### Gateway Load Balancer

- Deploy, Scale and manage a fleet of 3rd party network virtual appliances in AWS
- Example: Firewalls, Intrusion Detection and Prevention Systems, Deep Packet Inspection Systems, payload manipulation
- Operates at Layer3 (Network layer) - IP
- Combines the following functions:
    - Transparent Network Gateway - single entry/exit for all traffic
    - Load Balancer - distributes traffic to your virtual appliances
- Uses the GENEVE protocol on port **6081**
- Target Groups
    - EC2 instances
    - IP addresses - must be private IPs

### ELB Sticky Sessions (Session Affinity)

- It's possible to implement stickiness so that the same client is always redirected to the same instance behind a load
  balancer
- This works for Classic/Application/Network Load Balancer
- The "cookie" used for stickiness has an expiration date you control
- Use case: make sure the user doesn't lose the session data
- Enabling stickiness may bring imbalance to the load over the backend EC2 instances
- Cookie Names
    - Application-based Cookies
        - Custom Cookie
            - Generated by the target
            - Can include any custom attributes required by the application
            - Cookie name must be specified individually for each target group
            - Don't use AWSALB, AWSALBAPP, or AWSALBTG
        - Application cookie
            - Generated by the load balancer
            - Cookie name is AWSALBAPP
    - Duration-based Cookies
        - Cookie generated by the load balancer
        - Cookie name is AWSALB for ALB, AWSELB for CLB

### Cross-Zone Load Balancing

- With Cross Zone Load Balancing
    - each load balancer instance distributes evenly across all registered instances in all AZ
- Without Cross Zone Load Balancing
    - Requests are distributed in the instances of the node of the Elastic Load Balancer
- Application Load Balancer
    - Enabled by default (Can be disabled at the target group level)
    - No charge for inter AZ data
- Network Load Balancer & Gateway Load Balancer
    - Disabled by default
    - You pay charges for inter AZ data if enabled
- Classic Load Balancer
    - Disabled by default
    - No charges for inter AZ data if enabled

### SSL/TLS Certificates

- Basics:
    - An SSL Certificate allows traffic between your clients and your load balancer to be encrypted in transit (
      in-flight encryption)
    - SSL refers to Secure Socket Layer, used to encrypt connections
    - TLS refers to Transport Layer Security, which is a newer version
    - TLS certificates are mainly used nowadays
    - Public SSL are issued by Certificate Authorities
    - SSL certificated has an expiration date and must be renewed
- Load Balancer - SSL certificates
    - The load balancer uses an X.509 certificate (SSL/TLS server certificate)
    - Can manage certificate using ACM (AWS Certificate Management)
    - Can upload your certificate alternatively
    - Clients can use SNI (Server Name Indication) to specify the hostname they readch
    - Ability to specify a security policy to support older versions of SSL/TLS
- SSL - Server Name Indication
    - SNI solves the problem of loading multiple SSL certificates onto one web server (to multiple websites)
    - It's a newer protocol and requires the client to indicate the hostname of the target server in the initial SSL
      handshake
    - The server will find the correct certificate, or return the default one
    - Only works for ALB/NLB, CloudFront
    - Does not work for CLB

### Connection Draining

- Feature Naming
    - Connection Draining - CLB
    - Deregistration Delay - ALB&NLB
- Time to complete 'in-flight' requests while the instance is de-registering on unhealthy
- Stop sending new requests to the EC2 instance which is de-registering
- Between 1-3600 seconds (default: 300 seconds)
- Can be disabled
- Set to low value if your request is short

### Auto Scaling Group

- In real-life, the load on your websites and application can change
- In the cloud, you can create and get rid of servers very quickly
- The goal of ASG is to:
    - Scale out (add EC2 instances) tom match an increase load
    - Scale in (remove EC2 instances) to match a decrease load
    - Ensure we have a minimum and maximum number of EC2 instances running
    - Automatically register new instances to load balancer
    - Re-create an EC2 instance in case a previous one is terminated (ex: if unhealthy)
- ASG are free
- ASG Attributes
    - A Launch Template (Older Launch Configurations are deprecated)
        - AMI + Instance Type
        - EC2 User Data
        - EBS Volumes
        - Security Groups
        - SSH Key Pair
        - IAM roles for EC2 instances
        - Network + Subnets Information
        - Load Balancer Information
    - Min size / Max size / Initial Capacity
- CloudWatch Alarms & Scaling
    - Scale ASG based on CloudWatch alarms
    - An alarm monitors a metric (such as an Average CPU, or a custom metric)
    - Metrics such as average CPU are computed for the overall ASG instances
    - Based on the alarm, we can:
        - Scale-out policies
        - Scale-in policies

### ASG Dynamic Scaling Policies

- Strategies
    - Manual scaling
    - Dynamic scaling
        - Target tracking scaling
            - Most simple and easy to set up
            - Example: Want the average ASG CPU to stay at around 40%
        - Simple/step scaling
            - When a CloudWatch alarm is triggered (example CPU > 70%), then add 2 units
            - When a CloudWatch alarm is triggered (example CPU < 30%), then remove 1 unit
    - Scheduled scaling
        - Anticipate a scaling based on known usage pattern
    - Predictive scaling
        - Continuously forecast load and schedule scaling ahead
- Good metrics to scale on
    - CPU Utilization
    - Request Count per Target
    - Average Network In/Out
    - Any custom metric
- Scaling Cooldowns
    - After a scaling activity happens, you are in the cooldown period (default 300 seconds)
    - During the cooldown period, the ASG will not launch or terminate additional instances ( to allow metrics to
      stabilize)
    - Advice: Use a ready-to-use AMI to reduce configuration time in order to be serving request faster and reduce the
      cooldown period

## AWS Fundamental: RDS + Aurora + ElasticCache

### Amazon RDS Overview

- RDS stands for Relational Database Service
- It's a managed DB service for DB use SQL as query language
- It allows you to create databases in the cloud that managed by AWS
    - Postgres
    - MySQL
    - MariaDB
    - Oracle
    - Microsoft SQL Sever
    - Aurora (AWS Proprietary database)

### Advantage over using RDS versus deploying DB on EC2

- RDS is a managed service
    - Automated provisioning, OS patching
    - Continuous backups and restore to specific timestamp (Point in time restore)
    - Monitoring dashboards
    - Read replicas for improved read performance
    - Multi AZ setup for DR (Disaster Recovery)
    - Maintenance windows for upgrades
    - Scaling capability (vertical and horizontal)
    - Storage backend by EBS (gp2 or io1)
- But you can't SSH into your instances

### RDS - Storage Auto Scaling

- Increase your storage on your RDS DB instance dynamically
- When RDS detects you are running out of free database storage, it scales automatically
- Avoid manually scaling your database storage
- You have to set the Maximum Storage Threshold
- Automatically modify storage if:
    - Free storage is less than 10% of allocated storage
    - Low-storage lasts at least 5 minutes
    - 6 hours have passed since last modification
- Useful for applications with unpredictable workloads
- Supports all RDS database engines

### RDS Read Replicas for read scalability

- Up to 15 read replicas
- Within AZ, Cross AZ or Cross Region
- Replications are ASYNC, so reads are eventually consistent
- Replicas can be promoted to their own DB
- Application must update the connection string to leverage read replicas
- Use cases
    - Run a reporting application to run some analytics
    - Read replicas are used for SELECT only kind of statements
- Network cost
    - In AWS there's a network cost when data goes from one AZ to another
    - For RDS read replicas with the same region, you don't pay for the fee

### RDS Multi AZ (Disaster Recovery)

- SYNC replication
- One DNS name - automatic app failover to standby
- Increase availability
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling
- Note: The Read Replicas be setup as Multi AZ for Disaster Recovery (DR)
- From Single AZ to Multi-AZ
    - Zero downtime operation
    - Just click on 'modify' for the database
    - The following happens
        - A snapshot is taken
        - A new DB is restored from the snapshot in a new AZ
        - Synchronization is established between two databases

### RDS Custom

- Managed Oracle and Microsoft SQL Server Database with OS and database customization
- RDS: Automated setup, operation and scaling of database in AWS
- Custom: access to the underlying database and OS, so you can:
    - Configure settings
    - Install patches
    - Enable native features
    - Access the underlying EC2 instance using SSH or SSH Session Manager
- De-active Automation Mode to perform your customization, better to take a DB snapshot before
- RDS vs RDS Custom:
    - RDS: entire database and the OS to be managed by AWS
    - RDS custom: full admin access to the underlying OS and the database

### Amazon Aurora

- Aurora is a proprietary technology from AWS (not open-sourced)
- Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or
  MySQL database)
- Aurora is "AWS cloud optimized" and claims 5x percentage improvement over MySQL on RDS, over 3 x the performance of
  Postgres on RDS
- Aurora storage automatically grows in increments of 10 GB, up to 128TB
- Aurora can have up to 15 replicas, and the replication process is faster than MySQL (sub 10 ms replica lag)
- Failover in Aurora is instantaneous. It's High Availability native
- Aurora coast more than RDS (20% more) - but it's more efficient

### Aurora High availability and Read Scaling

- 6 copies of your data across 3 AZ:
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 needed for reads
    - Self healing with peer-to-peer replication
    - Storage is striped across 100s of volumes
- One Aurora Instance takes write (master)
- Automated failover for master in less than 30 seconds
- Master + up to 15 Aurora Read Replicas server reads
- Support for Cross Region Replication
- Aurora DB Cluster
    - Writer Endpoint
        - Pointing to master
    - Reader Endpoint
        - Connection Load Balancing

### Features of Aurora

- Automatic fail-over
- Backup and Recovery
- Isolation and security
- Industry compliance
- Push-button compliance
- Automated Patching with Zero Downtime
- Advanced Monitoring
- Routine Maintenance
- Backtrack: restore data at any point of time without using backups

### Aurora Advanced Concept

- Aurora Replicas - Auto Scaling
- Aurora - Custom Endpoints
    - Define a subset of Aurora Instances as a Custom Endpoint
    - Example: Run analytical queries on specific replicas
    - The Reader Endpoint is generally not used after defining Custom Endpoints
- Aurora Serverless
    - Automated database instantiation and auto-scaling based on actual usage
    - Good for infrequent intermittent or unpredictable workloads
    - No capacity planning needed
    - Pay per second, can be more cost-effective
- Aurora Multi-Master
    - In case you want continuous write availability for the writer nodes
    - Every node does R/W - vs promoting a Read Replica as the new master
- Global Aurora
    - Aurora Cross Region Read Replicas
        - Useful for disaster recovery
        - Simple to put in place
    - Aurora Global Database (recommended)
        - 1 Primary Region (read/write)
        - Up to 5 secondary (read-only) regions, replication lag is less than 1 second
        - Up to 16 Read Replicas per secondary region
        - Helps for decreasing latency
        - Promoting another region (for disaster recovery) has an RTO of < 1 minute
        - Typical cross-region replication takes less than 1 second
    - Aurora Machine Learning
        - Enables you to add ML-based predications to your applications via SQL
        - Simple, optimized and secure integration between Aurora and AWS ML services
        - Supported services
            - Amazon SageMaker
            - Amazon Comprehend
        - Use cases: fraud detection, ads targeting, sentiment analysis, product recommendations

### RDS & Aurora - Backup and Monitoring

- RDS Backups
    - Automated backups
        - Daily full backup of the database (during the backup window)
        - Transaction logs are backed-up by RDS every 5 minutes
        - ability to restore to any point in time (from oldest backup to 5 minutes ago)
        - 1 to 35 days retention, set 0 to disable automated backups
    - Manual DB snapshots
        - Manually triggered by the user
        - Retention of backup for as long as you want
    - Trick: in a stopped RDS database, you will still pay for storage.
      If you plan on stopping it for a long time, you
      should snapshot&restore instead
- Aurora Backups
    - Automated backups
        - 1 to 35 days (can not be disabled)
        - point-int-time recovery in that timeframe
    - Manual DB Snapshots
        - Manually triggered by the user
        - Retention of backup for as long as you want
- RDS & Aurora Restore options
    - Restoring a RDS/Aurora backup or a snapshot create a new database
    - Restoring MySQL RDS database from S3
        - Create a back-up from an on-premises database
        - Store it on Amazon S3 (object storage)
        - Restore the backup file onto a new RDS instance running MySQL
    - Restoring MySQL Aurora cluster from S3
        - Create a backup from your on-premises database using Percona XtraBackup
        - Store the backup file on Amazon S3
        - Restore the backup file onto a new Aurora cluster running MySQL
- Aurora Database Cloning
    - Create a new Aurora DB cluster from an existing one
    - Faster than snapshot & restore
    - Uses copy-on-write protocol
        - The new DB cluster uses the same data volume as the original DB cluster (fast and efficient - no copying is
          needed)
        - When updated are made to the new DB cluster data, the additional storage is allocated and data is copied to be
          separated
    - Very fast and cost-effective
    - Useful to create a "staging" database from a "production" database without impacting the production database

### RDS & Aurora Security

- At-rest encryption:
    - Database master &* replicas encryption using AWS KMS - must be defined at launch time
    - If the master is not encrypted, the read replicas cannot be encrypted
    - To encrypt an unencrypted database, go through a DB snapshot & restore as encrypted
- In-fligt encryption: TLS-ready by default, use the AWS TLS root certificates client side
- IAM Authentication: IAM roles to connect to your database (instead of username/pw)
    - MySQL
    - MariaDB
    - PostgreSQL
- Security Groups: Control Network access to your RDS/Aurora DB
- No SSH available except on RDS custom
- Audit logs can be enabled and sent to AWS CloudWatch Logs for longer retention

### Amazon RDS Proxy

- Fully Managed database proxy for RDS
- Allows apps to pool and shared DB connections established with the database
- Improving database efficient by reducing the stress on database resources and minimizing open connections (and time
  outs)
- Serverless, autoscaling, highly available (mutli-AZ)
- Reduced RDS & Aurora failover time by up 66%
- Supports RDS(MySQL, PostgresSQL, MariaDB,MS SQL Server) and Aurora (MySQL,PostgresSQL)
- No code changes required for most apps
- Enforce IAM Authentication for DB, and securely store credentials in AWS Secret Manager
- RDS Proxy is never publicly accessible (must be accessed from VPC)

### Amazon ElastiCache

- Overview
    - Get managed Redis or Memcached
    - Caches are in-memory databases with really high performance, low latency
    - Helps reduce load off to databases for read intensive workloads
    - Helps make your application stateless
    - AWS takes care of OS maintenance / patching, optimization, setup, configuration, monitoring, failure recovery and
      backups
    - Using ElastiCache involves heavy application code changes
- SA - DB Cache
    - Applications queries ElastiCache, if not available, get from RDS and store in ElastcCache
    - Helps relieve load in RDS
    - Cache must have an invalidation strategy to make sure only the most current data is used in there≈
- SA - User Session Store
    - Users logs into any of the application
    - The application writes the session data into ElastiCache
    - The user hits another instance of our application
    - The instance retrieves the data and the user is alraedy logged in
- Redis vs Memcached
    - Redis
        - Multi AZ with Auto-Failover
        - Read Replicas to scale reads and have high availability
        - Data durability using AOF persistence
        - Backup and restore features
        - Supports Sets and Sorted Sets
    - Memcached
        - Multi-node for partitioning of data (sharding)
        - No high availability (replication)
        - Non persistent
        - No backup and restore
        - Multi-threaded architecture

### ElastiCache - Cache Security

- ElastiCache supports IAM Authentication for Redis
- IAM polices on ELastiCache are only used for AWS API-level security
- Redis Auth
    - password/token for Redis cluster
    - Extra level of security for your cache (on top of security group)
    - Support SSL in flight encryption
- Memcached
    - Supports SASL-based authentication (advanced)

### Patterns for ELasitCache

- Lazy Loading: all the read data is cached, data can become stale in cache
- Write Through: Adds or update data in the cache when written in DB (no stale data)
- Session Store: store temporay session data in cache (using TTL features)
- Redis use case:
    - Gaming Leaderboards are computationally complex
    - Redis Sorted sets gurantee both uniqueness and element ordering
    - Each time a new element added, it's randked in real time, then added in correct order

### Classic ports

- PostgreSQL: 5432
- MySQL: 3306
- Oracle RDS: 1521
- MSSQL Server: 1433
- MariaDB: 3306
- Aurora: 5432/3306

## Route 53

### What is DNS?

- Domain Name System which translates the human friendly hostnames into the machine IP addresses
- DNS is the backbone of the internet
- DNS uses hierarchical naming structure
- DNS Terminologies
    - Domain Registrar: Amazon Route 53, GoDaddy...
    - DNS Records: A, AAAA, CNAME, NS...
    - Zone File: contains DNS records
    - Name Server: resolves DNS queries (Authoritative or Non-Authoritative)
    - Top Level Domain (TLD) : .com, .us, .gov, .org...
    - Second Level Domain (SLD): amazon.com, google.com
    - Root -> TLD -> SLD -> Sub Domain -> FQDN(fully Qualified Domain Name)

### Amazon Route53

- A highly available, scalable, fully managed and Authoritative DNS
    - Authoritative = the customer can update the DNS records
- Route 53 is also a Domain Registrar
- Ability to check the health of your resources
- The only AWS service which provides 100% SLA
- 53 is a reference to the traditional DNS port

### Route 53 - Records

- Define how you want to route traffic for a domain
- Each record contains:
    - Domain/subdomain Name
    - Record Type - eg A or AAAA
    - Value
    - Routing Policy
    - TTL - amount of time the record cached at DNS Resolvers
- Route53 supports the following DNS recoprd types:
    - A/AAAA/CNAME/NS
    - CAA/DS/MX/NAPTR/PTR/SOA/TXT/SPF/SRV

### Route 53 - Record Types

- A - maps a hostname to IPv4
- AAAA- maps a host name to IPv6
- CNAME - maps a hostname to another hostname
    - The target is a domain
    - Can't create a CNAME record for the top node of a DNS namespace(Zone Apex)
        - Example: Can't create for example.com, but you can create for www.example.com
- NS - Name Servers for the Host name which must have an A or AAAA record
    - ted Zone
    - Hosted Zones: A container for records that define how to route traffic to a domain and its subdomains
        - Public Hosted Zones - contains records that specify how to route traffic on the Internet (public domain names)
        - Private Hosted Zones - contain records that specify how you route traffic within one or more VPCs (private
          domain names)
        - You pay $0.50 per month per hosted zone

### Route53 - Records TTL (Time to Live)

- High TTL - e.g, 24hr
    - Less traffic on Route53
    - Possibly outdated records
- Low TTL - e.g, 60 sec
    - More traffic on Route53
    - Records are outdated for lesstime
    - Easy to change records
- Except for Alias records, TTL is mandatory for each DNS record

### CNAME vs Alias

- AWS Resources expose an AWS hostname
- CNAME:
    - Points a hostname to any other hostname
    - ONLY FOR NON ROOT DOMAIN
- Alias:
    - Points a hostname to an AWS Resource
    - Works for ROOT DOMAIN and NON ROOT DOMAIN
    - Free of charge
    - Native health check

### Route53 - Alias Records

- Map a hostname to an AWS resource
- An extension to DNS functionality
- Automatically recognizes changes in the resource's IP addresses
- Unlike CNAME, it can be used for the top node of a DNS namespace (Zone Apex) e.g: example.com
- Alias Record is always of type A/AAAA for AWS resources(IPv4/IPv6)
- Can't set TTL
- Route53 - Alias Record Targets
    - Elastic Load Balancer
    - CloudFront Distributions
    - API Gateway
    - Elastic Beanstalk environment
    - S3 Websites
    - VPC Interface Endpoints
    - Global Accelerator
    - Route53 record in the same hosted zone
    - Can not set an ALIAS record for an EC2 DNS name

### Route53 - Routing Policies

- Define how Route53 responds to DNS queries
- Don't get confused by the word "Routing"
    - Not the same as Load Balancer routing which routes the traffic
    - DNS does not route any traffic, it only responds to the DNS queries
- Route53 Supports the following Routing Policies
    - Simple
    - Weighted
    - Failover
    - Latency Based
    - Geolocation
    - Multi-Value Answer
    - Geoproximilty (using Route53 Traffic Flow feature)

### Routing Policies - Simple

- Typically, route traffic to a single resource
- Can specify multiple values in the same record
- If multiple values are returned, a random one is chosen by the client
- When Alias enabled. specify only one AWS resource
- Can't be associated with Health Checks

### Routing Policies - Weighted

- Control the % of the requests that go to each specify resource
- Assign each record a relative weight
    - Weights don't need to sum up to 100
    - traffic = Weight for a specify record / Sum of all the weights for all records
- DNS records must have the same name and type
- Can be associated with Health Checks
- Use cases: load balancing between regions, testing new application versions
- Assign a weight of 0 to a record to stop sending traffic to a resource
- If all records have weight of 0, then all records will be returned equally

### Routing Policies - Latency-based

- Redirect to the resource that has the least latency close to us
- Super helpful when latency for users is a priority
- Latency is based on traffic between users and AWS Regions
- Can be associated with Health Checks (has a failover capability )

### Route53 - Health Checks

- HTTP Health Checks are only for public resources
- Health Check => Automated DNS Failover:
    - Health checks that monitor an endpoint (application, server, other AWS resource)
    - Health checks that monitor other health checks (Calculated Health Checks)
    - Health checks that monitor CloudWatch Alarms (full control) - e.g, throttles of DynamoDB, alarms on RDS, custom
      metrics...(helpful for private resources)
- Health Checks are integrated with CW metrics

### Health Checks - Monitor an Endpoint

- About 15 global health checkers will check the ednpoint health
    - Healthy/Unhealthy Threshold - 3 (default)
    - Interval - 30 sec(can set to 10 sec - higher cost)
    - Supported protocol: HTTP, HTTPS and TCP
    - If > 18% of health checkers repost the endpoint is healthy, Route53 consider is Healthy. Otherwise, it's Unhealthy
    - Ability to choose which locations you want Route53 to use
- Health Checks pass only when the endpoint responds with 2xx and 3xx status codes
- Health Checks can be setup to pass/fail based on the text in the first 5120 bytes of the response
- Configure your router/firewall to allow incoming requests from Route53 Health Checkers

### Route53 - Calculated Health Checks

- Combine the results of multiple Health Checks into a single Health Check
- You can use OR, AND, or NOT
- Can monitor up to 256 Child Health Checks
- Spcify how many of the health checks need to pass to make the parent pass
- Usage: perform maintenance to your website without causing all health checks to fail

### Health Checks - Private Hosted Zones

- Route53 health checkers are outside the VPC
- They are't access private endpoints(private VPC or on-premises resource)
- You can create a CloudWatch Metric and associate a CloudWatch Alarm, then create a Health Check that check the alarm
  itself

### Routing Policies - Failover (Active-Passive)

- Primary instance -> Secondary - Disaster Recovery

### Routing Policies - Geolocation

- Different from Latency-based
- This routing is based on user location
- Specify location by Continent, Country or by US State(if there's overlapping, most precise location selected)
- Should create a "Default" record (in case there's no match on location)
- Use cases: website localization, restrict content distribution, load balancing
- Can be associated with Health Checks

### Geoproximity Routing Policy

- Route traffic to your resources based on the geographic location of users and resources
- Ability to shift more traffic to resources based on the defined bias
- To change the size of the geographic region, specify bias values:
    - To expand(1 to 99) - more traffic to the resource
    - To shrink(-1 to -99) - less traffic to the resource
- Resources can be:
    - AWS resources (specify AWS region)
    - Non-AWS resources (specify Latitude and Longitude)
- You must use Route53 Traffic Flow(advanced) to use this feature

### Routing Policies - IP-based Routing

- Routing is based on clients'IP addresses
- You provide a list of CIDRs for your clients and the corresponding endpoints/locations (user-IP-to-endpoint mappings)
- Use cases: Optimize performance, reduce network costs....
- Example: route and users from a particular ISP tp a specific endpoint

### Routing Policies - Multi-Value

- Use when routing traffic to multiple resources
- Route53 return multiple values/resources
- Can be associated with Health Checks (return only values for healthy resources)
- Up to 8 healthy records are returned for each Multi-Value query
- Multi-Value is not a substitute for having an ELB

### Domain Registrar vs DNS Service

- You buy or register your domain name, with a Domain Registrar typically by paying annual charges
- The Domain Registrar usually provides you with a DNS service to manage your DNS records
- But you can use another DNS service to manage your DNS records
    - Create a Hosted Zone in Route53
    - Update NS Records on 3rd party website to use Route53 Name Servers

## Solution Architecture Discussions

### Stateless Web App

- Public vs Private IP and EC2 instances
- Elastic IP vs Route53 vs Load Balancers
- Route53 TTL, A records and Alias Records
- Maintaining EC2 instances manually vs Auto Scaling Groups
- MultiAZ to survive disasters
- ELB Health Checks
- Security Group Rules
- Reservation of capacity for costing saving when possible
- Considering 5 pillars for a well-architected application: costs, performance, reliability, security, opertional
  excellence

### Stateful Web App

- ELB sticky sessions
- Web clients for storing cookies and making our web app stateless
- ElastiCache
    - For storing sessions (alternative: DynamoDB)
    - For caching data from RDS
    - Multi AZ
- RDS
    - For storing user data
    - Read replicas for scaling reads
    - Multi AZ for disaster recovery
- Tight Security with security groups referencing each other
- Aurora Database to have easy Multi-AZ and Read-Replicas
- Storing data in EBS (single instance application)
- Vs Storing data in EFS (distributed application)

### Instantiating Applications quickly

- When launching a full stack(EC2,EBS,RDS), it can take time to:
    - Install applications
    - Insert initial(or recovery) data
    - Configure everything
    - Launch the application
- EC2 instances:
    - Use a Golden AMI: install your applications, OS dependencies etc... beforehand and launch your EC2 instance from
      the Golden AMI
    - Bootstrap using User Data: For dynamic configuration, use User Data scripts
    - Hybrid: mix Golden AMI and User Data (Elastic Beanstalk)
- RDS Databases:
    - Restore from a snapshot: the database will have schemas and data ready
- ENS Volumes:
    - Restore from a snapshot: the disk will already be formatted and have data

### Elastic Beanstalk

- Developer centric view of deploying an application on AWS
- It uses all the components we've seen before: EC2, ASG, ELB, RDS...
- Managed service
    - Automatically handles capacity provisioning, load balancing, scaling, application health monitoring, instance
      configuration...
    - Just the application code is the responsibility of the developer
- We still have full control over the configuration
- Components:
    - Application: collection of Elastic Beanstalk components(environments, versions, configurations...)
    - Application Version: an iteration of your application code
    - Environment:
        - Collection of AWS resources running an application version ( only one application version at a time)
        - Tiers: Web Server Environment Tier & Worker Environment Tier
        - You can create multiple environments (dev,test,prod...)
- Deployment Modes:
    - Single Instance (Great for dev)
    - High Availability with Load Balancer (Great for prod)

## Amazon S3

- One of the main building blocks of AWS
- It's advertised as "infinitely scaling" storage

### Amazon S3 Use cases

- Backup and storage
- Disaster Recovery
- Archive
- Hybrid Cloud Storage
- Application hosting
- Data lakes & big data analytics
- Software delivery
- Static website

### Amazon S3 - Buckets

- Amazon S3 allows people to store objects(files) in "buckets"(directories)
- Buckets must have a globally unique name(across all regions all accounts)
- Buckets are defined at the region level
- S3 looks like a global service but buckets are created in a region
- Naming convention
    - No uppercase, No underscore
    - 3-63 characters long
    - Not an IP
    - Must start with a lowercase letter or number
    - Must NOT start with the prefix xn--

### Amazon S3 - Objects

- Objects(files) have a key
- The key is the FULL path
- The key is composed of prefix + object name
- No concept of "directories," just keys with very long names that contains slashes
- Object values are the content of the body
    - Max object size is 5TB (5000GB)
    - If uploading more than 5GB, must use "multi-part upload"
- Metadata (list of text key/value pairs - system or user metadata)
- Tags (Unicode key/value pair - up tp 10) - useful for security/lifecycle

### Amazon S3 - Security

- User-Based
    - IAM polices - which API calls should be allowed for a specific user from IAM
- Resource-Based
    - Bucket policies - bucket wide rules from the S3 console - allows cross account
    - Object Access Control List(ACL) - finer grain (can be disabled)
    - Bucket Access Control List(ACL) - less common (can be disabled)
- Note: an IAM principal can access an S3 object if:
    - The user IAM permissions ALLOW it OR the resource policy ALLOWS it
    - AND there's no explicit DENY
- Encryption: encrypt objects in Amazon S3 using encryption keys
- S3 Bucket Policies
    - JSON based policies
        - Resources: buckets and objects
        - Effect: Allow/Deny
        - Actions: Set of API to Allow or Deny
        - Principal: The account or user to apply the policy to
    - Use S3 bucket for policy to:
        - Grant public access to the bucket
        - Force objects to be encrypted at upload
        - Grant access to another account(Cross Account)
- Bucket settings for Block Public Access
    - These settings were created to prevent company data leaks
    - If you know your bucket should never be public, leave these on
    - Can be set at the account level

### Amazon S3 - Static Website Hosting

- S3 can host static websites and have them accessible on the Internet
- If you get 403, make sure the bucket policy allows public reads

### Amazon S3 - Versioning

- Can version your files in Amazon S3
- It's enabled at the bucket level
- Same key overwrite will change the version
- It's best practice to version your buckets
    - Protect against unintended deletes(ability to restore a version)
    - Easy roll back to previous version
- Notes:
    - Any file is not versioned prior to enabling version will have version "null"
    - Suspending versioning does not delete the previous versions

### Amazon S3 - Replication (CRR&SRR)

- Must enable Versioning in source and destination buckets
- Cross-Region Replication (CRR)
- Same-Region Replication (SRR)
- Buckets can be in different AWS accounts
- Copying is asynchronous
- Must give proper IAM permissions to S3
- Use cases:
    - CRR - compliance, lower latency access, replication across accounts
    - SRR - log aggregation, live replication between production and test accounts
- Notes:
    - After you enable Replication, only new objects are replicated
    - Optionally, you can replicate existing objects using S3 Batch Replication
        - Replication existing objects and objects that failed replication
    - For DELETE operations:
        - Can replicate delete markers from source to target (optional setting)
        - Deletions with a version ID are not replicated (to avoid malicious deletes)
    - There is no "chaining" of replication

### S3 storage classes

- Overall
    - Amazon S3 Standard - General Purpose
    - Amazon S3 Standard-Infrequent Access (IA)
    - Amazon S3 One Zone-Infrequent Access
    - Amazon S3 Glacier Instant Retrieval
    - Amazon S3 Glacier Flexible Retrieval
    - Amazon S3 Glacier Deep Archive
    - Amazon S3 Intelligent Tiering
    - Can move between classed manually or using S3 Lifecycle configurations
- S3 Durability and Availability
    - Durability
        - High durability (11 9's) of objects across multiple AZ
        - Same for all storage classes
    - Availability:
        - Measures how readily available a service is
        - Varies between on storage class
- S3 Standard - General Purpose
    - 4 9's Availability
    - Used for frequently accessed data
    - Low latency and high throughput
    - Sustain 2 concurrent facility failures
    - Use Cases: Big Data analytics, mobile&gaming applications, content distributions
- S3 Storage Classes -Infrequent Access
    - For data that is less frequently accessed, but requires rapid access when needed
    - Lower cost than S3 standard
    - Standard-IA
        - Use cases: Disaster Recovery, backups
    - One Zone-IA
        - High durability in a single AZ; data lost when AZ is destroyed
        - 99.5% Availability
- Amazon S3 Glacier Storage Classes
    - Low-cost object storage meant for archiving/backup
    - Price for storage + object retrieval cost
    - S3 Glacier Instant Retrieval
        - Millisecond retrieval, great for data accessed once a quarter
        - Minimum storage duration of 90 days
    - S3 Glacier Flexible Retrieval (formerly Amazon S3 Glacier)
        - Expedited(1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours) - free
        - Minimum storage duration of 90 days
    - S3 Glacier Deep Archive - for long term storage
        - Standard (12 hours), Bulk(48 hours)
        - Minimum storage duration of 180 days
- S3 Intelligent-Tiering
    - Small monthly monitoring and auto-tiering fee
    - Move objects automatically between Access Tiers based on usage
    - No retrieval charges

## Advanced Amazon S3

### Amazon S3 - Moving between Storage Classes

- You can transition objects between storage classes
- For infrequently accessed object, move them to Standard IA
- For archive objects that you don't need fast access to, move them to Glacier or Glacier Deep Archive
- Moving objects can be automated using a Lifecycle Rules

### Amazon S3 - Lifecycle Rules

- Transition Actions - configure objects to transition to another storage class
    - Move objects to Standard IA class 60 days after creation
    - Move to Glacier for archiving after 60 months
- Expiration actions- configure objects to expire(delete) after some time
    - Access log files can be set to delete after a 365 days
    - Can be used to delete old versions of files (if versioning is enabled)
    - Can be used to delete incomplete Multi-Part uploads
- Rules can be created for a certain prefix
- Rules can be created for certain objects Tags

### Amazon S3 Analytics - Storage Class Analysis

- Help you decide when to transition objects to the right storage class
- Recommendations for Standard and Standard IA
    - Does not work for One-Zone IA or Glacier
- Report is updated daily
- 24 to 48 hours to start seeing data analysis
- Good first step to put together Lifecycle Rules (or improve them)

### S3 - Requester Pays

- In general, bucket owners pay for all Amazon S3 storage and data transfer costs ass ociated with their bucket
- With Requester Pay buckets, the requester instead of the bucket owner pays the cost of the request and the data
  download from the bucket
- Helpful when you want to share large datasets with other accounts
- The requester must be authenticated in AWS (can not be anonymous)

### S3 Event Notifications

- Object created, removed, restore, replication...
- Object name filtering possible
- Use case: generate thumbnail of images upload to S3
- Can create as many S3 events as desired
- S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer
- IAM permissions:
    - SNS Resource (Access) Policy
    - SQS Resource (Access) Policy
    - Lambda Resource (Access) Policy
- With Amazon EventBridge
    - Advanced filtering options with JSON rules (metadata, object size, name...)
    - Multiple Destinations - ex Step Functions, Kinesis Streams / Firehose...
    - EventBridge Capabilities - Archive, Replay Events, Reliable delivery

### S3 Performance

- Baseline Performance
    - Amazon S3 automatically scales to high reuqest rates, latency 100-200ms
    - Your application can achieve at least 3500 PUT?COPY?POST?DELETE ir 550 GET?HEAD requests per second per prefix in
      a bucket
    - There are no limits to the number of prefixes in a bucket
    - If you spread reads across all your prefixes evenly, you can achieve 22000 requests per second for GET and HEAD
- Multi-Part upload:
    - Recommend for files > 100MB, must use for files > 5GB
    - Can help parallelize uploads (speed up transfers)
- S3 Transfer Acceleration
    - Increase transfer speed by transferring file to an AWS edge location which forward the data to the S3 bucket in
      the target region
    - Compatible with multi-part upload
- S3 Byte-Range Fetches
    - Parallelize GETs by requesting specific byte ranges
    - Better resilience in case of failures
    - Can be used to speed up downloads
    - Can be used to retrieve only partial data (for example the head of a file)

### S3 Select & Glacier Select

- Retrieve less data using SQL by performing server-side filtering
- Can filter by rows&columns (simple SQL statements)
- Less network transfer, less CPU cost client-side

### S3 Batch Operations

- Perform bulk operations on existing S3 objects with a single request, example:
    - Modify object metadata&properties
    - Copy objects between S3 buckets
    - Encrypt un-encrypted objects
    - Modify ACLs, tags
    - Restore objects from S3 Glacier
    - Invoke Lambda function to perform custom action on each object
- A job consists of a list of objects, the action to perform, and optional parameters
- S3 Batch Operations manages retries, tracks progress, send completion notifications, generate reports...
- You can use S3 Inventory to get object list and use S3 Select to filter you objects

## Amazon S3 Security

### Amazon S3 - Object Encryption

- You can encrypt objects in S3 buckets using one of 4 methods
- Server-Side Encryption (SSE)
    - Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3) - Enable by Default
        - Encrypts S3 objects using keys handled, managed and owned by AWS
    - Server-Side Encryption with KMS Keys stored in AWS KMS (SSE-KMS)
        - Leverage AWS Key Management Service (KMS) to manage encryption keys
    - Server-Side Encryption with Customer-Provide Keys (SSE-C)
        - When you want to manage your own encryption keys
- Client-Side Encryption
- It's important to understand which one are for which situation for the exam

### Amazon S3 Encryption - SSE-S3

- Encryption using keys handled managed, and owned by AWS
- Object is encrypted server-side
- Encryption type is AES-256
- Must set header "x-amz-server-side-encryption":"AES256"
- Enable by default for new buckets & new objects

### Amazon S3 Encryption - SSE-KMS

- Encryption using keys handled and managed by AWS KMS
- KMS advantages: user control + audit key usage using CloudTrail
- Object is encrypted server side
- Must set header "x-amz-server-side-encryption":"aws:kms"
- Limitation
    - Maybe impacted by the KMS limits
    - When upload, it calls the GenerateDataKey KMS API
    - When download, it calls the Decrypt KMS API
    - Count towards the KMS quota pert second (5500, 10000, 30000 req/s based on region)
    - Can request a quota increase using the Service Quotas Console

### Amazon S3 Encryption - SSE-C

- Server-Side Encryption using keys fully managed by the customer outside of AWS
- Amazon S3 does NOT store the encryption key you provide
- HTTPS must be used
- Encryption key must provide in HTTP headers, for every HTTP request made

### Amazon S3 Encryption - Client-Side Encryption

- Use client libraries such as Amazon S3 Client-Side Encryption Library
- Clients must encrypt data themselves before sending to Amazon S3
- Clients must decrypt data themselves when retrieving from Amazon S3
- Customer fully manages the keys and encryption cycle

### Amazon S3 - Encryption in transit (SSL/TLS)

- Encryption in flight is also called SSL/TLS
- Amazon S3 exposed two endpoints:
    - HTTP Endpoint - non encrypted
    - HTTPS Endpoint - encryption in flight
- HTTPS is recommended
- HTTPS is mandatory for SSE-C
- Force encryption is Transit: aws:SecureTransport

### Amazon S3 Encryption - DSSE-KMS

- Dual-layer Server-Side encryption
- Double encryption based on KMS

### Amazon S3- Default Encryption vs Bucket Policies

- SSE-S3 encryption is automatically applied to new objects stored in S3 bucket
- Optionally, you can "force encryption" using a bucket policy and refuse any API call to PUT an S3 object without
  encrypt ion headers (SSE-KMS or SSE-C)

### S3 CORS

- What is CORS?
    - Cross-Origin Resource Sharing (CORS)
    - Origin = scheme(protocol) + host(domain) + port
    - Web Browser based mechanism to allow requests to other origins while visiting the main origin
    - The results won't be fulfilled unless the other origin allows for the requests, using CORS Headers
- Amazon S3 - CORS
    - If a client makes a cross-region request on our S3 bucket, we need to enable the correct CORS headers
    - You can allow for a specific origin or for * (all regions)

### Amazon S3- MFA Delete

- MFA (Multi-Factor Authentication) - force users to generate a code on a device (usually a mobile phone or hardware)
  before doing important operation on S3
- MFA will be required to:
    - Permanently delete an object version
    - Suspend Versioning on the bucket
- MFA won't be required to:
    - Enable Versioning
    - List deleted versions
- To use MFA delete. Versioning must be enabled on the bucket
- Only the bucket owner(root account) can enable/disable MFA Delete

### S3 Access Logs

- For audit purpose, you may want to log all access to S3 bucket
- Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
- The data can be analyzed using data analysis tools...
- The target logging bucket must be in the same AWS region
- Do not set your logging bucket to be the monitored bucket
- It will create a logging loop, and your bucket will grow exponentially

### Amazon S3 - Pre-Signed URLs

- Generate pre-signed URLs using the S3 Console, AWS CLI or SDK
- URL Expiration
    - S3 Console - 1 min up to 720 minutes (12 hours)
    - AWS CLI - configure expiration with -expires-in parameter in seconds (defaullt 3600 ses, max 604800 secs ~ 168
      hours)
- Users given a pre-signed URL inherit the permissions of the user that generated the URL for GET/PUT
- Examples:
    - Allow only logged-in users to download a premium video from your S3 bucket
    - Allow an ever-changing list of users to download files by generating URLs dynamically
    - Allow temporaily a user to upload a file to a precise location in your S3 bucket

### Glacier Vault Lock & S3 Object Lock

- S3 Glacier Vault Lock
    - Adopt a WORM (Write Once Read Many) model
    - Create a Vault Lock Policy
    - Lock the policy for future edits (can no longer be changed or deleted)
    - Helpful for compliance and data retention
- S3 Object Lock (versioning must be enabled)
- Adopt a WORM model
- Block an object version deletion for a specified amount of time
- Retention mode - Compliance
    - Object versions can't be overwritten or deleted by any user, including the root user
    - Objects retention modes can't be changed, and retention periods can't be shortened
- Retention mode - Governance
    - Most users can't overwrite or delete an object version or alter it's lock settings
    - Some users have special permissions to change the retention or delete the object
- Retention Period: protect the object for a fixed period, it can be extended
- Legal Hold:
    - Protect the object indefinitely, independent from retention period
    - Can be freely placed and removed using the s3:PutObjectLegalHold IAM permission

### S3 Access Points

- Access Points simplify security management for S3 Buckets
- Each Access Point has:
    - Its own DNS name (Internet Origin or VPC Origin)
    - An access point policy (similar to bucket policy) - manage security at scale
- S3 - Access Point - VPC Origin
    - We can define the access point to be accessible only from within the VPC
    - You must create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint)
    - The VPC Endpoint Policy must allow access to the target bucket and Access Point

### S3 Object Lambda

- Use AWS Lambda Functions to change the object before it is retrieved by the caller application
- Only one S3 bucket is needed, on top of which we create S3 Access Point and S3 Object Lambda Access Points
- Use Cases:
    - Redacting personally identifiable information for analytics or non-production environment
    - Converting across data formats, such as converting XML to JSON
    - Resizing and watermarking images on the fly using caller-specific details, such as the user who requested the
      object

## CloudFront & AWS Global Accelerator

### AWS CloudFront

- CDN (Content Delivery Network)
- Improve read performance, content is cached at the edge
- Improve users experience
- 216 Point of Presence globally (edge locations)
- DDoS protection, integration with Shield, WAF
- CloudFront - Origins
    - S3 bucket
        - For distributing files and caching them at the edge
        - Enhanced seucrity with CloudFront Origin Access Control (OAC)
        - OAC is replacing Origin Access Identity (OAI)
        - CloudFront can be used as an ingress (to upload files to S3)
    - Customer Origin (HTTP)
        - Application Load Balancer
        - EC2 instance
        - S3 website (must first enable the bucket as a static S3 website)
        - Any HTTP backend you want
- CloudFront vs S3 Cross Region Replication
    - CloudFront:
        - Global Edge network
        - Files are cached for a TTL
        - Great for static content that must be available everywhere
    - S3 Cross Region Replication
        - Must be setup for each region you want replication to happen
        - Files are updated in near real-time
        - Read Only
        - Great for dynamic content that needs to be available at low-latency in few regions
- ALB or EC2 as an origin
    - EC2 instances or ALB must be public
- CloudFront Get Restriction
    - You can restrict who can access your distribution
        - Allowlist: Allow your users to access your content only if they're in one of the countries on a list of
          approved countries
        - Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of
          banned countries
    - The country is determined using a 3rd party Geo-IP database
    - Use case: Copyright Laws to control access to content

### CloudFront - Price Classes

- CloudFront Edge locations are all around the world
- The cost of data out per edge location varies
- You can reduce the number of edge locations for cost reduction
- Three price classes:
    - Price Class: All regions - best performance
    - Price Class 200: most regions, but excludes the most expensive regions
    - Price Class 100: only the least expensive regions

### CloudFront - Cache Invalidations

- In case you update the back-end origin, CloudFront doesn't know about it and will only get the refreshed content after
  the TTL has expired
- However you can force an entire or partial cache refresh (thus bypassing the TTL) by performing a CloudFront
  Invalidation
- You can invalidate all files (*) or a special path (/images/*)

### AWS Global Accelerator

- Global users for our application
    - You have deployed an application and have global users who want to access it directly
    - They go over the public internet, which can add a lot of latency due to many hops
    - We wish to go as fast as possible through AWS network to minimize latency
- Unicast IP: one server holds one IP address
- Anycast IP: all servers hold the same IP address and the client is routed to the nearest one
- AWS Global Accelerator
    - Leverage the AWS internal network to route to your application
    - 2 Anycast IP are created for your application
    - The Anycast IP send traffic directly to Edge Locations
    - The Edge locations sent the traffic to your application
    - Works with Elastic IP, EC2 instances, ALB, NLB, public or private
    - Consistent Performance
        - Intelligent routing to lowest latency and fast regional failover
        - No issue with client cache (because the IP doesn't change)
        - Internal AWS network
    - Health Checks
        - Global Accelerator performs a health check of your applications
        - Help make your application global (failover less than 1 minute for unhealthy)
        - Great for disaster recovery
    - Security
        - Only 2 external IP need to be whitelisted
        - DDos protection thanks to AWS Shield
- AWS Global Accelerator vs CloudFront
    - They both use the AWS global network and its edge locations around the world
    - Both services integrate with AWS Shield for DDoS protection
    - CloudFront
        - Improves performance for both cachable content (such as images and videos)
        - Dynamic content (such as API acceleration and dynamic site delivery)
        - Content is served at the edge
    - Global Accelerator
        - Improve performance for a wide range of applications over TCP or UDP
        - Proxying packets at the edge to applications running in one or more AWS Regions
        - Good fir for non-HTTP use cases, such as gaming(UDP), IoT(MQTT) or Voice over IP
        - Good for HTTP use cases that required static IP addresses
        - Good for HTTP use cases that required deterministic, fast regional failover

## AWS Storage Extras

### AWS Snow family

- High-secure, portable devices to collect and process data at the edge, and migrate data into and out of AWS
- Data migration:
    - Snowcone
    - Snowball Edge
    - Snowmobile
- Edge computing:
    - Snowcone
    - Snowball Edge
- Data Migrations with AWS Snow Family
    - Challenges:
        - Limited connectivity
        - Limited bandwidth
        - High network cost
        - Shared bandwidth (can't maximize the line)
        - Connection stability
    - AWS Snow Family: offline devices to perform data migrations
    - If it takes more than a week to transfer over the network, use Snowball devices!

### Snowball Edge (for data transfers)

- Physical data transport solution: move TBs or PBs of data in or out of AWS
- Alternative to moving data over the network (and paying network fees)
- Pay per data transfer job
- Provide block storage and Amazon S3-compatible object
- Snowball Edge Storage Optimized
    - 80TB of HDD capacity for block volume and S3 compatible object storage
- Snowball Edge Compute Optimized
    - 42TB of HDD or 28TB NVMe capacity for block volume and S3 compatible object storage
- Use cases: large data cloud migrations, DC decommission, disaster recovery

### AWS Snowcone & Snowcone SSD

- Small, portable computing, anywhere, rugged&secure, withstands harsh environment
- Light (4.5 pounds, 2.1 kg)
- Device used for edge computing, storage, and data transfer
- Snowcone - 8TB of HDD storage
- Snowcone SSD - 14TB of SSD storage
- Use Snowcone where Snowball does not fit (space-constrained environment)
- Must provide your own battery/cables
- Can be sent back to AWS offline, or connect it to internet and use AWS DataSync to send data

### AWS Snowmobile

- Transfer exabytes of data
- Each Snowmobile has 100PB of capacity (use multiple in parallel)
- High security: temperature controlled, GPS, 24/7 video surveillance
- Better than Snowball if you transfer more than 10PB

### Snow Family - Edge Computing

- What is Edge Computing?
    - Process data while it's being created on an edge location
        - A truck on the road, a ship on the sea, a mining station underground
    - These locations may have
        - Limited/no internet access
        - Limited/no easy access to computing power
    - We setup a Snowball Edge/Snowcone device to do edge computing
    - Used cases for Edge Computing:
        - Preprocess data
        - Machine learning at the edge
        - Transcoding media streams
    - Eventually (if need be) we can ship back the device to AWS (for transferring data for example)
- Snowcone & Snowcone SSD (smaller)
    - 2 CPUs, 4GB of memory, wired or wireless access
    - USB-C power using a cord or the optional battery
- Snowball Edge - Compute Optimized
    - 104 vCPUs, 416 GiB of RAM
    - Optional GPU (useful for video processing or machine learning)
    - 28TB NVMe or 42TB HDD usable storage
    - Storage Cluster available (up to 16 nodes)
- Snowball Edge - Storage Optimized
    - Up to 40vCPUs, 80Gib of RAM, 80TB storage
- All: Can run EC2 Instances & AWS Lambda functions (using AWS IoT Greengrass)
- Long-term deployment options: 1 and 3 years discounted pricing

### AWS OpsHub

- Historically, to use Snow Family devices, you needed a CLI (Command Line Interface tool)
- Today you can use AWS OpsHub (a software you install on your computer/laptop) to manage your Snow Family Device
    - Unlocking and configuring single or clustered devices
    - Transferring files
    - Launching and managing instances running on Snow Family Devices
    - Monitor device metrics (storage capacity, active instances on your device)
    - Launch compatible AWS services on your devices (ex: Amazon EC2 instances, AWS DataSync, Network File System(NFS))

### Snowball into Glacier

- Snowball cannot import to Glacier directly
- You must use Amazon S3 first, in combination with an S3 lifecycle policy

### Amazon FSx

- Overview
    - Launch 3rd party high-performance file systems on AWS
    - Fully managed service
- Amazon FSx for Windows (File Server)
    - FSx for Windows is a fully managed Windows file system share drive
    - Support SMB protocol & Windows NTFS
    - Microsoft Active Directory integration, ACLs, user quotas
    - Can be mounted on Linux EC2 instances
    - Support Microsoft's Distributed File System (DFS) Namespaces (group files across multiple FS)
    - Scale up to 10s of GB/s, millions of IOPS, 100s PB of data
    - Storage Options:
        - SSD - latency sensitive workloads (database, media processing, data analytics...)
        - HDD - broad spectrum of workloads (home directory, CMS...)
    - Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
    - Can be configured to be Multi-AZ (high availability)
    - Data is backed-up daily to S3
- Amazon FSx for Lustre
    - Lustre is a type of parallel distributed file system, for large-scale computing
    - The name Lustre is derived from "Linux" and "cluster"
    - Machine Learning, High Performance Computing (HPC)
    - Video Processing, Financial Modeling, Electronic Design Automation
    - Scale up to 100s GB/s, millions of IOPS, sub-ms latencies
    - Storage Options:
        - SSD - low-latency, IOPS intensive workloads, small & random file operations
        - HDD - throughput-intensive workloads, large & sequential file operations
    - Seamless integration with S3
        - Can "read S3" as a file system (through FSx)
        - Can write the output of the computations back to S3 (through FSx)
- FSx File System Deployment Options
    - Scratch File System
        - Temporary storage
        - Data is not replicated (doesn't persist if file server fails)
        - High burst (6x faster, 200MBps per TiB)
        - Usage: short-term processing, optimize costs
    - Persistent File System
        - Long-term storage
        - Data is replicated within same AZ
        - Replace failed files within minutes
        - Usage: long-term processing, sensitive data
- Amazon FSx for NetApp ONTAP
    - Managed NetApp ONTAP on AWS
    - File System compatible with NFS, SMB, iSCSI protocol
    - Move workloads running on ONTAP or NAS to AWS
    - Works with:
        - Linux
        - Windows
        - MacOS
        - VMware Cloud on AWS
        - Amazon Workspaces & AppStream 2.0
        - Amazon EC2, ECS and EKS
    - Storage shrinks or grows automatically
    - Snapshots, replication, low-cost, compression, and data de-duplication
    - Point-in-time instantaneous cloning (helpful for testing new workloads)
- Amazon FSx for OpenZFS
    - Managed OpenZFS file system on AWS
    - Files System compatible with NFS (v3,v4,v4.1,v4.2)
    - Move workloads running on ZFS to AWS
    - Works with:
        - Linux
        - Windows
        - MacOS
        - VMware Cloud on AWS
        - Amazon Workspaces & AppStream 2.0
        - Amazon EC2, ECS and EKS
    - Up to 1000000 IOPS with < 0.5ms latency
    - Snapshots, compression and low-cost
    - Point-in-time instantaneous cloning (helpful for testing new workloads)

### AWS Storage Gateway

- Hybrid Cloud For Storage
    - AWS is pushing for "hybrid cloud"
        - Part of your infrastructure is on the cloud
        - Part of your infrastructure is on-premises
    - This can be due to
        - Long cloud migrations
        - Security requirements
        - Compliance requirements
        - IT strategy
    - S3 is a proprietary storage technology (unlike EFS/NFS)
- AWS Storage Gateway
    - Bridge between on-premise data and cloud data
    - Use cases:
        - disaster recovery
        - backup & restore
        - tiered storage
        - on-premises cache & low-latency file access
    - Types of Storage Gateway
        - S3 File Gateway
        - FSx File Gateway
        - Volume Gateway
        - Tape Gateway

### Amazon S3 File Gateway

- Configured S3 buckets are accessible using the NFS and SMB protocol
- Most recently used data is cached in the file gateway
- Supports S3 Standard, S3 Standard IA, S3 One Zone IA, S3 Intelligent Tiering
- Transition to S3 Glacier using a Lifecycle Policy
- Bucket access using IAM roles for each File Gateway
- SMB Protocol has integration with Active Directory (AD) for user authentication

### Amazon FSx File Gateway

- Native access to Amazon FSx for Windows File Server
- Local cache for frequently accessed data
- Windows native compatibility (SMB, NTFS, Active Directory)
- Useful for group file shares and home directories

### Volume Gateway

- Block storage using iSCSI protocol backed by S3
- Backed by EBS snapshots which can help restore on-premises volumes
- Cached volumes: low latency access to most recent data
- Stored volumes: entire dataset is on premise, scheduled backups tp S3

### Tape Gateway

- Some companies have backup processes using physical tapes
- With Tape Gateway, companies use the same processes but, in the cloud
- Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
- Back up data using existing tape-base process (and iSCSI interface)
- Works with leading backup software vendors

### Storage Gateway - Hardware appliance

- Using Storage Gateway means you need on-premises virtualization
- Otherwise, you can use a Storage Gateway Hardware Appliance
- Can buy it on amazon.com
- Works with File Gateway, Volume Gateway, Tape Gateway
- Has the required CPU, memory, network. SSD cache resources
- Helpful for daily NFS backups in small data centers

### AWS Transfer Family

- A fully-managed service for file transfers into and out of Amazon S3 or Amazon EFS using the FTP protocol
- Supported Protocols:
    - AWS Transfer for FTP (File Transfer Protocol)
    - AWS Transfer for FTPS (File Transfer Protocol over SSL)
    - AWS Transfer for SFTP (Secure File Transfer Protocol)
- Managed infrastructure, Scalable, Reliable, Highly Available (Multi-AZ)
- Pay per provisioned endpoint per hour + data transfer in GB
- Store and manage user's credentials within the service
- Integrate with existing authentication systems (Microsoft AD, LDAP, Okta, Amazon Cognito)
- Usage: sharing files, public datasets, CRM, ERP

### AWS DataSync

- Move large amount of data to and from
    - On-premises / other cloud to AWS(NFS, SMB, HDFS, S3 API) - needs agent
    - AWS to AWS (different storage service) - no agent needed
- Can synchronize to:
    - Amazon S3 (any storage classes - including Glacier)
    - Amazon EFS
    - Amazon FSx (Windows, Lustre, NetApp, OpenZFS)
- Replication tasks can be scheduled hourly, daily, weekly
- File permissions and metadata are preserved (NFS POSIX, SMB...)
- One agent tasks can use 10 Gbps, can setup a bandwidth limit

### Storage Comparison

- S3: Object Storage
- S3 Glacier: Object Archival
- EBS volumes: Network storage for one EC2 instance at a time
- Instance Storage: Physical storage for your EC2 instance (high IOPS)
- EFS: Network File System for Linux instances, POSIX filesystem
- FSx for Windows: Network File System for Windows servers
- FSx for Luster: High Performance Computing Linux file system
- FSx for NetApp ONTAP: High OS Compatibility
- FSx for OpenZFS: Managed ZFS file system
- Storage Gateway: S3 & FSx File Gateway, Volume Gateway(cache&stored), Tape Gateway
- Transfer Family: FTP, FTPS, SFTP interface on top of Amazon S3 or Amazon EFS
- DataSync: Schedule data sync from on-premises to AWS, or AWS to AWS
- Snowcone/Snowball/Snowmobile: to move large amount of data to the cloud, physically
- Database: for specific workloads, usually with indexing and querying

## AWS Integration & Messaging

### Section Introduction

- When we start deploying multiple applications, they will inevitably need to communicate with one other
- There are two patterns of application communication
    - Synchronous communications (application to application)
    - Asynchronous / Event based (application to queue to application)
- Synchronous between applications can be problematic if there are sudden spikes of traffic
- In that case, it's better to decouple your applications
    - Using SQS: queue model
    - Using SNS: pub/sub model
    - Using Kinesis: real-time streaming model
- These services can scale independently of our application

### Amazon SQS

- What's a queue?
    - Producer -> Send messages -> SQS Queue -> Poll messages -> Consumer
- Standard Queue
    - Oldest offering (over 10 years old)
    - Fully managed service, used to decouple applications
    - Attributes:
        - Unlimited throughput, unlimited number of messages in queue
        - Default retention of messages: 4 days, maximum of 14 days
        - Low latency (<10 ms on publish and receive)
        - Limitation of 256KB per message sent
    - Can have duplicate messages (at least once delivery, occasionally)
    - Can have out of order messages (best effort ordering)
- SQS - Producing Messages
    - Produced to SQS using the SDK (SendMessage API)
    - The message is persisted in SQS until a consumer deletes it
    - Message retention: default 4 days, up to 14 days
    - SQS standard: unlimited throughput
- SQS - Consuming Messages
    - Consumers (running on EC2 instances, servers, or AWS Lambda)
    - Poll SQS for messages (receive up to 10 messages at a time)
    - Process the messages (example: insert the message into an RDS database)
    - Delete the messages using the DeleteMessage API
- SQS - Multiple EC2 Instances Consumers
    - Consumers receive and process messages in parallel
    - At least once delivery
    - Best-effort message ordering
    - Consumers delete messages after processing them
    - We can scale consumers horizontally to improve throughput of processing
- SQS with Auto Scaling Group
    - CloudWatch Metric - Queue Length
        - ApproximateNumberOfMessages
    - CloudWatch Alarm
    - ASG
- SQS to decouple between application tiers
- Amazon SQS - Security
    - Encryption:
        - In-flight encryption using HTTPS API
        - At-rest encryption using KMS keys
        - Client-side encryption if the client wants to perform encryption/decryption itself
    - Access Controls: IAM policies to regulate access to the SQS API
    - SQS Access Policies (similar to S3 bucket policies)
        - Useful for cross-account access to SQS queues
        - Useful for allowing other services (SNS, S3...) to write to an SQS queue

### SQS - Message Visibility Timeout

- After a message is polled by a consumer, it becomes invisible to other consumers
- By default, the "message visibility timeout" is 30 seconds
- That means the message has 30 seconds to be processed
- After the message visibility timeout is over, the message is "visible" in SQS
- If a message is not processed within the visibility timeout, it will be processed twice
- A consumer could call the ChangeMessageVisibility API to get more time
- If visibility timeout is high(hours), and consumer crashed, re-processing will take time
- If visibility timeout is too low(seconds), we mat get duplicates

### Amazon SQS - Long Polling

- When a consumer requests messages form the queue, it can optionally "wait" for messages to arrive if there are none
  in the queue
- This is called Long Polling
- LongPolling decreases the number or API calls made to SQS while increasing the efficiency and latency of your
  application
- The wait time can be between 1 sec to 20 sec (20 sec preferable)
- Long Polling is preferable to Short Polling
- Long polling can be enabled at the queue level or at the API level using WaitTimeSeconds

### SQS - FIFO Queue

- FIFO = First In First Out (ordering of messages in the queue)
- Limited throughput: 300 msg/s without batching, 3000 msg/s with
- Exactly-once send capability (by removing duplicates)
- Messages are processed in order by consumer

### SQS + Auto Scaling Group

- SQS with Auto Scaling Group (ASG)
- SQS as a buffer to database writes
- SQS to decouple between application tiers

### Amazon SNS

- The event producer only sends message to one SNS topic
- As many event receivers (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Up to 12500000 subscriptions per topic
- 100000 topics limit
- Many AWS services can send data directly to SNS for notifications
- How to publish
    - Topic Publish (using the SDK)
        - Create a topic
        - Create a subscription (or many)
        - Publish to the topic
    - Direct Publish (for mobile apps SDK)
        - Create a platform application
        - Create a platform endpoint
        - Publish to the platform endpoint
        - Works with Google GCM, Apple APNS, Amazon ADM...
- Security
    - Encryption:
        - In-flight encryption using HTTPS API
        - At-rest encryption using KMS keys
        - Client-side encryption if the client wants to perform encryption/decryption itself
    - Access Controls: IAM policies to regulate access to the SNS API
    - SNS Access Policies
        - Useful for cross-account access to SNS topics
        - Useful for allowing other services to write to an SNS topic

### SNS and SQS - Fan Out Pattern

- SNS + SQS: Fan Out
    - Push once in SNS, receive in all SQS queues that are subscribers
    - Fully decoupled, no data loss
    - SQS allows for data persistence, delayed processing and retries of work
    - Ability to add more SQS subscribers over time
    - Make sure your SQS queue access policy allows for SQS to write
    - Cross-Region Delivery: works with SQS Queues in other regions
- Application: S3 Events to multiple queues
    - For the same combination of: event type(e.g: object create) and prefix(e.g: images/) you can only have one S3
      Event rule
    - If you want to send the same S3 event to many SQS queues, use fan-out
- Application: SNS to Amazon S3 through Kinesis Data Firehose
    - SNS can send to Kinesis, and therefore we can have the following solutions architecture
- Amazon SNS - FIFO Topic
    - FIFO = First In First Out (ordering of messages in the topic)
    - Similar features as SQS FIFO:
        - Ordering by Message Group ID (all messages in the same group are ordered)
        - Deduplication using a Deduplication ID or Content Based Deduplication
    - Can only have SQS FIFO queues as subscribers
    - Limited throughput (same throughput as SQS FIFO)
- SNS - Message Filtering
    - JSON policy used to filter messages sent to SNS topic's subscriptions
    - If a subscription doesn't have a filter policy, it receives every message

### Amazon Kinesis - Overview

- Make it easy to collect, process and analyze streaming data in real-time
- Ingest real-time data such as: Application logs, Metrics, Website click-streams, IoT telemetry data...
- Kinesis Data Streams: capture, process, and store data streams
- Kinesis Data Firehose: load data streams into AWS data stores
- Kinesis Data Analytics: analyze data streams with SQL or Apache Flink
- Kinesis Video Streams: capture, process and store video streams

### Kinesis Data Streams

- Applications/Client/SDk,KPL/Kinesis agent as Producers
    - Can produce Record
        - Partition Key
        - Data Blob (up to 1MB)
        - 1MB/sec or 1000 msg/sec per shard
- Can scale # of shards
- Apps(KCL, SDK)/Lambda/Kinesis Data Firehose/Kinesis Data Analytics
    - Can consume Record
        - Partition Key
        - Sequence no.
        - Data Blob
        - 2MB/sec (shard) Per shard all consumers
        - Or 2 MB/sec (enhanced) Per shard per consumer
- Retention between 1 day to 365 days
- Ability to reprocess (replay) data
- Once data is inserted in Kinesis, it can't be deleted (immutability)
- Data that shares the same partition goes to the same shard (ordering)
- Producers: AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
- Consumers:
    - Write your own: Kinesis Client Library (KCL), AWS SDK
    - Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics
- Capacity Modes
    - Provisioned mode:
        - You choose the number of shards provisioned, scale manually or using API
        - Each shard gets 1MB/s in (or 1000 records per second)
        - Each shard gets 2MB/s out (classic or enhanced fan-out consumer)
        - You pay per shard provisioned per hour
    - On-demand mode:
        - No need to provision or manage the capacity
        - Default capacity provisioned (4MB/s in or 4000 records per second)
        - Scales are automatically based on observed throughput peak during the last 30 days
        - Pay per stream per hour & data in/out per GB
- Kinesis Data Streams Security
    - Control access / authorization using IAM policies
    - Encryption in flight using HTTPS endpoints
    - Encryption at rest using KMS
    - You can implement encryption/decryption of data on client side (harder)
    - VPC Endpoints available for Kinesis to access within VPC
    - Monitor API calls using CloudTrail

### Kinesis Data Firehose

- Fully Managed Service, no administration, automatic scaling, servers, can batch writes:
    - AWS: Redshift/Amazon S3/OpenSearch
        - Redshift is copied from S3
    - 3rd party partner: Splunk/MongoDB/DataDog/NewRelic/...
    - Custom: send to any HTTP endpoint
- Pay for data going through Firehose
- Near Real Time
    - 60 seconds latency minimum for non full batches
    - or minimum 1MB of data at a time
- Supports many data formats, conversions, transformations, compression
- Supports custom data transformations using AWS Lambda
- Can send failed or all data to a backup S3 bucket

### kinesis Data Streams vs Firehose

- Kinesis Data Streams
    - Streaming service for ingesting at scale
    - Write custom code (producer/consumer)
    - Real-time (~200ms)
    - Managing scaling (shard splitting/merging)
    - Data storage for 1 to 365 days
    - Supports replay capability
- Kinesis Data Firehose
    - Load streaming data into S3/Redshift/OpenSearch/3rd party/custom HTTP
    - Fully managed
    - Near real-time (buffer time min .60 sec)
    - Automatic scaling
    - No data storage
    - Doesn't support replay capability

### Data Ordering for Kinesis vs SQS FIFO

- Ordering data into Kinesis
    - 100 trucks on the road sending their GPS positions regularly into AWS
    - Want to consume the data in order for each truck
    - Using "Partition Key" value of the "truck_id"
    - The same key will always go to the same shard
- Ordering data into SQS
    - For SQS standard, there is no ordering
    - For SQS FIFO, if you don't use a Group ID, messages are consumed in the order they are sent, with only one
      consumer
    - Want to scale the number of consumers, but want messages to be "grouped" when they are related to each other
    - Then you use a Group ID (similar to Partition Key in Kinesis)
- Kinesis vs SQS ordering
    - Assume 100 trucks, 5 kinesis shards, 1 SQS FIFO
        - Kinesis
            - On average you'll have 20 trucks per shard
            - Trucks will have their data ordered within each shard
            - The maximum amount of consumers in parallel we can have is 5
            - Can receive up to 5 MB/s of data
        - SQS FIFO
            - You only have one SQS FIFO queue
            - You will have 100 Group ID
            - You can have up to 100 consumers (due to the 100 Group ID)
            - You have up to 300 messages per second (or 3000 if using batching)

### SQS vs SNS vs Kinesis

- SQS
    - Consumer pull data
    - Data is deleted after being consumed
    - Can have as many workers (consumers) as we want
    - No need to provision throughput
    - Ordering guarantees only on FIFO queues
    - Individual message delay capability
- SNS
    - Push data to many subscribers
    - up to 12500000 subscribers
    - Data is not persisted (lost if not delivered)
    - Pub/Sub
    - Up to 100000 topics
    - No need to provision throughput
    - Integrates with SQS for fan-out architecture pattern
    - FIFO capability for SQS FIFO
- Kinesis
    - Standard: pull data
        - 2MB per shard
    - Enhanced-fan out: push data
        - 2MB per shard per consumer
    - Possibility to replay data
    - Meant for real-time big data, analytics and ETL
    - Ordering at the shard level
    - Data expires after X days
    - Provisioned mode or on-demand capacity mode

### Amazon MQ
- SQS, SNS are cloud-native services: proprietary protocols from AWS
- Traditional applications running from on-premises may use open protocol such as: MQTT, AMQP, STOMP, Openwire, WSS
- When migrating to the cloud, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- Amazon MQ is a managed message broker service for RabitMQ and ActiveMQ
- Amazon MQ doesn't scale as much as SQS/SNS
- Amazon MQ runs on servers, can run in Multi-AZ with failover
- Amazon MQ has both queue feature and topic feature
- High availability
    - Deploy in multi-AZ
  - Has back-up storage mounted in EFS

### AWS RDS

- Relational Database Service
    - Postgres
    - MySQL
    - MariaDB
    - Oracle
    - MicrosoftSQL Server
    - Aurora (AWS Proprietary database)
        - Cost more, more efficient
        - cloud optimized

#### RDS Deployments

- Read Replicas
    - Scale the read workload of your DB
    - Can create up to 15 Read Replicas
    - Data is only written to the main DB
- Multi-AZ
    - Failover in case of AZ outage
    - Data is only written to the main DB
    - Can only have 1 other AZ as failover
- Multi-Region (Read Replicas)
    - Disaster Recovery
    - Local performance
    - Replication cost

### AWS ElasticCache

- Manged Redis or Memcached
- In-memory database

### DynamoDB

- Fully Managed Highly available with replication across 3 AZ
- NoSQL database
- Flexible schema
- Serverless
- low latency

#### DynamoDB Accelerator - DAX

- In-memory cache

#### DynamoDB Global Tables

- low latency in multiple-regions
- Active-Active replication (read/write to any AWS region)

### Redshift

- Based on PostgresSQL, not used for OLTP
- It's OLAP - online analytical processing (analytics and data warehousing)
- Columnar storage of data
- Single AZ deployed

### Amazon EMR

- Elastic MapReduce
- Hadoop Clusters (Big Data)

### Amazon Athena

- Serverless query service to perform analytics against S3 objects
- Standard SQL to query files

### Amazon QuickSight

- Serverless machine learning-powered business intelligence service to create interactive dashboards
- Fast, automatically scalable, embeddable, with per-session pricing

### DocumentDB

- Same as MongoDB
- store,query and index JSON data

### Neptune

- Fully managed **graph** DB

### Amazon QLDB

- Quantum Ledger Database
- A ledger is a book Recording financial transactions
- Immutable system
- Used to review history of all changes to your application data

### Amazon Managed BlockChain

- Without the need for a trusted, central authority
- Decentralise

### AWS Glue

- Managed ETL (Extract, Transform and Load) service
- Serverless
- Prepare and transform data for analytics
- Glue Data catalog: catalog of datasets, can be used by Athena,Redshift,EMR

### DMS - Database Migration Service

- Quickly and securely migrate database to AWS, resilient, self healing
- The source database remains available during the migration
- Supports
    - Homogeneous
    - Heterogeneous

## Other Compute

### ECS - Elastic Container Service

- You must provision & maintain the infrastructure (the EC2 instances)

### Fargate

- No need to provision
- Serverless

### ECR

- Elastic Container Registry
- Store Docker images

### AWS Lambda

- Easy pricing
    - Pay per request and compute time
- Event-Driven
- Easy monitoring through AWS cloudwatch

### Amazon API Gateway

- Fully managed service to create, publish, maintain, monitor, and secure APIs
- Serverless and scalable
- Restful and Websockets

### AWS Batch

- Managed batch processing at any scale
- Dynamically launch EC2 instances or Spot Instances
- Batch jobs are defined as Docker images and run on ECS

### Amazon LightSail

- High availability but no auto-scaling, limited AWS integrations

## Deployments and Managing Infrastructure at Scale

### CloudFormation

- Infrastructure as code
- JSON?YAML

### AWS Cloud Development Kit (CDK)

- Program to define cloud infrastructure

### AWS Elastic Beanstalk

- Paas
- Full control over the configuration
- Free, but you pay for the underlying services
- Three architecture models
    - Single instance deployment
    - LB + ASG
    - ASG only
- Health agent pushed metrics to CloudWatch
- Checks for app health, published health events

### AWS CodeDeploy

- deploy application automatically
- Work with EC2 instances
- Work with on-premise servers

### AWS CodeCommit

- Git-based repositories

### AWS CodeBuild

- Code building service in the cloud
- Compile source code, run tests, and produces packages that are ready to be deployed
- Only pay for the build time

### AWS CodePipeline

- Orchestrate different steps
- Fast delivery and rapid update

### AWS CodeArtifact

- Software packages
- Retrieve dependencies straight from CodeArtifact

### AWS CodeStar

- Quick way to get started for development tools
- Can edit the code using AWS Cloud9

### AWS Cloud9

- Cloud IDE
- Web browser

### AWS System Manager (SSM)

- Helps you manage your EC2 and On-premises systems at scale
- Another Hybrid service
- Get operational insights about the state of your infrastructure
- Most important features are
    - Patching automation for enhanced compliance
    - Run commands across an entire fleet of servers
    - Store parameter configuration with the SSM Parameter Store
- SSM Session Manager
    - Start a secure shell on your EC2 and on-premises servers
    - No SSH access, bastion hosts or SSH keys needed
    - No port 22 needed
    - Send session log data to S3 or CloudWatch Logs

### AWS OpsWorks

- Chef & Puppet help you perform server configuration automatically or repetitive actions
- Managed Chef & Puppet
- Alternative to AWS SSM

## Global Infrastructure

### Global Application

- Deployed in multiple geographies
- On AWS this cloud be Regions/Edge Locations
- Decreased Latency
- Disaster Recovery
- Attack protection

### Route 53

- Managed DNS (Domain Name System)
- Routing polices
    - Simple routing policy
    - Weighted routing policy (Health check)
    - Latency routing policy
    - Failover routing policy (Disaster recovery)

### S3 Transfer Acceleration

- Increase transfer speed by transferring file to AWS edge location which will forward the data to the S3 bucket in the
  target region

### AWS Global Accelerator

- Improve global application **availability** and **performance** using the AWS global network
- 2 Anycast static IP are created for your application

### AWS Outposts

- AWS will set up and manage "Outposts Racks" within your on-premises infrastructure and you can start leveraging AWS
  services on-premises

### AWS WaveLength

- Wavelength zones are infrastructure deployments embedded within the telecommunications providers' datacenters at the
  edge of the 5G networks
- Brings AWS services to the edge of the 5G networks

### AWS Local ZOnes

- Places AWS compute, storage, database, and othrer selected AWS services closer to end users to tun latency-sensitive
  applications
- Extend your VPC to more locations

### Global Applications Architecture

- Single Region, Single AZ
- Single Region, Multi AZ
- Multi Region, Active-Passive
- Multi Region, Active-Active

## Cloud Integration

### SQS

- Simple Queue Service
- Default retention of messages: 4 days, maximum of 14 days

### Amazon Kinesis

- real-time big data streaming
- Kinesis Data Streams
- Kinesis Data Analytics
- Kinesis Data Firehose
- Kinesis Video Streams

### SNS

- Simple Notification Service

### Amazon MQ

- MQTT,AMQP
- Managed message broker service for RabbitMQ and ActiveMQ

## Cloud Monitoring

### CloudWatch Metrics

- CloudWatch provides metrics for every service in AWS
- Metric is a variable to monitor
- Metrics have timestamps
- Create CloudWatch dashboard for metrics
- Important Metrics
    - EC2 instances: CPU Utilization, Status check, Network
        - Default metrics every 5 minutes
        - Option for Detailed Monitoring(charged): metrics every 1 minute
    - EBS volumes: Disk Read/Writes
    - S3 buckets: BucketSizeBytes, NumberOfObjects, AllRequests
    - Billing: Total Estimated Charge
    - Service Limits: how much you've been using a service API

### CloudWatch Alarm

- Alarms are used to trigger notifications for any metric
- Alarm actions:
    - Auto scaling
    - EC2 Actions
    - SNS notification
- Various options
- Can choose the period on which to evaluate an alarm
- Alarm status: OK, INSUFFICIENT_DATA, ALARM
- Billing Alarm can only be set in **US East-1**

### CloudWatch Logs

- Can collect logs from
    - Elastic Beanstalk
    - ECS
    - AWS lambda
    - CloudTrail based on filter
    - CloudWatch log agents: on EC2 machines or on-premises servers
    - Route53: Log DNS queries
- Enables real-time monitoring of logs
- Adjustable CloudWatch Logs retention

### Amazon EventBridge (formerly CloudWatch Events)

- Schedule: Cron jobs
- Event Pattern
- Schema Registry
- Archive events
- Ability to replay archived events

### AWS CloudTrail

- Provide governance, compliance, and audit for your AWS account
- Enabled by default
- Get history of events/API calls made within your AWS account by:
    - Console
    - SDK
    - CLI
    - AWS Services
- Can put logs to CloudWatch Logs or S3
- A trail can be applied to All Regions (default) or a single region
- CloudTrail Insights: automated analysis of your CloudTrail Events

### AWS X-Ray

- TroubleShooting performance
- Understand dependencies in a microservice architecture
- Pinpoint service issues
- Review request behavior
- Find errors and exceptions

### CodeGuru

- ML-powered service for automated code reviews and application performance recommendations
- Provide two functionalities
    - CodeGuru Reviewer
    - CodeGuru Profiler

### AWS Health Dashboard

- Service History
    - all regions, all services health
    - RSS feed can subscribe
    - Previously called AWS service health dashboard
- Your Account
    - Previously called AWS Personal Health Dashboard
    - Provides alerts and remediation guidance when AWS is experiencing events that may impact you
    - Can aggregate data from an entire AWS Organization

## VPC & Network

### IP Address in AWS

- IPv4 - Internet Protocol version 4
    - Public IPv4
    - EC2 instance get a new public IP address every time you stop then restart it
    - Private IPv4
    - Fixed for EC2 instances even start/stop
- Elastic IP - allows you to attach a fixed public IPv4 address to EC2 instance
    - Ongoing cost if not attached to EC2 instance of if the EC2 instance is stopped
- IPv6

### VPC & Subnets Primer

- VPC - Virtual Private Cloud
- Subnet
    - Public subnet
    - Private subnet
- Route Tables
- Internet Gateway, public subnet have a route to the internet gateway
- NAT Gateways (AWS-managed) & NAT Instances (self-managed) allow your instances in your Private Subnets to access
  internet

### NACL & Security Groups

- NACL (Network ACL)
    - A firewall which controls traffic from and to subnet
    - Can have ALLOW and DENY rules
    - Are attached at the Subnet level
    - Rules only include IP addresses
    - Stateless
- Security Groups
    - A firewall that controls traffic to and from an ENI/ an EC2 instance
    - Rules include IP addresses and other security groups
    - Stateful

### VPC Flow Logs

- Capture information about IP traffic going into your interfaces:
    - VPC Flow Logs
    - Subnet Flow Logs
    - Elastic Network Interface Flow Logs

### VPC Peering

- Connect two VPC, privately using AWS' network
- Behave as if they were in the same network
- Not transitive

### VPC Endpoints

- Allow you to connect to AWS service suing a private network
- Enhanced security and lower latency
- VPC Endpoint Gateway: S3 & DynamoDB
- VPC Endpoint Interface: the rest

### AWS PrivateLink (VPC Endpoint Services)

- Most secure/scalable way to expose a service to 1000s VPCs
- Requires a network load balancer (Service VPC) and ENI (Customer VPC)

### Site to Site VPN & Direct Connect

- Site to Site VPN
    - Connect an on-premises VPN to AWS
    - The connection is automatically encrypted
    - Goes over public internet
    - Customer Gateway and Virtual Private Gateway
- Direct Connect
    - Establish a physical connection between on-premises and AWS
    - The connection is private, secure, and fast
    - Goes over a private network
    - Take at least a month to establish

### AWS Client VPN

- Connect from your compute using OpenVPN to your private network in AWS and on-premises

### Transit Gateway

- For having transitive peering between thousands of VPC and on-premises, hub and spoke connection
- One single Gateway to provide this functionality
- Works with Direct Connect Gateway, VPN connections

## Security and Compliance

### AWS Shared Responsibility model

- AWS - Security of the Cloud
    - Protecting infrastructure
    - Managed service like S3, DynamoDB
- Customer responsibility - Security in the cloud
    - For EC2, customer is responsible for management of the guest OS, firewall & network configuration, IAM
    - Encrypting data
- Shared control
    - Patch Management, Configuration Management, Awareness & Training

### DDos protection

- Distributed Denial-of-Service
- AWS Shield Standard
- AWS shield Advanced
- AWS WAF: Filter specific requests based on rules
- CloudFront and Route53
    - Availability protection using global edge network
    - Combined with AWS Shield
- Be ready to scale - leverage AWS Auto Scaling

### AWS Shield

- AWS Shield Standard
    - Free service
    - Provides protection from attacks such as SYN/UDP Floods, Reflection attacks and other layer 3/4
- AWS Shield Advanced
    - Optional DDos mitigation service
    - EC2, ELB, CloudFront, Global Accelerator, Route 53

### AWS WAF (Web Application Firewall)

- Layer 7 HTTP
- ALB, API Gateway, CloudFront, AppSync
- Define Web ACL
    - IP address, HTTP headers
    - SQL injection and Cross-Site Scripting (XSS)
    - Size constraints, geo-match (block countries)
    - Rate-based rules - for DDos protection

### Penetration test on AWS

- 8 services without prior approval
    - Amazon CE2, NAT Gateways, ELB
    - RDS
    - CLoudFront
    - Aurora
    - API Gateway
    - Lambda
    - Lightsail
    - Elastic Beanstalk
- Prohibited services
    - DNS zone walking
    - Dos
    - flooding

### AWS KMS (Key Management Service)

- Data at rest / transit
- AWS manages the encryption keys for us
- Encryption opt on:
    - EBS
    - S3
    - Redshift
    - RDS
    - EFS
- Encryption automatically enabled
    - CloudTrail logs
    - S3 Glacier
    - Storage Gateway

### CloudHSM

- AWS provisions encryption hardware
- Hardware Security Module
- Tamper resistant

### CMK (Customer Master Keys)

- Customer Managed CMK
    - Create, manager and used by the customer
    - Rotation policy
    - bring-your-own-key
- AWS managed CMK
    - Create, manager and used on the customer's behalf by AWS
    - S3, EBS, Redshift
- AWS owned CMK
    - AWS own and manages to use in multiple accounts
- CloudHSM keys (customer keystore)
    - Generated from your own CloudHSM hardware device
    - Cryptographic operations are performed within the CloudHSM cluster

### AWS ACM

- SSL/TLC Certificates
- HTTPS
- Free charge for public TLS certificates
- Automatic TLS certificate renewal
- Integration with
    - ELB
    - CLoudFront
    - API on API gateway

### AWS Secrets Manager

- Storing secrets
- Rotation of secrets
- Integration with Amazon RDS
- Secrets are encrypted using KMS
- Mostly meant for RDS integration

### AWS Artifact

- Portal that provides customers with on-demand access to AWS compliance documentation and AWS agreements
- Artifact Reports
- Artifact Agreements
- Can be used to support internal audit or compliance

### AWS GuardDuty

- Intelligent Threat discovery to protect your AWS account
- Use ML, anomaly detection, 3rd party data
- Input data:
    - CloudTrail Events Logs
    - VPC Flow Logs
    - DNS Logs
    - Optional Features
- Can set up EventBridge rules to be notified in case of findings, to SNS/Lambda
- Can protect against CryptoCurrency attacks

### Amazon Inspector

- Automated Security Assessments
- For EC2 instance
    - Leveraging the AWS System Manager agent
- For Container images push to Amazon ECR
- For Lambda functions
- Reporting & Integration with AWS Security HUb, AWS EventBridge

### AWS Config

- Auditing and recording compliance of your AWS resources
- Record configurations and changes over time
- Store in S3

### AWS Macie

- ML and pattern matching to discover and protect sensitive data (such as PII)

### AWS Security Hub

- Central security tool to manage security across several AWS accounts and automate security checks
- Dashboard integrated
- Need to enable AWS config

### AWS Detective

- Analyzes, investigates and quickly identifies the root cause of security issues or suspicious activities (using ML and
  graphs)
- Collect from VPC flow logs, CLoudTrail, and GuardDuty
- Visualizations

### AWS Abuse

- Report suspected AWS resources used for abusive or illegal purposes
- Abusive & prohibited behaviors are:
    - Spam
    - Port Scanning
    - DDos
    - Intrusion attempts
    - Hosting objectionable or copyrighted content
    - Distributing malware

### Root user privileges

- Account owner
- Complete access to all AWS services and resources
- Actions can be performed
    - **Change account settings**
    - View certain tax invoices
    - **Close your AWS account**
    - Restore IAM user permissions
    - **Change or cancel AWS support plan**
    - **Register as a seller in the Reserved Instance Marketplace**
    - Configure and S3 to enable MFA
    - Edit or delete S3 bucket policy that includes an invalid VPC ID or VPC endpoint ID
    - Sing up fpr GovCloud

### IAM Access Analyzer

- Find out which resources are shared externally
- Define Zone of Trust

## Machine Learning

### Amazon Rekognition

- Find objects, people, text, scenes in images and videos using ML
- Use cases:
    - Labeling
    - Content Moderation
    - Text Detection
    - Face Detection and Analysis
    - Face Search and Verification
    - Celebrity Recognition

### AWS Transcribe

- Automatically convert speech to text
- Use ASR (automatic speech recognition)
- Automatically remove PII using Redaction

### Amazon Polly

- Turn text into lifelike speech using deep learning
- Allowing you to create applications that talk

### AWS Translate

- Natural and accurate language translation

### AWS Lex & Connect

- Lex
    - ASR to convert speech to text
    - Natural Language understanding to recognize the intent of text, callers
    - Helps build chatbots, call center bots
- Amazon connect
    - Virtual contact center
    - Integrate with other CRM systems or AWS

### AWS Comprehend

- For Natural Language Procession - NLP
- Fully managed and serverless service
- Use machine learning to find insights and relationships in text

### AWS Sagemaker

- Fully managed service to build ML models

### AWS Forecast

- Use ML to deliver highly accurate forecast

### AWS Kendra

- Fully managed document service powered by ML
- Natural language search capabilities
- Incremental learning
- Manually fine-tune search results

### AWS Personalize

- Fully Managed ML-service to build apps with real-time personalized recommendations

### AWS Textract

- Automatically extracts text, handwriting, and data from any scanned documents using AI and ML
- Extract data from forms and tables

## Account management, billing & support

### AWS Organizations

- Global service
- Allow to manage multiple AWS accounts
- The main account is master account
- Cost benefits:
    - Consolidate billing
    - Pricing benefits from aggregated usage
    - pooling of Reserved EC2 instances for optimal savings
- API is available to automate AWS account creation
- Restrict account privileges using Service Control Polices
    - Whitelist or blacklist IAM actions
    - Applied at the OU or Account level
    - Not apply to Master Account
    - The SCP does not affect service-linked roles
    - SCP must have an explicit Allow (not allow anything by default)
- Using tagging standards for billing purposes

### AWS Organization - Consolidated Billing

- Combine usage - Share volume pricing, reserved instances and saving plan discounts
- One bill
- Reserved instances discount can be turned off

### AWS Control Tower

- Easy way to set up and govern a secure and compliant multi-account AWS environment based on best practices
- Benefits
    - Automate setup environments
    - Automate ongoing policy management using guardrails
    - Detect policy violations and remediate them
    - Monitor compliance through an interactive dashboard
- Runs on top of AWS Organizations

### AWS Service Catalog

- Quick self-service portal to launch a set of authorized products pre-defined by admins

### Pricing Model in AWS

- Pay as you go
- Save when you reserve
- Pay less by using more
- Pay less as AWS grows

#### Compute pricing -EC2

- Number of instances
- Instance configuration
- ELB running and amount of data processed
- Detailed monitoring
- On-demand instances
    - Minimum of 60s
    - Pay per second (Linux/Windows) or per hour (other)
- Reserved instances
    - 75% discount
    - 1 or 3 year commitment
    - All upfront, partial upfront, no upfront
- Spot instances
    - 90% discount
    - Bid for unused capacity
- Saving plans

**todo**

### Savings Plan

- Commit a certain $ amount per hour for 1 or 3 years
- Easiest way to setup long-term commitments on AWS
- EC2 Saving plan
    - 72% discount
    - Commit to usage of individual instance families in a region
- Compute savings plan
    - 66% discount
    - Family, Region, size, OS, tenancy, compute options
    - EC2, Fargate, Lambda
- Machine Learning Savings plan
    - SageMaker

### AWS Compute Optimizer

- Reduce costs and improve performance by recommending optimal AWS resources for your workloads
- Use ML to analyze your resources' configurations and their utilization CloudWatch metrics
- Support
    - EC2
    - ASG
    - EBS
    - Lambda

### AWS Billing and Costing Tools

- Estimate costs in the cloud
    - Pricing Calculator
- Tracking costs in the cloud
    - Billing Dashboard
    - Cost Allocation Tags
    - Cost and Usage reports
    - Cost Explorer
- Monitoring against costs plans
    - Billing Alarms
    - Budgets

#### AWS Pricing Calculator

- Estimate the cost for your solution architecture

#### AWS Billing Dashboard

- AWS Free tier dashboard

#### Cost Allocation Tags

- Track cost on a detailed level
- AWS generated tags:
    - Automatically applied to the resource you create
    - Start with Prefix aws
- User-defined tags
    - Defined by the user
    - Start with Prefix user
- Tag can be used to create Resource Groups

### Cost and Usage Reports

- The most comprehensive set of AWS cost and usage data available

### Cost Explorer

- Visualize, understand and manage your AWS costs and usage over time
- Choose an optimal Savings Plan
- Forecast usage up to 12 months
- Monthly cost

### Billing Alarms in CloudWatch

- Billing data metric is stored in CloudWatch us-east-1
- Actual cost

### AWS Budgets

- Create budget and send alarms when costs exceeds the budgets
- 4 type:
    - Usage
    - Cost
    - Reservation
    - Saving Plans

### AWS Cost Anomaly Detection

- Continuously monitor your cost and usage using ML to detect unusual spends

### AWS Service Quotas

- Notify you when you are close to a service quota value threshold
- Create CloudWatch Alarms on the Service Quota console

### Trusted Advisor

- High level AWS account assessment
- 5 categories
    - Cost optimization
    - Performance
    - Security
    - Fault tolerance
    - Service limits
- Support plans
    - 7 core checks - Basic/Developer plan
        - S3 Bucket permissions
        - Security Group - Specific Ports Unrestricted
        - IAM use
        - EBS public snapshot
        - RDS public snapshot
        - Service limits
        - MFA on Root account
    - Full check - Business/Enterprise
        - Set CloudWatch alarm
        - Programmatic Access using AWS Support API

### AWS Support Plan

- Basic Support
    - Free
    - 24x7 access to customer service, documentation, white papers, and support forums
    - 7 core check in Trusted Advisor
    - AWS Personal Health Dashboard
- Developer Plan
    - Business hours email access to Cloud Support Associates
    - Unlimited cases / one primary contact
    - Case severity / response time
        - General guidance < 24 business hours
        - System impaired < 12 business hours
- Business Plan
    - 24x7 phone,email, and chat access to Cloud Support Engineers
    - Unlimited cases / Unlimited contacts
    - Access to Infrastructure Event Management for additional fee
    - Case severity / response time
        - General guidance < 24 business hours
        - System impaired < 12 business hours
        - **Production system impaired < 4 hours**
        - **Production system down < 1 hour**
- Enterprise On-Ramp
    - Access to pool of Technical Account Managers (TAM)
    - Concierge Support Team (billing and account best practices)
    - Infrastructure Event Management, Well-Architected & Operations Reviews
    - Case severity / response time
        - ...
        - **Production system impaired < 4 hours**
        - **Production system down < 1 hour**
- Enterprise
    - Designated TAM
    - Concierge Support Team
    - Infrastructure Event Management, Well-Architected & Operations Reviews
    - Case severity / response time
        - ...
        - **Production system impaired < 4 hours**
        - **Production system down < 1 hour**
        - **Business-critical system down < 15 minutes**

## Advanced Identity

### AWS STS (Security Token Service)

- Enable you to create temporary, limited-privileges credential to access your AWS resources
- Short-term credentials, you configure expiration period
- Use cases
    - Identity federation
    - IAM Roles for cross/same account access
    - IAM Roles for Amazon EC2

### AWS Cognito

- Identity for your Web and Mobile application users
- Instead of creating them in IAM user

### AWS Directory Services

- AWS Managed Microsoft AD
- AD Connector
- Simple AD

### AWS IAM Identify Center

- One login (single sign-on) for all your

## Other AWS Services

### AWS WorkSpaces

- Managed Desktop as a Service (DaaS)  solution to easily provision Windows or Linux desktops
- Multi Regions

### AWS AppStream 2.0

- Desktop Application Streaming Service
- The application is delivered from within a web browser

### AWS IoT Core

- AWS IoT Core allows you to easily connect IoT devices to the AWS cloud
- Serverless, secure, scalable

### AWS Elastic Transcoder

- Convert media files stored in S3 into media files in the formats required by consumer playback devices

### AWS AppSync

- Store and sync data across mobile and web apps in real-time
- Make use of GraphQL

### AWS Amplify

- A set of tools and services that helps you develop and deploy scalable full stack web and mobile applications

### AWS Device Farm

- Fully-manage service that tests your web and mobile apps against desktop browsers, real mobile devices and tablets

### AWS Backup

- Fully-managed service to centrally manage and automate backups across services

### Disaster Recovery Strategies

- Backup and restore
- Pilot Light
- Warm stand by
- Multi site / Hot-Site

### AWS Elastic Disaster Recovery

- CloudEndure Disaster Recovery
- Quickly and easily recover your physical, virtual and cloud-based servers into AWS
- Continuous block-level replication for your servers

### AWS DataSync

- Move large amount data from on-premises to AWS

### AWS Application Discovery Service

- Plan migration projects by gathering information about on-premises data centers
- Server utilization data and dependency mapping are important for migrations
    - Agentless Discovery
    - Agent-based Discovery
- Resulting data can be viewed within AWS Migration Hub

### AWS Application Migration Service

- Lift and shift (rehost)
- Convert your physical, virtual and cloud-based servers to run natively on AWS

### AWS Fault injection simulator (FIS)

- a fully managed service for running fault injection experiments on AWS workloads
- Chaos Engineering

### AWS Step Functions

- Build Serverless visual workflow to orchestrate your Lambda functions
- Parallel, conditions, sequence, timeouts, error handling

### AWS Ground Station

- Fully managed service that lets you control satellite communications process data, and scale your satellite operations

### AWS Pinpoint

- Scalable 2way (outbound/inbound) **marketing** communications service

## AWS Architecting & Ecosystem

### AWS Cloud Best practices principle

### Well Architected Framework 6 pillars

- Operational Excellence
- Security
- Reliability
- Performance Efficiency
- Cost Optimization
- Sustainability

#### Operational Excellence

- Design principles
    - Perform operations as code
    - Annotate documentation
    - Make frequent, small, reversible changes
    - Refine operations procedures frequently
    - Anticipate failure
    - Learn from all operational failures

### Security

- Design principles
    - Implement a strong identity foundation
    - Enable traceability
    - Apply security at all layers
    - Automate security best practices
    - Protect data in transit and at rest
    - Keep people away from data

### Reliability

- Design principles
    - Test recovery procedures
    - Automatically recover from failure
    - Scale horizontally to increase aggregate system availability
    - Stop guessing capacity
    - Manage change in automation

### Performance Efficiency

- Design principles
    - Democratize advanced technologies
    - Go global in minutes
    - Use serverless architectures
    - Experiment more often
    - Mechanical sympathy

### Cost Optimization

- Design principles
    - Adopt a consumption mode
    - Measure overall efficiency
    - Stop spending money on data center operations
    - Analyze and attribute expenditure
    - Use managed and application leve services to reduce cost of ownership

### Sustainability

- Design principles
    - Understand your impact
    - Establish sustainability goals
    - Maximize utilization
    - Anticipate and adopt new, more efficient hardware and software offerings
    - Use managed services
    - Reduce the downstream impact of your cloud workloads

### AWS Well-Architected Tool

- Review your architectures against the 6 pillars and adopt architectural best practices

### AWS Cloud Adoption Framework (CSF)

- Capabilities
    - Business
    - People
    - Governance
    - Platform
    - Security
    - Operations
- Transformation Domains
    - Technology
    - Process
    - Organization
    - Product

### AWS Right Sizing

- Start small
- Lowest possible cost
    - Before a cloud Migration
    - continuously after the cloud onboarding process
- Cloud Watch, Cost Explorer, Trusted Advisor

### AWS Ecosystem

- AWS Blogs
- AWs Forums (community)
- AWS Whitepapers & Guides
- AWS Partner Solutions (Quick Start)
    - CloudFormation, Template
- AWS Solutions
    - Vetted Technology Solutions for the AWS cloud
- AWS Marketplace
    - Custom AMI
    - CloudFormation templates
    - Saas
    - Containers
- AWS Training
    - Digital
    - Classroom
- AWS Professional Services & Partner Network
    - APN Technology Partners
    - Consulting
    - Training
    - Competency Program
    - Navigate Program

### AWS Knowledge center

- Contains the most frequent & common questions and requests

### AWS IQ

- Quickly find professional help for your AWS projects
- Engage and pay 3rd party experts for on-demand project work

### AWS re:Post

- AWS managed Q&A service, replaces of AWS Forums

### AWS managed service (AMS)

- Provide infrastructure and application support on AWS
- Team of AWS experts who manage and operate your infrastructure for security, reliability and availability

### Cloud Foundations

-Guided path to help customers deploy, configure, and secure their new workloads while ensuring they are ready for
on-going operations in the cloud

### AWS Regions

- Cluster of data centers
- Most AWS services are region-scoped

### AWS Availability Zones

- Each region has min 3, max 6 AZ
- Each AZ has one or more discrete data centers with redundant power, networking and connectivity
- Separate from each other, isolated from disasters
- Connected with high bandwidth, ultra-low latency networking

### AWS Points of Presence (Edge Locations)

- 400+ (400 + edge locations & 10 + Regional Caches)
- Content is delivered to end users with lower latency

AMI
Amazon machine image

EC2 Image Builder
Used to automate the creation of VM or container images

- Automate the creation, maintain, validate and test EC2 AMIs
- Can be run on a schedule
- Free service

EC2 Instance Store
EFS - Elastic File System

Route 53
Edge location
CloudFront
CloudFormation

**Think infrastructure as code; think CloudFormation.**
S3
SNS
SQS
Trusted Advisor:

**cost optimization, security, fault tolerance, service limits, and performance improvement**

Inspector
GardDuty
Shield
WAF
NACL
IAM

- IAM Credential report
- IAM Access Advisor
- AWS IAM Identity Center
    - Simplify access management to multiple AWS accounts as well as facilitate single sign-on access to its AWS
      accounts
    - SSO,SAML

Lambda
ECS
EKS
ECR
LightSail
CloudWatch

**Think resource performance monitoring, events, and alerts; think CloudWatch.**

Config

Amazon Elastic Transcoder
Amazon Elastic Transcoder lets you convert media files that you have stored in Amazon S3 into media files in the formats
required by consumer playback devices

Macie
PII data detect

Neptune
Amazon Neptune is a fast, reliable, fully-managed graph database service t

Transcribe
**Speech to test**
Polly
RDS
DynamoDB
DocumentDB
RedShift
EFS
EBS
System Management
Personal Health dashboard
SDK
CLI
Management Console
Recognition

Cost Explorer
Forecast spending

**Well-Architected FrameWork:
Operation Excellence, Performance efficiency
Cost optimization, Security, reliability,**
sustainability
AWS Auto scaling
AWS Budgets
**Cost budget, Usage budget, Reservation budget and Savings Plans budget.**
AWS Service Quotas (Service Limits)

**The services that are part of foundations are:
Amazon VPC, AWS Trusted Advisor, AWS Service Quotas (formerly called AWS Service Limits).**

Amazon athena
Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 using standard SQL

AWS CloudHSM

AWS Wavelength ： 5G

AWS Direct connect
AWS LocalZone

Amazon EMR
Elastic map reduce

AWS Batch

AWS Elastic Beanstalk: Web applications

Internet Gateway

Amazon Lightsail

AWS Acceptable Use Policy

Global Accelerator

Amazon MQ

Amazon Kinesis data stream
Amazon Kinesis Data Streams enables you to build custom applications that process or analyze streaming data for
specialized needs.

AWS Storage Gateway
File,tape,volume

Amazon Kendra

Amazon Personalize
real-time personalized recommendations

Amazon Comprehend
**natural-language processing (NLP) service**
uses machine learning to find insights and relationships in a text
Amazon Comprehend is a natural language processing (NLP) service that uses machine learning to find meaning and insights
in text

Amazon Lex
lifelike conversational interactions.

AWS Knowledge Center

AWS CodeDeploy

AWS Cost and Usage Reports (AWS CUR)

**Just remember that only S3 and DynamoDB support VPC Endpoint Gateway.**
Others are VPC endpoint interface

### AWS Glue

AWS Glue is a fully managed extract, transform, and load (ETL) service that makes it easy for customers to prepare and
load their data for analytics.

Discover, prepare, and combine data for analytics, machine learning, and application development

Basic/Developer/Business/Enterprise

Customers with a Developer, Business or Enterprise support plan have access to best practice guidance.

Enterprise: TAM, AWS Concierge service

Business: Contextual guidance based on customer use-case,

AWS Firewall Manager

AWS PrivateLink
AWS PrivateLink provides private connectivity between VPCs and services hosted on AWS or on-premise

Amazon Quicksight - Amazon QuickSight is a scalable, serverless, embeddable, machine learning-powered business
intelligence (BI) service built for the cloud.

AWS Quick Starts - Template for CloudFormation

CloudEndure Disaster Recovery

Disaster Recovery

- Multi site
- Warm standBy
- Pilot light
- Backup and Restore

AWS codeStar
Code Commit,Code build, Code Deploy, Code pipeline integrated, Cloud9

Amazon WorkSpace
Amazon WorkSpaces is a managed, secure Desktop-as-a-Service (DaaS) solution

AWS Sumerian
3D and AR

### AWS Notes

在云中计算
全球基础设施和可靠性
联网
存储和数据库
安全性
监控和分析
定价和支持
迁移和创新
云科技

#### 云优势

咖啡店

- 按需访问访问 on-demand
- 避免大量前期投资
- 按需预置计算资源 scaling
- 按实际使用量服务 pay-as-you-go

#### 部署模型

- 云
- 本地
- 混合 hybrid

### 基于云的部署

- 应用程序的所有组件都在云中运行
- 将现有程序部署在云中

### 本地部署

- 使用虚拟化和资源管理工具部署资源
- 使用应用程序管理和虚拟化技术提高资源利用率

### 混合部署

- 基于云的资源连接到本地

#### 云计算的优势

- 成本优化
- 容量 stop guessing
- 规模效益
    - 较小规模：仅根据自己的使用量支付较高的价格
    - 从客户的使用总量中获益
- 速度和敏捷性
    - 数据中心：从需要资源到用哟耦资源需要几周
    - 云计算：几分钟

### 在云中的计算

启动实例，连接实例，使用实例

- on-demand
- compute saving plan
    - Compute Savings Plans、EC2 Instance Savings Plans 和 Amazon SageMaker Savings Plan
- dedicated instance
- dedicated host

### EC2 Auto scaling

### Amazon Lambda servless

### AWS Fargate

Compute Service

### 全球基础设施和可靠性 Global Infrastructure

31 Regions, more than 3 AZ, geographic
数据治理和法律要求，与客户的距离，区域内可用的服务，定价
99 AZ (Availability Zone) one or more data center
Local Zones less than 3 AZ
Wavelength zones, 5G
Edge location

AWS CloudFront
AWS OutPosts  
AWS Outposts is a fully managed service that offers the same AWS infrastructure, AWS services, APIs, and tools to
virtually any data center, co-location space, or on-premises facility for a truly consistent hybrid experience.
AWS EdgeLocation

AWS Management Console
AWS CLI
AWS SDK

### 联网

Amazon Virtual Private Cloud
Subnet
Public Subnet IGW (Internet Gateway)
Private Subnet VPG (Virtual Private Gateway)

Amazon Direct Connect

NACL/Security Group
NetWork Access Control List:

- Work for subnet
- By default, allow all inbound and outbound traffic
- Stateless

Security Group :

- Instance level
- By default, reject all inbound traffic, allow all outbound traffic
- Stateful

DNS: domain
Amazon Route53 100% 可用性

### 存储和数据库

- EBS
- S3
- EFS
- RDS

Category:

- Block storage, one block one instance
    - Instance store
    - EBS Elastic Block storage, single AZ level
        - EBS SnapShot, incremental backup
        - Charged by
            - Volume type (based on performance),
            - Storage volume in GB per month provisioned,
            - Number of IOPS provisioned per month,
            - Storage consumed by snapshots,
            - Outbound data transfer.

- Object storage:
    - Simple storage service (11 9 Durability) Regional but global accessible
        - Key (reference path), Metadata, Data
        - Bucket (global unique)
        - Access policy
        - Storage class
            - S3 standard, at least 3 AZs
                - Amazon S3 Object Lock
                - Amazon S3 Storage Lens
            - S3 IA (Infrequent access) less storage fee, more retrieve fee
            - S3 IA Single Zone, less storage fee
            - S3 Intelligent Tier, a littler monitoring and automation fee
            - S3 Glacier, retrieve objects minutes to hours
                - Amazon S3 Glacier Vault Lock
            - S3 Glacier Deep Archive in 12 hours
- File storage
    - EFS (NFS,SMB), Elastic file system, store in multi AZs
- DataBase
    - SQL (Structure query language)
        - AWS RDS (Relational database) Paas
            - Amazon Aurora
                - 3 AZ replicates with 6 copy
                - enterprise
                - remove unnecessary I/O operation to reduce DB cost
            - PostgreSQL
            - MySQL
            - MariaDB
            - Oracle Database
            - Microsoft SQL SErver
    - No-SQL (Key-value)
        - DynamoDB
            - Serverless
            - Auto Scaling
            - over 10 thousand billion
    - Amazon Database Migration service (DMS)
    - Amazon RedShift (Data lake)
    - Amazon DocumentDB (Based on MongoDB)
    - Amazon Neptune (Graph QL)
    - Amazon QLDB:
      Amazon QLDB is a fully managed ledger database that provides a transparent, immutable, and cryptographically
      verifiable transaction log ‎owned by a central trusted authority
    - Amazon Managed Blockchain
    - Amazon ElasticCache (Redis,Memcache)
    - Amazon DynamoDB Accelerator

- Security
    - Shared responsibility model
        - Customer: security in the cloud
        - AWS: security of the cloud
    - IAM (Identity access management)
        - IAM user
        - IAM role (temporary access)
        - IAM group
        - IAM policy (JSON) Effect&Action
        - MFA (Multi-Factor Authentication)
        - IAM access advisor
        - IAM Credential report
    - AWS Organisation
        - OU (Organisation unit)
        - SCP (Service control policy)
    - AWS Artifact
    - AWS customer compliance center
    - AWS WAF (Reject rule Distributed Denial-of-Service (DDoS))
        - CloudFront,APIGateway,ALB, AppSync
        - AWS WAF gives you control over how traffic reaches your applications by enabling you to create security rules
          that block common attack patterns, such as SQL injection or cross-site scripting, and rules that filter out
          specific traffic patterns you define.
    - AWS Shield
        - Standard (free)
        - Advance: Amazon Elastic Compute Cloud, Elastic Load Balancing (ELB), Amazon CloudFront, Amazon Route 53, AWS
          Global Accelerator.
    - AWS Inspector (Security assess)
    - AWS KMS (Key management service)
    - AWS GuardDuty (account threat detected)
        - VPC flowLog
        - DNS
        - CloudTrail insights
    - AWS Detective
        - VPC flowLog
        - CloudTrail insights
        - GuardDuty findings

- Monitoring and Analyze
    - CloudWatch (metrics, threshold, alert) (on-cloud and on-prem)
    - CloudTrail (Audit API Request)
        - Event: What, Who, When, How
        - Management Event
        - Data event (Additional Fee)
        - Insight Event (Additional Fee)
        - **Think account-specific activity and audit; think CloudTrail.**
    - Amazon Trusted Advisor
        - cost optimisation
        - service limit
        - performance
        - security
        - fault tolerance
    - AWS X-Ray
        - AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a
          microservices architecture

- Pricing
    - Pricing calculator
        - server, storage, network, IT labor.
    - Billing
    - AWS cost explorer
    - AWS Consolidated billing
    - AWS budgets (cost,usage,reserved,saving plan)
- Amazon support
    - basic
        - Personal health dashboard
        - core Amazon trusted advisor
    - developer
    - business
    - enterprise
        - TAM (Technical account manager)
- Amazon Marketplace
- Amazon migration
    - AWS cloud adoption framework 业务能力+技术能力
        - 业务
        - 人员
        - 监管
        - 平台
        - 安全性
        - 操作
    - Migration solution
        - re-host
        - re-platform DB --> RDS
        - refactor
        - re-purchase
        - retain
        - retire
        - re-locate
    - Innovation
        - Serverless
        - AI
        - ML
    - Amazon Well-Architected Framework
        - Operational Excellence
        - Security
        - Reliability
            - Foundations
                - Amazon VPC, AWS Trusted Advisor, AWS Service Quotas
            - Management
                - CloudTrail, CloudWatch, Config
        - Performance Efficiency
        - Cost optimisation
        - Sustainability
    - AWS Well-Architected Tool
    - AWS cloud practitioner

Reservations support:
Amazon EC2 Reserved Instances, Amazon DynamoDB Reserved Capacity,Amazon ElasticCache Reserved Nodes, Amazon RDS RIs,
Amazon Redshift Reserved Nodes

AWS Compute Optimizer:
Amazon EC2 instances, Amazon EC2 Auto Scaling groups, Amazon EBS volumes, AWS Lambda functions

Main cost:
Compute, Storage, Outbound data transfer

AWS Systems Manager:
on-prem and on-cloud, centrally operational work

### Six advantages of cloud computing

- Trade Capital Expense for Variable/Operational Expense
- Massive economies of scale
- Stop guessing capacity
- Increase speed and agility
- Stop spending money running and maintaining data centers
- Go global in minutes

### Five characteristics of cloud computing

- On-demand self-service
- Broad network access
- Resource pooling
- Rapid elasticity
- Measured service

AWS Systems Manager Session Manager
AWS SSM Session Manager is a fully-managed service that provides you with an interactive browser-based shell and CLI
experience

Global Accelerator

AWS IAM, Amazon CloudFront, Route 53 and WAF are some of the global services.

Elastic Load Balancer

- Application Load Balancer
    - HTTP and HTTPS
- Network Load Balancer
    - TCP
    - UDP
    - TLS
- Gateway Load Balance
  Network Load Balancer
  Network Load Balancer is best suited for load balancing of Transmission Control Protocol (TCP), User Datagram
  Protocol (UDP) and Transport Layer Security (TLS) traffic where extreme performance is required.

AWS Security Token Service (AWS STS)
AWS Security Token Service (AWS STS) is a web service that enables you to request temporary, limited-privilege
credentials for AWS Identity and Access Management (IAM) users or for users that you authenticate (federated users).

AWS OpsWorks:
AWS OpsWorks is a configuration management service that provides managed instances of Chef and Puppet

AWS Fault Injection Simulator
AWS Fault Injection Simulator is a fully managed service for running fault injection experiments on AWS that makes it
easier to improve an application’s performance, observability, and resiliency.

AWS CodeGuru (Code review)
Amazon CodeGuru is a developer tool that provides intelligent recommendations to improve code quality and identify an
application’s most expensive lines of code

IAM access advisor
Access advisor shows the service permissions granted to a user and when those services were last accessed.

AWS CodeArtifact, CodeGuru,CodePipeline,CodeCommit,CodeStart,CodeBuild,CodeDeploy,Cloud9

AWS IoT Core
AWS IoT Core lets you connect IoT devices to the AWS cloud without the need to provision or manage servers

Amazon Connect
Amazon Connect is an easy to use omnichannel cloud contact center that helps you provide superior customer service at a
lower cost.

AWS Control Tower
AWS Control Tower provides the easiest way to set up and govern a new, secure, multi-account AWS environment based on
best practices established through AWS’ experience working with thousands of enterprises as they move to the cloud

Amazon API Gateway
Amazon API Gateway is a fully managed service that makes it easy for developers to create, publish, maintain, monitor,
and secure APIs at any scale.RESTful APIs and WebSocket APIs

Amazon SageMaker

Amazon SageMaker is a fully managed service that provides every developer and data scientist with the ability to build,
train, and deploy machine learning (ML) models quickly.

Amazon Forecast
Amazon Forecast is a fully managed service that uses machine learning to deliver highly accurate forecasts.

AWS OpsHub - AWS OpsHub is a graphical user interface you can use to manage your AWS Snowball devices, enabling you to
rapidly deploy edge computing workloads and simplify data migration to the cloud

Amazon AppStream 2.0 - Amazon AppStream 2.0 is a fully managed non-persistent application and desktop streaming service.
You centrally manage your desktop applications on AppStream 2.0 and securely deliver them to any computer

AWS Device Farm
AWS Device Farm is an application testing service that lets you improve the quality of your web and mobile apps by
testing them across an extensive range of desktop browsers and real mobile devices; without having to provision and
manage any testing infrastructure.

Credential Reports
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg3OTI5OTIxLDE2NDYyMTEyNCwtNjI0OT
Q1ODk4LDc0MzUxMzEwMSw4MjkyMDY1MDksLTQ3MzE5MTEwNCwt
MTE2OTg4ODg1MF19
-->