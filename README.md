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

Amazon S3

### Buckets

- Globally unique name
- region level

### Objects

- have a key
- the key is the FULL path
- no concept of "directories"

### Security

- User-based
    - IAM polices
- Resource based
    - Bucket policies
    - Object ACL
    - Bucket ACL
- Encryption

### Versioning

### Replication

- CRR (Cross-Region)
- SRR (Same-Region)
- async
- need to enable versioning

### S3 storage classes

Durability: 11 9's
Availability

- S3 Standard - General Purpose
    - 4 9's Availability
    - Low latency and high throughput
- S3 Storage Classes -Infrequent Access
    - Standard-IA
    - One Zone-IA
- S3 Glacier Storage Classes
    - Low-cost object storage mean for archiving/backup
    - price for storage + object retrieval cost
    - S3 Glacier Instant Retrieval
        - Millisecond retrieval
    - S3 Glacier Flexible Retrieval
        - Expedited(1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours)
    - S3 Glacier Deep Archive
        - Standard (12 hours), Bulk(48 hours)
- S3 Intelligent-Tiering
    - Small monthly monitoring and auto-tiering fee
    - No retrieval charges

### AWS Snow family

collect and process data at the edge
migrate data into and out of AWS

- Snowball Edge
    - move TBs or PBs of data
    - Snowball Edge Storage Optimized
        - 80TB
    - Snowball Edge Compute Optimized
        - 42TB
- Snowcone
    - Snowcone - 8TB of HDD Storage
    - Snowcone SSD - 14 TB of SSD Storage
    - AWS DataSync
- Snowmobile
    - Transfer exabytes of data
    - 100 PB of capacity
    - Better than snowball if you transfer more than 10 PB
- AWS OpsHub
    - A software to manage your Snow Family Device

### AWS Storage Gateway

Bridge between on-premise data and cloud data in S3

- File Gateway
- Volume Gateway
- Tape Gateway

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

### CloudFront

- CDN (Content Delivery Network)
- Improve read performance, content is cached at the edge
- DDoS protection, integration with Shield, WAF
- Origins
    - S3 bucket
    - Customer Origin (HTTP)
- Great for static content

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