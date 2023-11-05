## EC2

- EC2 is one of the most popular of AWS' offering
- Elastic Compute Cloud = Infrastructure as a Service
- It mainly consists in the capability of :
    - Renting virtual machines (EC2)
    - Storing data on virtual drives (EBS)
    - Distributing load across machines (ELB)
    - Scaling the services using an auto-scaling group (ASG)
- Knowing EC2 is fundamental to understand how the Cloud works

### EC2 sizing & configuration options

- Operating system (OS): Linux,Windows,MacOS
- Compute power & cores: CPU
- Random-access memory: RAM
- Storage
    - Network-attached (EBS,EFS)
    - hardware (EC2 instance store)
- Network card: speed of the card, Public IP address
- Firewall rules: security group
- Bootstrap script (configure at first launch) : EC2 User Data

### EC2 User Data

- EC2 instance user data is the data that you specified in the form of a bootstrap script or configuration parameters,
  while launching your instance.
- It is possible to bootstrap our instances using an EC2 User data script
- bootstrapping means launching commands when a machine starts
- Run only once at the instance first start
- EC2 user data is used to automate boot tasks such as:
    - Installing updates
    - Installing software
    - Downloading common files from the internet
    - Anything you can think of
- The EC2 User Data Script runs with the root user

### EC2 instance metadata

- EC2 instance metadata is data about your instance that you can use to manage the instance.

### Classic Ports to know

- 22 = SSH (Secure shell) - log into Linux instance
- 21 = FTP (File Transfer Protocol) - upload files into a file share
- 22 = SFTP (Secure File Transfer Protocol) - upload files using SSH
- 80 = HTTP - access unsecured websites
- 443 = HTTPS - access secured websites
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
        - High performance, relational/non-relational databases
        - Distributed web scale cache stores
        - In-memory databases optimized for BI (business intelligence)
        - Application performing real-time processing of big unstructured data
- Storage Optimized: I,G,H
    - Great for storage-intensive tasks that require high, sequential read and write access to large data sets on local
      storage
    - Use cases
        - High frequency online transaction procession (OLTP) systems
        - Relational & NoSQL databases
        - Cache for in-memory databases (for example, Redis)
        - Data warehousing applications
        - Distributed file systems

- AWS has the following naming convention
    - m5.2xlarge
    - m: instance class
    - 5: generation (AWS improves them over time)
    - 2xlarge: size within the instance class

### EC2 Instances Purchasing options

- On-Demand Instances
    - Pay for what you use
        - Linux/Windows - billing per second after the first minute
        - All other systems - billing per hour
    - Has the highest cost but no upfront payment
    - No long-term commitment
    - Recommend for short-term and uninterrupted workloads, where you can't predict how the application will behave
- Reserved (1&3 years)
    - Reserved Instances
        - Regional or Zonal
    - Up to 72% discount compared to On-demand
    - Reservation Period - 1 year (+discount) or 3 years (+++discount)
    - Payment options - No upfront(+), Partial upfront(++), All upfront(+++)
    - Reserved Instance's Scope - Regional or Zonal (reserve capacity in an AZ)
    - Recommended for steady-state usage applications (think database)
    - You can buy and sell in the Reserved Instance Marketplace
    - Convertible Reserved instances
        - Can change the EC2 instance type/instance family/OS/scope and tenancy
        - Up to 66% discount
- Saving Plans (1&3 years)
    - Get a discount based on long-term usage (up to 72% - same as RIs)
    - Commitment to an amount of usage, long workload
    - Commit to a certain type of usage ($10/hour for 1/3 years)
    - Usage beyond EC2 Saving plans is billed at the on-demand price
    - Locked to a specific instance family & AWS region (e.g: M5 in us-east-1)
    - Flexible across
        - Instance size
        - OS
        - Tenancy (Host,Dedicated,Default)
- Spot Instances
    - Short workloads, cheap, can lose instances (less reliable)
    - Can get up to 90% discount compared to On-demand
    - Instances that you can "lose" at any point of time if your max price is less than the current spot price
    - The MOST cost-efficient instances in AWS
    - Useful for workloads that are resilient to failure
        - Batch jobs
        - Datta analysis
        - Image processing
        - Any distributed workloads
        - Workloads with a flexible start and end time
    - Not suitable for critical jobs or databases
- Dedicated Hosts
    - A physical server with EC2 instance capacity fully dedicated to your use
    - Book an entire physical server, control instance placement
    - Address compliance requirement and use existing server-bound software licenses (per-socket, per-core, pe-VM
      software licenses)
    - Purchasing options
        - On-demand - pay per second for active Dedicated Host
        - Reserved (1&3 years) (No Upfront, Partial Upfront, All Upfront)
    - The most expensive option
    - Useful for software that have compliance licensing model (BYOL - Bring Your Own License)
    - Or for companies that have strong regulatory or compliance needs
- Dedicated Instances
    - No other customers will share your hardware, Instances run on hardware that's dedicated to you
    - May share hardware with other instances in same account
    - No control over instance placement (can move hardware after Stop/Start)
- Capacity Reservation
    - Reserve On-Demand instances capacity in a **specific AZ** for any duration
    - You always have access to EC2 capacity when you need it
    - No time commitment (create/cancel any time), no billing discounts
    - Combine with Regional Reserved Instances and Saving Plans to benefit from billing discounts
    - You're charged at On-Demand rate whether you run instances or not
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
- Security Groups Deeper Dive
    - Security groups are acting as a "firewall" on EC2 instances
    - They regulate:
        - Access to Ports
        - Authorised IP ranges - IPv4 and IPv6
        - Control of inbound network (from other to the instance)
        - Control of outbound network (from the instance to other)
- Security Groups Good to know
    - Can be attached to multiple instances
    - Locked down to a region/VPC combination
    - Does live "outside" the EC2 - if traffic is blocked the EC2 instance won't see it
    - It's good to maintain one seperate security group for SSH access
    - If you application is not accessible (time out), then it's a security group issue
    - If your application gives a "connection refused" error, then it's an application error or it's not launched
    - All inbound traffic is blocked by default
    - All outbound traffic is authorized by default

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
