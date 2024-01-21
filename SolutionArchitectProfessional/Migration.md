# Migration

## Cloud Migrations - The 6R

- Rehosting: "lift and shift"
    - Simple migrations by re-hosting on AWS (applications, databases, data..)
    - No cloud optimizations being done, application is migrated as is
    - Could save as much as 30% on cost
    - Example: Migrate using AWS VM Import/Export. AWS Server Migration Service
- Replatforming
    - Example: migrate your database to RDS
    - Example: migrate your application to Elastic Beanstalk (Java with Tomcat)
    - Not changing the core architecture, but leverage some cloud optimizations
- Repurchase: "drop and shop"
    - Moving to a different product while moving to the cloud
    - Often you move to SaaS platform
    - Expensive in the short term, but quick to deploy
    - Example: CRM to Salesforce.com, HT to Workday, CMS to Drupal
- Refactoring / Re-architecting
    - Reimagining how the application is architected using Cloud Native features
    - Driven by the need of the business to add features, scale, performance
    - Example: move an application to Serverless architectures, use AWS S3
- Retire
    - Turn off things you don't need (maybe as a result of Re-architecting)
    - Helps with reducing the surface areas for attacks (more security)
    - Save cost, maybe up to 10% to 20%
    - Focus your attention on resources that must be maintained
- Retain
    - Do nothing for now (for simplicity, cost reason, importance)
    - It's still a decision to make in a Cloud Migration

## Storage Gateway

### Hybrid Cloud for Storage

- AWS is pushing for "hybrid cloud"
    - Part of your infrastructure is on the cloud
    - Part of your infrastructure is on-premises
- This can be due to
    - Long cloud migrations
    - Security requirements
    - Compliance requirements
    - IT strategy
- S3 is a proprietary storage technology (unlike EFS / NFS), so how you expose the S3 data on-premises

### AWS Storage Gateway

- Bridge between on-premises data and cloud data
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
- Application Server -> NFS or SMB -> S3 File Gateway -> HTTPS -> S3 -> Lifecycle policy -> S3 Glacier

### Amazon FSx File Gateway

- Native access to Amazon FSx for Windows File Server
- Local cache for frequently accessed data
- Windows native compatibility (SMB, NTFS, Active Directory..)
- Useful for group file shares and home directories

### Volume Gateway

- Block storage using iSCSI protocol backed by S3
- Backed by EBS snapshots which can help restore on-premises volumes
- Cached volumes: low latency access to most recent data
- Stored volumes: entire dataset is on premise, scheduled backups to S3

### Tape Gateway

- Some companies have backup processes using physical tapes
- With Tape Gateway, companies use the same processes but in the cloud
- Virtual Tape Library (VTL) backed by Amazon S3 and Glacier
- Back up data using existing tape-based processes (and iSCSI interface)
- Works with leading backup software vendors

### Storage Gateway - Hardware appliance

- Using Storage Gateway means you need on-premises virtualization
- Otherwise, you can use a Storage Gateway Hardware Appliance
- You can buy it on amazon.com
- Works with File Gateway, Volume Gateway, Tape Gateway
- Has the required CPU, memory, network, SSD cache resources
- Helpful for daily NFS backups in small data centers

### AWS Storage Gateway

- User/group file shares -> NFS/SMB -> File Gateway (local cache) -> Encryption in Transit, Internet or Direct Connect
    - -> Amazon S3 (excluding Glacier & Glacier Deep Archive) -> Any S3 Storage Class Including Glacier
    - -> Amazon FSx for Windows File Server -> Automated Backups -> Amazon S3
- Application Server -> iSCSI -> Volume Gateway (local cache) -> Amazon S3 -> AWS EBS
- Backup Application -> iSCSI VTL -> Tape Gateway (local cache) -> Amazon S3 (Tape Library) -> Eject from backup application -> Tape Archive (Glacier & Glacier Deep Archive)

## Storage Gateway - Advanced Concepts

### File Gateway: Extensions

- Corporate data center -> NFS / SMB -> File Gateway Appliance -> Amazon S3
- VPC -> EC2 -> NFS / SMB -> File Gateway Appliance -> Amazon S3
- Amazon S3 -> S3 Events -> Lambda
    - Athena
    - Redshift Spectrum
    - EMR
    - CRR -> Amazon S3

### File Gateway: Read Only Replicas

- File Share Client / Application Server -> File share -> File Gateway Appliance -> Amazon S3 -> File

### File Gateway: Backup and Lifecycle Policies

- File Share Client / Application Server -> File Gateway Appliance -> Amazon S3 Standard -> S3 Lifecycle -> Amazon S3 Standard - IA -> S3 Lifecycle -> Amazon S3 Glacier

### File Architectures: Other possibilities

- Amazon S3 Object Versioning
    - Ability to store multiple object versions as they are modified
    - Helpful to restore a file to a previous version
    - Could restore an entire file system to a previous version
    - Must use the 'RefreshCache' API on the Gateway to be notified of restore
- Amazon S3 Object Lock
    - Enables to have the File Gateway for Write Once Read Many (WORM) data
    - If there are file modifications or renames in the file share clients, the file gateway creates a new version of the object without affecting prior versions, and the original locked version will remain unchanged

## Snow Family

### AWS Snow Family

- Highly-secure, portable devices to collect and process data at the edge, and migrate data into and out of AWS
- Data migration: Snowcone, Snowball Edge, Snowmobile
- Edge computing: Snowcone, Snowball Edge

### Data Migrations with AWS Snow Family

- Challenges
    - Limited connectivity
    - Limited bandwidth
    - High network cost
    - Shared bandwidth (can't maximize the line)
    - Connection stability
- AWS Snow Family: offline devices to perform data migrations
- If it takes more than a week to transfer over the network, use Snowball devices!

### Diagrams

- Direct upload to S3
    - client -> Amazon S3 bucket
- With Snow Family
    - client & AWS Snowball -> ship -> AWS Snowball, import/export Amazon S3 bucket

### Snowball Edge (for data transfers)

- Physical data transport solution: move TBs or PBs of data in or out of AWS
- Alternative to moving data over the network (and paying network fees)
- Pay per data transfer job
- Provide block storage and Amazon S3-compatible object storage
- Snowball Edge Storage Optimized
    - 80TB of HDD capacity for block volume and S3 compatible object storage
- Snowball Edge Compute Optimized
    - 42TB of HDD or 28TB of NVMe capacity for block volume and S3 compatible object storage
    - Use cases: large data cloud migrations, DC decommissions, disaster recovery

### AWS Snowcone & Snowcone SSD

- Small, protable computing, anywhere, rugged & secure, withsatnds hardsh environments
- Light (4.5 pounds, 2.1kg)
- Device used for edge computing, storage, and data transfer
- Snowcone - 8TB of HDD Storage
- Sonwcone SSD - 14TB of SSD Storage
- Use Snowcone where Snowball does not fit (space-constained environemnt)
- Must proivde your owne battery / cables
- Can be sned to AWS offline, or connect it to internet and use AWS DataSync to send data

### AWS Snowmobile

- Transfer exabytes of data (1EB = 1,000PB = 1000,000TBs)
- Each Snowmobile has 100PB of capacity (use multiple in parallel)
- High security: temperature controlled, GPS, 24/7 video surveillance
- Better than Snowball if you transfer more than 10PB

### Snow Family - Usage Process

- Request Snowball devices from the AWS console for delivery
- Install the snowball client / AWS OpsHub on your servers
- Connect the snowball to your servers and copy files using the client
- Ship back the device when you're done (goe tho the right AWS facility)
- Data will be loaded into an S3 bucket
- Snowball is completely wiped

### What is Edge Computing?

- Process data while it's being created on an edge location
    - A truct on the road, a ship on the sea, a mining station underground...
- These locations may have
    - Limited / no internet access
    - Limited / no easy access to computing power
- We setup a Snowball Edge / Snowcone device to do edge computing
- Use cases of Edge Computing
    - Preprocess data
    - Machine learning at the edge
    - Transcoding media streams
- Eventually (if need be) we can ship bakc the device to AWS (for transferring data for example)

### Snow Family - Edge Computing

- Snowcone & Snowcone SSD (smaller)
    - 2CPUs, 4GB of memory, wired or wireless access
    - USB-C power using a cord or the option battery
- Snowball Edge - Compute Optimized
    - 104vCPUs, 416GiB of RAM
    - Optional GPU (useful for video processing or machine learning)
    - 28TB NVMe or 42TB HDD usable storage
    - Storage Clustering available (up to 16 nodes)
- Snowball Edge — Storage Optimized
    - Up to 40vCPUs, 80GiB of RAM, 80TB storage
- All: can run EC2 Instances & AWS Lambda functions (using AWS IoT Greengrass)
- Long-term deployment options: 1 and 3 year discounted pricing

### AWS OpsHub

- Historically, to use Snow Family devices, you needed a CLI (Command Line Interface tool)
- Today, you can use AWS OpsHub (a software you install on your compute / laptop) to manage your Snow Family Device
    - Unlocking and configuring single or clustered devices
    - Transferring files
    - Launching and managing instance running on Snow Family Devices
    - Monitor device metrics (storage capacity, active instances on your device)
    - Launch compatible AWS services on your devices (ex: Amazon EC2 instances, AWS DataSync, Nework File System(NFS))

### Snow Family - Improving transfer Performance

- Most impactful to least
    - Perform multiple write operations at one time - from multiple terminals
    - Transfer small files in batches - zip up small files until at lease 1MB
    - Don't perform other operations on files during transfer
    - Reduce local network use
    - Eliminate unnecessary hops - directly connect to the computer
- The data transfer rate using the file interface is typically between 25MB/s and 40MBs. If you need to transfer data faster than this, use the Amazon S3 Adapter for Snowball, which has a data transfer rate typically between 250MB/s and 400MB/s

## AWS DMS - Data Migration Service

### DMS - Database Migration Service

- Quickly and securely migrate databases to AWS, resilient, self-healing
- The source database remains available during the migration
- Supports
    - Homogeneous migrations: ex Oracle to Oracle
    - Heterogeneous migrations: ex Microsoft SQL Server to Aurora
- Continuous Data Replication using CDC
- You must create an EC2 instance to perform the replication tasks

### DMS Sources and Targets

- Sources
    - On-premises and EC2 instances databases: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, MonogoDB, SAP, DB2
    - Azure: Azure SQL Database
    - Amazon RDS: all including Aurora
    - Amazon S3
    - DocumentDB
- Targets
    - On-premises and EC2 instances databases: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, SAP
    - Amazon RDS including Aurora
    - Amazon Redshift
    - Amazon DynamoDB
    - Amazon S3
    - OpenSearch Service
    - Kinesis Data Streams
    - DocumentDB

### AWS Schema Conversion Tool (SCT)

- Convert your Database's Schema from one engine to another
- Example OLTP: (SQL Server to Oracle) to MySQL, PostgreSQL, Aurora
- Example OLAP: (Teradata to Oracle) to Amazon Redshift
- You do not need to use SCT if you are migrating the same DB engine
    - Ex: On-premises PostgreSQL => RDS PostgreSQL
    - The DB engine is still PostgreSQL (RDS is the platform)

### DMS - Good things to know

- Works over VPC Peering, VPN (site to site, software), Direct Connect
- Supports Full Load, Full Load + CDC, or CDC only
- Oracle:
    - Source: Supports TDE for the source using "BinaryReader"
    - Target: Supports BLOBs in tables that have a primary key, and TDE
- OpenSearch
    - Source: does not exist
    - Target: possible to migrate from a relational database using DMS
    - Therefore, DMS cannot be used to replicated OpenSearch data

### Snowball + Database Migration Service (DMS)

- Larger data migrations can include many terabytes of information
- Can be limited due to network bandwidth or size of data
- AWS DMS can use Snowball Edge & Amazon S3 to speed up migration
- Following stages
    - You use the AWS Schema Conversion Tool (AWS SCT) to extract the data locally and move it to an Edge device
    - You ship the Edge device or devices back to AWS
    - After AWS receives your shipment, the Edge device automatically loads its data into an Amazon S3 bucket
    - AWS DMS takes the files and migrates the data to the target data store. If you are using change data capture (CDC), those updates are written to the Amazon S3 bucket and then applied to the target data store

## AWS CART - Cloud Adoption Readiness Tool (CART)

- Helps organization develop efficient and effective plans for cloud adoption and migrations
- Transforms your idea of moving to the cloud into a detailed plan that follows AWS best practices
- Answer a set of questions across six perspectives (business, people, process, platform, operations, security)
- Generate a custom report on your level of migration readiness

## Disaster Recovery

### Disaster Recovery Overview

- Any event that has a negative impact on a company's business continuity of finances is a disaster
- Disaster recovery (DR) is about preparing for and recovering from a disaster
- What kind of disaster recovery?
    - On-premise => On-premise: traditional DR, and very expensive
    - On-premise => AWS Cloud: hybrid recovery
    - AWS Cloud Region A => AWS Cloud Region B
- Need to define two terms
    - RPO: Recovery Point Objective
    - RTO: Recovery Time Objective

### Disaster Recovery Strategies

- Backup and Restore
- Pilot Light
- Warm Standby
- Hot Site / Multi Site Approach

### Backup and Restore (High RPO)

- AWS Storage Gateway
- AWS Snowball
- Amazon S3
- Schedule regular snapshots

### Disaster Recovery - Pilot Light

- A small version of the app is always running in the cloud
- Useful for the critical core (pilot light)
- Very similar to Backup and Restore
- Faster than Backup and Restore as critical systems are already up

### Warm Standby

- Full system is up and running, but at minimum size
- Upon disaster, we can scale to production load

### Multi Site / Hot Site Approach

- Very low RTO (minutes or seconds) - very expensive
- Full Production Scale is running AWS and On Premise

### All AWS Multi Region

- Route 53 -> active swapping
- Data Replication between Aurora Global Database

### Disaster Recovery Tips

- Backup
    - EBS Snapshots, RDS automated backups / Snapshots, etc...
    - Regular pushes to S3/S3 IA/ Glacier, Lifecycle Policy, Cross Region Replication
    - From On-Premise: Snowball or Storage Gateway
- High Availability
    - Use Route 53 to migrate DNS over from Region to Region
    - RDS Multi-AZ, ElastiCache Multi-AZ, EFS, S3
    - Site to Site VPC as a recovery from Direct Connect
- Replication
    - RDS Replication (Cross Region), AWS Aurora + Global Databases
    - Database replication from on-premise to RDS
    - Storage Gateway
- Automation
    - CloudFormation / Elastic Beanstalk to re-create a whole new environment
    - Recover / Reboot EC2 instances with CloudWatch if alarms fail
    - AWS Lambda functions for customized automations
- Chaos
    - Netflix has a "simian-army" randomly terminating EC2

## AWS FIS - Fault Injection Simulator

### AWS Fault Injection Simulator (FIS)

- A fully managed service for running fault injection experiments on AWS workloads
- Based on Chaos Engineering - stressing an application by creating disruptive events (e.g, sudden increase in CPU or memory), observinghow the systems responds, and implementing improvements
- Helps you uncover hidden bugs and performance bottlenecks
- Supports the following AWS services: EC2, ECS, EKS, RDS...
- Use pre-built templates that generate the desired disruptions

## VM Migration Services

### Application Discovery Services

- Plan migration projects by gathering information about on-premises data centers
- Server utilization data and dependency mapping are important for migrations
- Agent-less discovery (Application Discovery Agent-less Connector)
    - Open Virtual Appliance (OVA) package that can be deployed to a VMware host
    - VM inventory, configuration, and performance histroy such as CPU, memory, and disk usage
    - OS agnostic
- Agent-based discovery
    - system configuration, system performance, running processes, and details of the network connections between systems
    - Supports Microsoft Server, Amazon Linux, Ubuntu, RedHat, CentOS, SUSE...
- Resulting data can be exported as CSV or viewed within AWS Migration Hub
- Data can be explorer using pre-defined queries in Amazon Athena

### AWS Application Discovery Service - Migration Hub Data Exploration

- Allows you to use Amazon Athena to analyze data collected from on-premises servers during discovery
- Data is automatically stored in S3 bucket at regular intervals
- Use Pre-defined or custom queries in Amazon Athena to analyze data
- Example: type of processes running on each server
- Ability to upload additional data sources such as Configuration Management Database (CMDB) exports
- Integrate Athena with QuickSight to visualize data

### AWS Application Migration Service (MGN)

- The "AWS evolution" of CloudEndure Migration, replacing AWS Server Migration Service (SMS)
- Lift-and-shift (rehost) solution, which simplify migrating appl ications to AWS
- Converts your physical, virtual, and cloud-based servers to run natively on AWS
- Supports a wide range of platforms, Opearting Systems, and databases
- Minimal downtime, reduced costs
- Corporate Data Center / Any cloud -> Disks -> AWS Replication Agent -> continuous replication -> AWS Cloud -> Staging (Low-cost EC2 instances & EBS volumes) -> cutover -> Production (Target EC2 instances & EBS volumes)

### AWS Elastic Disaster Recovery (DRS)

- Used to be named "CloudEndure Disaster Recovery"
- Quickly and easily recover your physical, virtual, and cloud-based servers into AWS
- Example: protect your most critical databases (including Oracle, MySQL, and SQL Server), enterprise apps (SAP), protect your data from ransomware attacks
- Continuous block-level replication for your servers

### On-premises strategy with AWS

- Ability to download Amazon Linux 2 AMI as a VM (.iso format)
    - VMWare, KVM, VirtualBox (Oracle VM), Microsoft Hyper-V
- AWS Application Discovery Service
    - Gather information about your on-premises servers to plan a migration
    - Server utilization and dependency mappings
    - Track with AWS Migration Hub
- AWS Application Migration Service (MGN)
    - Replacing AWS Server Migration Services & CloudEndure Migration
    - Incremental replication of on-premises live servers to AWS
    - Migrates the entire VM into AWS
- AWS Elastic Disaster Recovery (RDS)
    - Replacing CloudEndure Disaster Recovery
    - Recover on-premises workloads onto AWS
- AWS Database Migration Service (DMS)
    - replicate on-premises => AWS, AWS => AWS, AWS => on-premises
    - Works with various database technologies (Oracle, MySQL, DynamoDB, etc..)

## AWS Migration Evaluator

- Helps you build a data-driven business case for migraion to AWS
- Provides a clear baseline of what your organization is running today
- Install Agentless Collector to conduct broad-based discovery
- Take a snapshot of on-premises foot-print, server dependencies
- Analyze current state, define target state, then develop migraion plan
- Install Collector (Download and configure the on-premises Agentless Collector) -> Gather Data (Connect to hypervisors and server to gather inventory, resource utilization) / Import Data (Upload existing data from 3rd party tools or Application Discovery Service) -> AWS migration Evaluator -> Quick Insights (Review Quick Insights report for customized cost insights) -> Business Case (Gain expert guidance to build a business case for migration to AWS)

## AWS Backup

- Fully manage service
- Centrally manage and automate backups across AWS serivces
- No need to create custom scripts and manual processes
- Supported services:
    - Amazon EC2 / Amazon EBS
    - Amazon S3
    - Amazon RDS (all DBs engines) / Amazon Aurora / Amazon DynamoDB
    - Amazon EFS / Amazon FSx (Lustre & Windows File Server)
    - AWS Storage Gateway (Volume Gateway)
- Supports cross-region backups
- Supports cross-account backups
- Supports PITR for supported services
- On-Demand and Scheduled backups
- Tag-based backup policies
- You create backup polices known as Backup Plans
    - Backup frequency (every 12 hours, daily, weekly, monthly, cron expression)
    - Backup window
    - Transition to Cold Storage (Never, Days, Weeks, Months, Years)
    - Retention Period (Always, Days, Weeks, Months, Years)

### AWS Backup Vault Lock

- Enforce a WORM (Write Once Read Many) state for all the backups that you store in your AWS Backup Vault
- Additional layer of defense to protect your backup against
    - Inadvertent or malicious delete operations
    - Updates that shorten or alter retention periods
- Even the root user cannot delete backups when enabled
















































