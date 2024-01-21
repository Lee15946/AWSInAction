# Storage

## EBS & Local Instance Store

### EBS

- Network drive you attach to ONE instance only
- Linked to a specific availability zone (transfer: snapshot => restore)
- Volumes can be resized (only up)
- Make sure you choose an instance type that is EBS optimized to enjoy maximum throughput

### EBS Volume Types

- EBS Volumes come in 6 types
    - gp2 / gp3 (SSD): General purpose SSD volume that balances price and performance for a wide variety of workloads
    - io1 / io2 (SSD)/ io2 Block Express: Highest-performance SSD volume for mission-critical low-latency or
      high-throughtput workloads
    - st1 (HDD): Low cost HDD volume designed for frequently accessed, throughput-intensive workloads
    - sc1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads
- EBS Volumes are characterised in Size | Throughput | IOPS (I/O Ops Per Sec)
- When in doubt always consult the AWS documentation - it' good!
- Only gp2 / gp3 and io1 / io2 can be used as boot volumes

### EBS Snapshots

- Incremental - only backup changed blocks
- EBS backups use IO, and you shouldn't run them while your application is handling a lot of traffic
- Snapshots will be stored in S3 (but you won't directly see them)
- Not necessary to detach volume to do snapshot, but recommend
- Can copy snapshots across region (for DR)
- Can make Image (AMI) from Snapshot
- EBS volumes restored by snapshots need to be pre-warmed (use the Fast Snapshot Restore FSR feature of fio/dd command
  to read the entire volume)

### Amazon Data Lifecycle Manager

- Automate the creation, retention, and deletion of EBS snapshpts and EBS-backed AMIs
- Schedule backups, cross-account snapshot copies, delete outdated backups,...
- Uses resource tags to identity the resources (EC2 instances, EBS volumes)
- Can't be used to manage snapshots/AMIs created outside DLM
- Can't be used to manage instance-store backed AMIs

### Amazon Data Lifecycle Manager vs. AWS Backup

- Use Data Lifecycle Manager
    - when you want to automate the creation, retention, and deletion of EBS Snapshots
- Use AWS Backup
    - to manage and monitor backups across the AWS services you use, including EBS volumes, from a single place

### EBS Encryption - Account level setting

- New Amazon EBS volumes aren't encrypted by default
- There's an account-level setting to encrypt automatically new EBS volumes and Snapshots
- This setting needs to be enabled on a per-region basis

### EBS Multi-Attach - io1 / io2 family

- Attach the same EBS volume to multiple EC2 instances in the same AZ
- Each instance has full read & write permissions to the volume
- Use case:
    - Achieve higher application availability in clustered Linux applications (ex: Teradata)
    - Applications must manage concurrent write operations
- Must use a file system that's cluster-aware (not XFS, EX4, etc...)

### Local EC2 Instance Store

- Physical disk attached to the physical server where you EC2 is
- Very High IOPS (because physical)
- Disk up to 7.5 TiB (can changed over time), stripped to reach 60 TiB (can changed over time...)
- Block Storage (just like EBS)
- Cannot be increased in size
- Risk of data loss if hardware fails

### Instance Store vs EBS

- Instance store is physically attached to the machine (ephemeral storage)
- EBS is network drive (persistent)
- Pros:
    - Better I/O performance (EBS gp2 has a max IOPS of 16000, io1 of 64000, io2 Block Express of 256000)
    - Good for buffer / cache / scratch data / temporary content
    - Data survives reboots
- Cons:
    - On stop or termination, the instance store is lost
    - You can't resize the instance store
    - Backups must be operated by the user

## Amazon EFS

### EFS - Elastic File System

- Managed NFS (network file system) that can be mounted on many EC2
- EFS works with EC2 instances in multi-AZ, & on-premise (DX & VPN)
- Highly available, scalable, expensive (3x gp2), pay per GB used
- Use cases: content management, web serving, data sharing, WordPress
- Compatible with Linux based AMI (not Windows), POSIX-compliant
- Use NFSv4.1 protocol
- Use security group to control access to EFS
- Encryption at rest using KMS
- Can only attach to one VPC, create on ENI (mount target) per AZ
- POSIX file system (~Linux) that has a standard file API
- File systme scales automatically, pay-per-use, no capacity planning!

### EFS - Performance & Storage Classes

- EFS Scale
    - 1000s of concurrent NFS clients, 10GB+ /s throughput
    - Grow to Petabyte-scale network file system automatically
- Performance Mode (set at EFS creation time)
    - General Purpose (default) - latency-sensitive use cases (web server, CMS, etc...)
    - Max I/O - higher latency, throughput, highly parallel (big data, media processing)
- Throughput Mode
    - Bursting - 1TB = 50BMiB/s + burst of up to 100MiB/s
    - Provisioned - set your throughput regardless of storage size, ex: 1GiB/s for 1TB storage
    - Elastic - automatically scales throughpur up or down based on your workloads
        - Up to 3GiB/s for reads and 1GiB/s for writes
        - Used for unpredictable workloads

### EFS - Storage Classes

- Storage Tiers (lifecycle management feature - move file after N days)
    - Standard: for frequently accessed files
    - Infrequent access (EFS-IA): cost to retrieve files, lower price to store. Enable EFS-IA with a Lifecycle Policy
- Availability and durability
    - Regional: Mutli-AZ, great for prod
    - One Zone: One AZ, great for dev, backup enabled by default, compatible with IA (EFS One Zone-IA)

### EFS - On-premises & VPC Peering

- On-premises Server
    - Direct Connect OR/AND Site-to-Site VPN
    - NFS Mount Target by IPv4 (not DNS)
    - Redundancy in DX / DX or DX / VPN
- AWS Cloud
    - Amazon EFS
    - ENI Redundancy in mount target (ENI)
- VPC
    - EC2
    - VPC peering

### EFS - Access Points

- Easily manage applications access to EFS environments
- Enforce a POSIX user and group to use when accessing the file system
- Restrict access to a directory within the file system and optionally specify a different root directory
- Can restrict access from NFS clients using IAM policies

### EFS - File System Policies

- Resource-based policy to control access to EFS File Systems (same as S3 bucket policy)
- By default, it grants full access to all clients

### EFS - Cross-Region Replication

- Replicate objects in an EFS file system to another AWS Region
- Setup for new or existing EFS file systems
- Provides RPO and RTO of minutes
- Doesn't affect the provisioned throughput of the EFS file system
- Use cases: meet your compliance and business continuity goals

## S3

### S3 - Overview

- Object to storage, serverless, unlimited storage, pay-as-you-go
- Good to store static content (image, video files)
- Access objects by key, no indexing facility
- Not a file system, cannot be mounted natively on EC2
- Anti patterns
    - Lots of small files
    - POSIX file system (use EFS instead), file locks
    - Search features, queries, rapidly changing data
    - Website with dynamic content

### S3 Storage Classes Comparison

- You can transition objects between tiers (or delete) using S3 Lifecycle Policies

### S3 - Replication (Version enabled)

- Cross Region Replication (CRR)
- Same Region Replication (SRR)
- Combine with Lifecycle Rules
- Helpful to reduce latency, disaster recovery, security
- S3 Replication Time Control (S3 RTC)
    - Replicates most objects that you upload to Amazon S3 in seconds, and 99.99% of those objects within 15 minutes
    - Helpful for compliance, DR, etc ...

### S3 Event Notifications

- `S3:ObjectCreated`, `S3:ObjectRemoved`, `S3:ObjectRestore`, `S3:Replication`...
- Object name filtering possible (*.jpg)
- Use case: generate thumbnails of images uploaded to S3
- Can create as many "S3 events" as desired
- S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer

### S3 Event Notifications with Amazon EventBridge

- Advanced filtering options with JSON rules (metadata, object size, name...)
- Multiple Destinations - ex Step Functions, Kinesis Streams / Firehose...
- EventBridge Capabilities - Archive, Replay Events, Reliable delivery

### S3 - Baseline Performance

- Amazon S3 automatically scales to high request rates, latency 100-200 ms
- Your application can achieve at least 3500 PUT/COPY/POST/DELETE or 5500 GET/HEAD requests per second per prefix in a
  bucket
- There are no limits to the number of prefixes in a bucket
- Example (object path => prefix)
    - bucket/folder/sub1/file => /folder/sub1/
    - bucket/folder/sub2/file => /folder/sub2/
    - bucket/1/file => /1/
    - bucket/2/file => /2/
- If you spread reads across all four prefixes evenly, you can achieve 22,000 requests per second for GET and HEAD

### S3 Performance

- Multi-Part upload
    - recommended for files > 100MB, must use for files > 5GB
    - Can help parallelize uploads (speed up transfers)
- S3 Transfer Acceleration
    - Increase transfer speed by transferring file to an AWS edge location which will forward the data to the S3 bucket
      in the target region
    - Compatible with multi-part upload

### S3 Performance - S3 Byte-Range Fetches

- Parallelize GETs by requesting specific byte ranges
- Better resilience in case of failures
- Can be used to speed up downloads
- Can be used to retrieve only partial data (for example the head of a file)

### S3 Multi-Part Upload - Remove Incomplete Parts

- Use Lifecycle Policy to abort & delete incomplete Multi-part Uploads after X days
- Use AWS CLI to List Incomplete Multi-part Uploads

### S3 Select & Glacier Select

- Retrieve less data using SQL by performing server side filtering
- Can filter by rows & columns (simple SQL statements)
- Less network transfer, less CPU cost of client-side

## Amazon S3 - Storage Class Analysis

- May be seen as "Storage Class Analysis" at the exam
- Help you decide when to transition objects to the right storage class
- Recommendations for Standard and Standard IA
    - Does NOT work for One-Zone IA or Glacier
- Report is updated daily
- 24 to 48 hours to start seeing data analysis
- Visualize data in Amazon QuickSight
- Good first step to put together Lifecycle Rules (or improve them)!

## Amazon S3 - Storage Lens

- Understand, analyze, and optimize storage across entire AWS Organization
- Discover anomalies, identiy cost efficiencies, and apply data protection best practices across entire AWS
  Organization (30 days usage & activity metrics)
- Aggregate data for Organization, specific accounts, regions, buckets, or prefixes
- Default dashboard or create your own dashboards
- Can be configured to export metrics daily to an S3 bucket (CSV, Parquet)
- S3 Storage Lens -> Organization / Accounts / Regions / Buckets -> Aggregate & Analyze (Dashboard) -> Summary
  Insights / Data Protection / Cost Efficiency

### Storage Lens - Default Dashboard

- Visualize summarized insights and trends for both free and advanced metrics
- Default dashboard shows Multi-Region and Mutli-Account data
- Preconfigured by Amazon S3
- Can't be deleted, but can be disabled

### Storage Lens - Free vs. Paid

- Free Metrics
    - Automatically available for all customers
    - Contains around 28 usage metrics
    - Data is available for queries for 14 days
- Advanced Metrics and Recommendations
    - Additional paid metrics and features
    - Advanced Metrics - Activity, Advanced Cost Optimization, Advanced Data Protection, Status Code
    - CloudWatch Publishing - Access metrics in CloudWatch without additional charges
    - Prefix Aggregation - Collect metrics at the prefix level
    - Data is available for queries for 15 months

## S3 Solution Architecture

### S3 Solution Architecture - Exposing Static Objects

- EC2 Instance Store
- CloudFront -> EC2 -> EBS
- CloudFront -> ALB -> ASG -> EFS
- CloudFront -> S3

### S3 Solution Architecture - Indexing objects in DynamoDB

- writes -> Amazon S3 -> Lambda Function -> DynamoDB Table
- API for object metadata
    - Search by date
    - Total storage used by a customer
    - List of all objects with certain attributes
    - Find all objects uploaded within a date range

### Solution Architecture on AWS - Dynamic vs Static Content

- DNS Layer (Route 53) -> Dynamic -> Dynamic Content (REST, HTTP server): ALB + EC2 / API Gateway + Lambda -> Database
  Layer (DynamoDB) -> Caching / Session Layer (DAX & DynamoDB)
- Static -> CDN Layer (CloudFront) -> Static content -> Static Assets Layer (S3) -> events -> Lambda Function
- Pre-signed URL

## Amazon FSx

### Amazon FSx - Overview

- Launch 3rd party high-performance file systems on AWS
- Fully managed service
- FSx for Lustre
- FSx for NetApp ONTAP
- FSx for Windows File Server
- FSx for OpenZFS

### Amazon Fsx for Windows (File Server)

- FSx for Windows is a fully manged Windows file system share drive
- Support protocol & Windows NTFS
- Microsoft Active Directory integration, ACLs, user quotas
- Can be mounted on Linux EC2 instances
- Supports Microsoft's Dirstributed File System (DFS) Namespaces (groups files across multiple FS)
- Scale up to 10GB/s, millions of IOPS, 100s PB of data
- Storage Options:
    - SSD - latency sensitive workloads (databases, media processing, data analytics...)
    - HDD - board spectrum of workloads (home directory, CMS...)
- Can be accessed from your on-premises infrastructure (VPN or Direct Connect)
- Can be configured to Multi-AZ (high availability)
- Data is backed-up daily to S3

### Amazon FSx for Lustre

- Lustre is a type of parallel distributed file system, for large-scale computing
- The name Lustre is derived form "Linux" and "cluster"
- Machine Learning, High Performance Computing (HPC)
- Video Processing, Financial Modeling, Electronic Design Automation
- Scales up to 100s GB/s, millions of IOPS, sub-ms latencies
- Storage Options:
    - SSD - low-latency, IOPS intensive workloads, small & random file operations
    - HDD - throughput-intensive workloads, large & sequential file operations
- Seamless integration with S3
    - Can "read S3" as a file system (through FSx)
    - Can write the output of the computations back to S3 (through FSx)
- Can be used from on-premises servers (VPN or Direct Connect)

### FSx File System Deployment Options

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

### Amazon FSx for NetApp ONTAP

- Managed NetApp ONTAP on AWS
- File System compatible with NFS, SMB, iSCSI protocol
- Move workloads running on ONTAP or NAS to AWS
- Works with
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Storage shrinks or grows automatically
- Snapshots replication, low-cost, compression and data de-duplication
- Point-in-time instantaneous cloning (helpful for testing new workloads)

### Amazon FSx for OpenZFS

- Managed OpenZFS file system on AWS
- File System compatible with NFS (v3, v4, v4.1, v4.2)
- Move workloads running on ZFS to AWS
- Works with
    - Linux
    - Windows
    - MacOS
    - VMware Cloud on AWS
    - Amazon Workspaces & AppStream 2.0
    - Amazon EC2, ECS and EKS
- Up to 1,000,000 IOPS with < 0.5ms latency
- Snapshots compression and low-cost
- Point-in-time instantaneous cloning (helpful for testing new workloads)

### FSx - Solution Architecture

### FSx -Solution Architecture - Migration from Single AZ to Multi AZ

- FSx for Window File Server (Single AZ) -> AWS DataSync -> FSx for Windows File Server (Multi-AZ)
- FSx for Window File Server -> backup -> Backup -> restore -> FSx for Windows File Server (Multi-AZ)

### FSx - Solution Architecture - Decrease FSx Volume Size

- If you take a backup, you can only restore to a same size
- You can only increase the amount of storage capacity for a file system, you cannot decrease storage capacity
- Instead, create a new FSx (smaller), use DataSync to sync data and then migrate your app over
- FSx for Window File Server (2TB) -> AWS DataSync -> FSx for Window File Server (1TB) -> Application Switch

### FSx for Lustre - Data Lazy Loading

- Any data processing job on Lustre with S3 as an input data source can be started without Lustre doing a full download
  of the dataset first
- Data is lazy loaded: only the data is acutally processed is loaded, meaning you can decrease your costs and latency
- Data is also loaded only once, there fore you reduce your requests on Amazon S3

## AWS DataSync

- Move large amount of data to and from
    - On-premises / other cloud to AWS (NFS, SMB, HDFS, S3 API...) - needs agent
    - AWS to AWS (different storage services) - no agent needed
    - Can synchronize to:
        - Amazon S3 (any storage classes - including Glacier)
        - Amazon EFS
        - Amazon FSx (Windows, Lustre, NetApp, OpenZFS...)
    - Replication tasks can be scheduled hourly, daily, weekly
    - File permissions and metadata are preserved (NFS POSIX, SMB...)
    - One agent task can use 10Gbps, can setup a bandwidth limit

### AWS DataSync - NFS / SMB to AWS (S3, EFS, FSx...)

- On-Premises
    - NFS or SMB Server
    - AWS DataSync Agent
    - AWS Snowcone (agent pre-installed)
- Region
    - AWS DataSync
    - AWS Storage Resources
    - TLC connection

### AWS DataSync - Transfer between AWS storage services

- AWS DataSync
    - copy data and metadata between AWS Storage Services

## AWS DataSync Solution Architecture

### AWS DataSync - Private VIF through Direct Connect

- Corporate Data Center (DataSync Agent) -> Direct Connect -> Private VIF -> VPC -> PrivateLink -> Interface VPC
  Endpoint -> AWS DataSync

## AWS Transfer Family

- A fully-managed service for file transfers into and out of Amazon S3 or Amazon EFS using the FTP protocol
- Supported Protocols
    - AWS Transfer for FTP (File Transfer Protocol (FTP))
    - AWS Transfer for FTPS (File Transfer Protocol over SSL (FTPS))
    - AWS Transfer for SFTP (Secure File Transfer Protocol (SFTP))
- Managed infrastructure, scalable, Reliable,High Available (multi-AZ)
- Pay per provisioned endpoint per hour + data transfer in GB
- Store and manage users' credentials within the service
- Integrate with existing authentication systems (Microsoft Active Directory, LDAP, Okta, Amazon Cognito, custom)
- Usage: sharing files, public datasets, CRM, ERP
- Users (FTP client) -> Route 53 (optional) -> AWS Transfer Family -> IAM Role -> Amazon S3 / Amazon EFS

### AWS Transfer Family - Endpoint Types

- Public Endpoint
    - Internet -> AWS Cloud -> SFTP
    - IPs managed by AWS (subject to changes, use DNS names)
    - Can't setup allow lists by sourcee IP addresses
- VPC Endpoint with Internal Access
    - VPC (EC2 instance) -> SFTP -> VPN or Direct Connect -> Corporate Data Center
    - Static private IPs
    - Setup allows lists (SGs & NALCs)
- VPC Endpoint with Internet-facing Access
    - Internet -> VPC -> SFTP (EIP) -> EC2 Instance -> VPN or Direct Connect -> Corporate Data Center
    - Static private IPs
    - Static public IPs (EIPs)
    - Setup Security Groups

## AWS Storage Services Price Comparison

### Price of Storage (GB Month)

- expensive -> cheap
    - EBS io2, io1
    - EFS Standard, EFS One-Zone
    - EBS gp2, gp3
    - EBS st1
    - EFS Standard IA, S3 Standard
    - EBS sc1
    - EFS One-Zone IA
    - S3 Standard IA, etc...


































 


