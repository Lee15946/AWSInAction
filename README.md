# AWS In Action

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

## Databases in AWS

### Choosing the right database

- Questions to choose the right databases based on your architecture
    - Read-heavy, write-heacy, or balanced workload? Throughput needs? Will it change, does it need to scale or
      fluctuate
      during the day?
    - How much data to store and for how long? Will it grow? Average object size? How are they accessed?
    - Data durability? Source of truth for the data?
    - Latency requirements? Concurrent users?
    - Data model? How will you query the data? joins? Structured? Semi-Structured?
    - Strong schema? More flexibility? Reporting? Search? RDBMS/NoSQL?
    - License costs? Switch to Cloud Native DB such as Aurora?
- Database Types
    - RDMBS (=SQL/OLTP): RDS, Aurora - great for joins
    - NoSQL database - no joins, no SQL: DynamoDB(~JSON), ElastiCache(key/value pairs), Neptune(graphs), DocumentDB(for
      MongoDB), Keyspaces (for Apache Cassandra)
    - Object Store: S3 (for big objects) / Glacier (for backups/archives)
    - Data Warehouse (=SQL Analytics / BI): Redshift(OLAP), Athena, EMR
    - Search: OpenSearch(JSON) - free text, unstructured searches
    - Graphs: Amazon Neptune - displays relationships between data
    - Ledger: Amazon Quantum Ledger Database (QLDB)
    - Time series: Amazon Timestream

### Amazon RDS - Summary

- Managed PostgreSQL/MySQL/Oracle/SQL Server/MariaDB/Custom
- Provisioned RDS Instance Size and EBS Volume Type&Size
- Auto-scaling capability for Storage
- Support for Read Replicas and Multi AZ
- Security though IAM, Security Groups, KMS, SSL in transit
- Automated Backup with Point in time restore feature (up to 35 days)
- Manual DB Snapshot for longer-term recovery
- Manaed and scheduled maintenance (with downtime)
- Support for IAM Authentication, integration with Secrets Manager
- RDS Custom for access to and customize the underlying instance (Oracle&SQL Server)
- Use case: Store relational datasets(RDBMS/OLTP), perform SQL queries, transactions

### Amazon Aurora - Summary

- Compatible API for PostgreSQL/MySQL, separation of storage and compute
- Storage: data is stored in 6 replicas, across 3 AZ - highly available, self-healing, auto-scaling
- Compute: Cluster of DB instance across multiple AZ, auto-scaling of Read Replicas
- Cluster: Custom endpoints for writer and reader DB instances
- Same security/monitoring/maintenance features as RDS
- Know the backup&restore options for Aurora
- Aurora Serverless - for unpredictable/intermittent workloads, no capacity planning
- Aurora Multi-Maser - for continuous writes failover (high write availability)
- Aurora Global: up to 16 DB Read Instances in each region, < 1 second storage replication
- Aurora Machine Learning: perform ML using SageMaker&Comprehend on Aurora
- Aurora Database Cloning: new cluster from existing one, faster than restoring a snapshot
- Use case: same as RDS, but with less maintenance/more flexibility/more performance/more features

### Amazon ElastiCache - Summary

- Managed Redis/Memcached(similar offering as RDS, but for caches)
- In-memory data store, sub-millisecond latency
- Select and ElastiCache instance type (e.g, cache.m6g.large)
- Support for Clustering(Redis) and Multi AZ, Read Replicas (sharding)
- Security through IAM, Security Groups. KMS, Redis Auth
- Backup/Snapshot/Point in time restore feature
- Managed and Scheduled maintenance
- Requires some application code changes to be leveraged
- Use case: Key/Value store, Frequent read, less writes, cached result for DB queries, store session data for websites,
  can not use SQL

### Amazon DynamoDB - Summary

- AWS proprietary technology, managed serverless NoSQL database, millisecond latency
- Capacity modes: provisioned capacity with optional auto-scaling or on-demand capacity
- Cab replace ElastiCache as a key/value store (storing session data for example, using TTL feature)
- High available, MultiAZ by default, Read and Writes are decoupled , transaction capability
- DAX cluster for read cache, microsecond read latency
- Security, authentication and authorization is done through IAM
- Event Processing: DynamoDB Streams to integrate with AWS Lambda, or Kinesis Data Streams
- Global Table feature: active-active setup
- Automated backups up to 35 days with PITR (restore to new table), or on-demand backups
- Export to S3 without using RCU within the PITR window, import from S3 withour using WCU
- Great to rapidly evolve schemas
- Use case: Serverless applications development (small documents 100s KB), distributed serverless cache

### Amazon S3 - Summary

- S3 is a key/value store for objects
- Great for bigger objects, not so great for many small objects
- Serverless, scales infinitely, max object size is 5TB, versioning capability
- Tiers: S3 Standard, S3 Infrequent Access, S3 Intelligent, S3 Glacier + lifecycle policy
- Features: Versioning, Encryption, Replication, MFA-Delete, Access Logs...
- Security: IAM, Bucket Policies, ACL, Access Points, Object Lambda, CORS, Object/Vault Lock
- Encryption: SSE-S3, SSE-KMS, SSE-C, client-side, TLS in transit, default encryption
- Batch operations: on object using S3 Batch, listing files using S3 Inventory
- Performance: Multi-part upload, S3 Transfer Acceleration, S3 Select
- Automation: S3 Event Notifications (SNS, SQS, Lambda, EventBridge)
- Use case: static files, key value store for big files, website hosting

### DocumentDB

- Aurora is an "AWS-implementation" of PostgreSQL/MySQL
- DocumentDB is the same for MongoDB (which is a NoSQL database)
- MongoDB is used to store, query, and index JSON data
- Similar deployment concepts as Aurora
- Fully Managed, highly available with replication across 3 AZ
- DocumentDB storage automatically grows in increments of 10GB. up to 64TB
- Automatically scales to workloads with million of requests per seconds

### Amazon Neptune

- Fully managed graph database
- A popular graph dataset would be social network
- Highly available across 3 AZ, with up to 15 read replicas
- Build and run applications working with highly connected datasets - optimized for these complex and hard queries
- Can store up to billions of relations and query the graph with milliseconds latency
- Highly available with replications across multiple AZs
- Great for knowledge graphs, fraud detection, recommendation engines, social networking

### Amazon Keyspaces (for Apache Cassandra)

- Apache Cassandra is an open-source NoSQL distributed database
- A managed Apache Cassandra-compatible database service
- Serverless, Scalable, highly available, fully managed by AWS
- Automatically scales tables up/down based on the application's traffic
- Tables are replicated 3 times across multiple AZ
- Using the Cassandra Query Language (CQL)
- Single-digit millisecond latency at any scale, 1000s of requests per second
- Capacity: On-demand mode or provisioned mode with auto-scaling
- Encryption, backup, Point-In-Time Recovery (PITR) up to 35 days
- Use case: store IoT devices info, time-series data...

### Amazon QLDB

- QLDB stands for "Quantum Ledger Database"
- A ledger is a book recording financial transactions
- Fully Managed, Serverless, High available, Replication across 3 AZ
- Used to view history of all the changes made to your application data over time
- Immutable system: no entry can be removed or modified, cryptographically verifiable
- 2-3x better performance than common ledger blockchain frameworks, manipulate data using SQL
- Difference with Amazon Managed Blockchain: no decentralization component, in accordance with financial regulation
  rules

### Amazon Timestream

- Fully manages, fast, scalable, serverless time series database
- Automatically scales up/down to adjust capacity
- Store and analyze trillions of events per day
- 1000s times faster & 1/10th the cost of relational databases
- Scheduled queries. multi-measure records, SQL compatibility
- Data storage tiering: recent data kept in memory and historical data kept in a cost-optimized storage
- Built-in time series analytics functions (helps you identify patterns in your data in near real-time)
- Encryption in transit and at rest
- Use case: IoT apps, operational applications, real-time analytics

## Data & Analytics

### Amazon Athena

- Serverless query service to analyze data stored in Amazon S3
- Uses standard SQL language to query the files (built in Presto)
- Supports CSV, JSON, ORC, Avro and Parquet
- Pricing: $5.00 perTB of data scanned
- Commonly used with Amazon QuickSight for reporting/dashboards
- Use cases: Business intelligence / analytics / reporting, analyze & query VPC Flow Logs, ELB Logs, CloudTrail trails,
  etc...
- Exam Tip: analyze data in S3 using serverless SQL, use Athena

### Amazon Athena - Performance Improvement

- Use columnar data for cost-savings (less scan)
    - Apache Parquet or ORC is recommended
    - Huge performance improvement
    - Use Glue to convert your data to Parquet or ORC
- Compress data for smaller retrievals (bzip2, gzip, lz4, snappy, zlip, zstd...)
- Partition datasets in S3 for easy querying on virtual columns
- Use larger files (> 128MB) to minimize overhead

### Amazon Athena - Federated Query

- Allows you to run SQL queries across data stored in relational, non-relational, object and custom data sources (AWS or
  on-premises)
- Use Data Source Connectors that run on AWS Lambda to run Federated Queries(e.g, CloudWatch Logs, DynamoDB, RDS...)
- Store the result back in Amazon S3

### Amazon Redshift

- Redshift is based on PostgreSQL, but it's not used for OLTP
- It's OLAP - online analytical processing (analytics and data warehousing)
- 10x better performance than other data warehouses, scale to PBs of data
- Columnar storage of data (instead of row based) & parallel query enggine
- Pay as you go based on the instances provisioned
- Has a SQL interface for performing the queries
- BI tools such as Amazon QuickSight or Tableau integrate with it
- vs Athena: faster queries / joins / aggregations thanks to indexes
- Redshift Cluster
    - Leader node: for query planning, results aggregation
    - Compute node: for performing the queries, send results to leader
    - You provision the node size in advance
    - You can use Reserved Instances for cost savings
- Redshift - Snapshots & DR
    - Redshift has "Multi-AZ" mode for some clusters
    - Snapshots are point-in-time backups of a cluster, stored internally in S3
    - Snapshots are incremental (only what has changed is saved)
    - You can restore a snapshot into a new cluster
    - Automated: every 8 hours, every 5GB, or on a schedule, Set retention
    - Manual: snapshot is retained until you delete it
    - You can configure Amazon Redshift to automaticalyy copy snapshots (automated or manual) of a cluster or another
      AWS Region
- Loading data into Redshift: Large inserts are MUCH better
    - Amazon Kinesis Data Firehose - through S3 copy
    - S3 using COPY command
        - Internet - Without Enhanced VPC routing
        - Through VPC - With Enhanced VPC Routing
    - EC2 Instance - JDBC driver
        - Better to write Data in batches
- Redshift Spectrum
    - Query data that is already in S3 without loading it
    - Must have a Redshift cluster available to start the query
    - The query is then submitted to thousands of Redshift Spectrum nodes

### Amazon OpenSearch

- Amazon OpenSearch is successor to Amazon ElastiSearch
- In DynamoDB, queries only exist by primary key or indexes
- With OpenSearch, you can search any field, even partially matches
- It's common to use OpenSearch as a complement to another database
- Two modes: managed cluster or serverless cluster
- Does not natively support SQL (can be enabled via a plugin)
- Ingestion from Kinesis Data Firehose, AWS IoT, and CloudWatch Logs
- Security through Cognito & IAM, KMS, encryption, TLS
- Comes with OpenSearch Dashboards (visualization)
- OpenSearch patterns
    - DynamoDB
        - CRUD -> DynamoDB Table -> DynamoDB Stream -> Lambda Function -> Amazon OpenSearch -> EC2 (API to search items)
    - CloudWatch Logs
        - CloudWatch Logs -> Subscription Filter -> Lambda Function (managed by AWS) -> (Realtime) Amazon OpenSearch
        - CloudWatch Logs -> Subscription Filter -> Kinesis Data Firehose -> (Near Real Time) Amazon OpenSearch
    - Kinesis Data Streams & Kinesis Data Firehose
        - Kinesis Data Streams -> Kinesis Data Firehose (near real time) -> data transformation (Lambda) -> Amazon
          OpenSearch
        - Kinesis Data Streams -> Lambda Function (real time) -> Amazon OpenSearch

### Amazon EMR

- EMR stands for "Elastic MapReduce"
- EMR helps creating Hadoop clusters (Big Data) to analyze and process vast amount of data
- The clusters can be made of hundreds of EC2 instances
- EMR comes bundled with Apache Spark, HBase, Presto, Flink...
- EMR takes care of all the provisioning and configuration
- Auto-scaling and integrated with Spot instances
- Use cases: data processing, machine learning, web indexing, big data...
- Amazon EMR - Node types & purchasing
    - Master Node: Manage the cluster, coordinate, manage health - long running
    - Core Node: Run tasks and store data - long running
    - Task Node(optional): Just to run tasks - usually Spot
    - Purchasing options:
        - On-demand: reliable, predictable, won't be terminated
        - Reserved (min 1 year): cost savings (EMR will automatically use if available)
        - Spot instances: cheaper, can be terminated, less reliable
    - Can have long-running cluster, or transient (temporary) cluster

### Amazon QuickSight

- Serverless machine learning-powered business intelligence service to create interactive dashboards
- Fast, automatically scalable, embeddable, with per-session pricing
- Use cases:
    - Business analytics
    - Building visualizations
    - Perform ad-hoc analytics
    - Get business insights using data
- Integrated with RDS, Aurora, Athena, Redshift, S3...
- In-memory computation using SPICE engine if data is imported into QuickSight
- Enterprise edition: possibility to setup Column-Level security (CLS)
- QuickSight Integrations
    - Data sources(AWS Services):
        - RDS
        - Aurora
        - Redshift
        - Athena
        - S3
        - OpenSearch
        - Timestream
    - Data Sources(SaaS)
        - Salesforce
        - JIRA
    - Teradata/On-premises Databases(JDBC)
    - Data sources(Imports)
        - XLSX
        - CSV
        - JSON
        - TSV
        - ELF&CLF(Long format)
- QuickSight - Dashboard & Analysis
    - Define Users(standard versions) and Groups(enterprise version)
        - These users&groups only exist within QuickSight, not IAM!!
    - A dashboard...
        - is a read-only snapshot of an analysis that you can share
        - preserves the configuration of the analysis (filtering, parameters, controls, sort)
    - You can share the analysis or the dashboard with Users or Groups
    - To share a dashboard, you mush first publish it
    - Users who see the dashboard can also see the underlying data

### AWS Glue

- Managed extract, transform, and load (ETL) service
- Useful to prepare and transform data for analytics
- Fully serverless service
- AWS Glue - Convert data into Parquet format
- Glue Data Catalog: catalog of datasets
    - Use AWS Glue Data Crawler to get data from Amazon S3/RDS/DynamoDB, JDBC to write metadata to AWS Glue Data Catalog
    - It can be used as Data discovery to Amazon Athena, Amazon Redshift Spectrum, Amazon EMR
- Glue - things to know at high-level
    - Glue Job Bookmarks: prevent re-processing old data
    - Glue Elastic Views:
        - Combine and replicate data across multiple data store using SQL
        - No custom code, Glue monitors for changes in the source data, serverless
        - Leverages a "virtual table"(materialized view)
    - Glue DataBrew: clean and normalize data using pre-built transformation
    - Glue Studio: new GUI to create. run and monitor ETL jobs in Glue
    - Glue Streaming ETL (built on Apache Spark Structured Streaming): compatible with Kinesis Data Streaming, Kafka,
      MSK(managed Kafka)

### AWS Lake Formation

- Data lake = central place to have all your data for analytics purposes
- Fully managed service that makes it easy to setup a data lake in days
- Discover, cleanse, transform, and ingest data into your Data Lake
- It automates many complex steps (collecting, cleansing, moving cataloging data,...) and de-duplicate (using ML
  Transforms)
- Combine structured and unstructured data in the data lake
- Out-of-the-box source blueprints: S3, RDS, Relational & NoSQL DB...
- Fine-grained Access Control for your applications (row and column-level)
- Built on top of AWS Glue
- AWS Lake Formation
    - Data sources: Amazon S3, RDS, Aurora, On-premises Database (SQL&NoSQL)
    - Ingest to Data Lake stores in S3
    - Can have Source Crawlers, ETL and Data Pre, Data Catalog, Security Settings, Access Control
    - Can send data to Athena, Redshift, EMR, Spark and to users
- AWS Lake Formation Centralized Permissions Example
    - Access Control Column-level security across all services connected to AWS Lake Formation

### Amazon MSK

- Amazon Managed Streaming for Apache Kafka (Amazon MSK)
- Alternative to Amazon Kinesis
- Fully managed to Apache Kafka on AWS
    - Allow you to create, update, delete clusters
    - MSK creates & manages Kafka brokers nodes & Zookeeper nodes for you
    - Deploy the MSK cluster in your VPC, multi-AZ (up to 3 for HA)
    - Automatic recovery from common Apache Kafka failures
    - Data is stored on EBS volumes for as long as you want
- MSK Serverless
    - Run Apache Kafka on MSK without managing the capacity
    - MSK automatically provisions resources and scales compute & storage
- Kinesis Data Streams vs Amazon MSK
    - Kinesis Data Streams
        - 1MB message size limit
        - Data Streams with Shards
        - Shard Splitting & Merging
        - TlS In-flight encryption
        - KMS at-rest encryption
    - Amazon MSK
        - 1MB by default, configure for higher (ex: 10MB)
        - Kafka Topics with Partitions
        - Can only add partitions to a topic
        - PLAINTEXT on TLS in-flight EEncryption
        - KMS at-rest encryption
- Amazon MSK Consumers
    - Kinesis Data Analytics for Apache Flink
    - AWS Glue Streaming ETL Jobs Powered by Apache Spark Streaming
    - Lambda
    - Applications running on Amazon EC2/ECS/EKS

### Big Data Ingestion Pipeline

- IoT Core allows you to harvest data from IoT devices
- Kinesis is great for real-time data collection
- Firehose helps with data delivery to S3 in near real-time (1 minute)
- Lambda can help Firehose with data transformations
- Amazon S3 can trigger notifications to SQS
- Lambda can subscribe to SQS (we could have connector S3 to Lambda)
- Athena is a serverless SQL service and results are stored in S3
- The reporting bucket contains analyzed and can be used by reporting tool such as AWS QuickSight, Redshift etc...

## Machine Learning

### Amazon Rekognition

- Find objects. people, text, scenes in images and videos using ML
- Facial analysis and facial search to do user verification, people counting
- Create a database of "familar" faces or compare against celebrities
- Use cases
    - Labeling
    - Content Moderation
    - Text Detection
    - Face Detection and Analysis (gender, age range, emotions...)
    - Face Search and Verification
    - Celebrity Recognition
    - Pathing (ex: for sports game analysis)
- Amazon Rekognition - Content Moderation
    - Detect content that is inappropriate, unwanted or offensive (images and videos)
    - Used in social media, broadcast media, advertising, and e-commerce situations to create a safer user experience
    - Set a Minimum Confidence Threshold for items that will be flagged
    - Flag sentitive content for manual review in Amazon Augmented AI (A2I)
    - Help comply with regulations

### Amazon Transcribe

- Automaticvally convert speech to text
- Uses a deep learning process called automatic speech recognition (ASR) to convert speech to text quickly and
  accurately
- Automatically remove Personal Identifiable Information(PII using Redaction
- Supports Automatic Language Identification for multi-lingual audio
- Use cases:
    - transcribe customer service calls
    - automate closed captioning and subtitling
    - generate metadata for media assets to create a fully searchable archive
- Automaticvally convert speech to text
- Uses a deep learning process called automatic speech recognition (ASR) to convert speech to text quickly and
  accurately
- Automatically remove Personal Identifiable Information(PII using Redaction
- Supports Automatic Language Identification for multi-lingual audio
- Use cases:
    - transcribe customer service calls
    - automate closed captioning and subtitling
    - generate metadata for media assets to create a fully searchable archive

### Amazon Polly

- Turn text into lifelike speech using deep learning
- Allowing you to create applications that talk
- Amazon Polly - Lexicon & SSML
    - Customize the pronunciation of words with Pronunciation lexicons
        - Stylized words: St3ph4ne => "Stephane"
        - Acronyms : AWS => "Amazon Web Services"
    - Upload the lexicons and use them in the SynthesizeSpeech operation
- Generate speech from plain text or form documents marked up with Speech Synthesis Markup Language (SSML) - enables
  more customization
    - emphasizing specific words on phrases
    - using phonetic pronunciation
    - including breathing sounds, whispering
    - using the Newscaster-speaking style

### Amazon Translate

- Natural and accurate language translation
- Amazon Translate allows you to localize content - such as websites and applications - for international users, and to
  easily translate large volumes of text efficiently

### Amazon Lex & Connect

- Amazon Lex (same technology that powers Alexa)
    - Automatic Speech Recognition (ASR) to convert speech to text
    - Natural Language Understanding to recoginze the intent of text, callers
    - Help build chatbots, call center bots
- Amazon Connect
    - Receive calls, create contact flows, cloud-based virtual contact center
    - Can integrate with other CRM systems or AWS
    - No upfront payments, 80% cheaper than traditional contact center solutions

### Amazon Comprehend

- For Natural Language Processing - NLP
- Fully managed and serverless service
- Uses machine learning to find insights and relationships in text
    - Language of the text
    - Extracts key phrases, places, people, brands or events
    - Understands how positive or negatice the text is
    - Analyzes text using tokenization and parts of speech
    - Automatically organizes a collection of text files by topic
- Sample use cases:
    - analyze customer interactions (emails) to find what leads to a positive or negative experience
    - Create and gropus articles by topics that Comprehend will uncover

### Amazon Comprehend Medical

- Amazon Comprehend Medical detects and returns useful information in unstructured clinical text:
    - Physician's notes
    - Discharge summaries
    - Test results
    - Case notes
- Uses NLP to detect Protected Health Information (PHI) - Detect PHI API
- Store your documents in Amazon S3, analyze real-time data with Kinesis Data Firehose, or use Amazon Transcribe to
  transcribe patient narratives into text that can be analyzed by Amazon Comprehend Medical

### Amazon SageMaker

- Fully managed service for developers/data scientists to build ML models
- Typically difficult to do all the processes in one place + provision servers

### Amazon Forecast

- Fully managed service that using ML to deliver highly accurate forecasts
- Example: predict the future sales of a raincoat
- 50% more accurate than looking at the data itself
- Reduce forecasting from months to hours
- Use cases: Product Demand Planning, Financial Planning, Resource Planning...

### Amazon Kendra

- Fully managed document search service powered by Machine Learning
- Extract answers from within a document (text, pdf, HTML, PowerPoint, MS word, FAQs)
- Natural language search capabilities
- Learn from user interactions/feedback to promote preferred results (Incremental Learning)
- Ability to manually fine-tune search results (importance of data, freshness, custom)

### Amazon Personalize

- Fully managed ML-service to build apps with real-time personalized recommendations
- Example: personalized product recommendations/re-ranking, customized direct marketing
- Same technology used by Amazon.com
- Integrates into existing websites, applications, SMS, email, marketing systems...
- Implement in days, not months (you don't need to build, train, and deploy ML solutions)
- Use cases: retail stores, media and entertainment

### Amazon Textract

- Automatically extracts text, handwriting and data form any scanned documents using AI and ML
- Extract data from forms and tables
- Read and process any type of documents (PDFs, images)
- Use cases:
    - Financial Services (e.g, invoices, financial reports)
    - Healthcare (e.g, medical records, insurance claims)
    - Public Sector (e.g, tax forms, ID documents, passports)

### AWS Machine Learning - Summary

- Rekognition: face detection, labeling, celebrity recognition
- Transcribe: audio to text
- Polly: text to audio
- Translate: translations
- Lex: build conversational bots - chatbots
- Connect: cloud contact center
- Comprehend: natural language processing
- SageMaker: machine learning for every developer and data scientist
- Forecast: build highly accurate forecasts
- Kendra: ML-powered search engine
- Personalize: real-time personalized recommendations

## Identity and Access Management (IAM) - Advanced

### AWS Organizations

- Global service
- Allows to manage multiple AWS accounts
- The main account is the management account
- Other accounts are member accounts
- Memeber accounts can only be part of one organization
- Consolidated Billing across all accounts - songle payment method
- Pricing benefits from aggregated usage (volume discount for EC2, S3)
- Shared reserved instances and Saving Plans discounts across accounts
- API is available to automate AWS account creation
- Organizational Units (OU)
- Advantages
    - Multi Account vs One Account MultiVPC
    - Use tagging standard for billing purposes
    - Enable CloudTrail on all accounts, send logs to central S3 account
    - Send CloudWatch Logs to central logging account
    - Establish Cross Account Roles for Admin purposes
- Security: Service Control Policies (SCP)
    - IAM policies applied to OU or Accounts to restrict Users and Roles
    - They do not apply to the management account (full admin power)
    - Must have an explicit allow (does not allow anything by default - like IAM)
    - SCP Examples: Blocklist and Allowlist strategies

### IAM - Advanced Policies

- IAM Conditions
    - aws:SourceIp: restrict the client IP from which API calls are being made
    - aws:RequestedRegion: restrict the region the API calls are made to
    - ec2: ResourceTage: restrict based on tags
    - aws: MultiFactorAuthPresent to force MFA
- IAM for S3
    - s3:ListBucket permission applies to arn:aws:s3::test => bucket level permission
    - s3:GetObject,s3::PutObject,s3::DeleteObject applies to arn:aws:s3::test/* => object level permission
- Resource Policies & aws:PrincipalOrgID
    - aws:PrincipalOrgID can be used in any resource policies to restrict access to accounts that are member of an AWS
      Organization

### IAM - Resource-based Policies vs IAM Roles

- Cross account:
    - attaching a resource-based policy to a resource (example: S3 bucket policy)
    - OR using a role as a proxy
- When you assume a role(user,application or service), you give up your original permissions and take the permissions
  assigned to the role
- When using a resource-based policy, then principal doesn't have to give up his permissions
- Example: User in account A needs to scan a DynamoDB table in Account A and dump it in an S3 bucket in account B
- Supported by: Amazon S3 buckets, SNS topics, SQS queues, etc...
- Amazon EventBridge - Security
    - When a rule runs, it needs permissions on the target
    - Resource-base policy: Lambda, SQS,SNS, CloudWatch Logs, API Gateway
    - IAM role: Kinesis stream, Systems Manager Run Command, ECS task

### IAM - Policy Evaluation Logic

- IAM Permission Boundaries
    - IAM Permission Boundaries are supported for users and roles (not groups)
    - Advanced feature to use a managed policy to set the maximum permissions an IAM entity can get
    - Can be used in combinations of AWS Organizations SCP
- IAM Policy Evaluation Logic
    - Deny -> Organizations SCPs -> Resource-based policies -> Identity-based policies -> IAM permissions boundaries ->
      Session policies

### AWS IAM Identity Center (successor to AWS Single Sing-On)

- One login (sing sing-on) for all your
    - AWS accounts in AWS Organizations
    - Business cloud applications (e.gg, Salesforce, Box, Microsoft 365)
    - SAML2.0-enabled applications
    - EC2 Windows Instances
- Identity providers
    - Built-in identity store in IAM Identity Center
    - 3rd party: Active Directory(AD), OneLoin, Okta
- Fine-grained Permissions and Assignments
    - Multi-Account Permissions
        - Manage access across AWS acoounts in your AWS Organization
        - Permission Sets - a collection of one or more IAM policies assigned to users and groups to define AWS access
    - Application Assignments
        - SSO access to many SAML 2.0 business applications
        - Provide required URLs, certificates, and metadata
    - Attribute-Based Access Control (ABAC)
        - Fine-grained permissions based on users' attributes stored in IAM Identity Center Store
        - Example: cost center, title, locale
        - Use case: Define permission once, then modify AWS access by changing the attributes

### AWS Directory Services

- What is Microsoft Active Directory (AD)?
    - Found on any Windows Server with AD Domain Services
    - Database of objects: User Accounts, Computers, Printers, File Shares, Security Groups
    - Centralized security managements, create account, assign permissions
    - Objects are organized in trees
    - A group of tress is a forest
- AWS Directory Services
    - AWS Managed Microsoft AD
        - Create your own AD in AWS, manage users locally, supports MFA
        - Establish "trust" connections with your on-premise AD
    - AD Connector
        - Directory, Gateway (proxy) to redirect to on-premise AD, supports MFA
        - Users are managed on the on-premise AD
    - Simple AD
        - AD-compatible managed directory on AWS
        - Cannot be joined with on-premise AD
- IAM Identity Center - Active Directory Setup
    - Connect to an AWS Managed Microsoft AD (Directory Service)
        - Integration is out of the box
    - Connect to a Self-Managed Directory
        - Create Two-way Trust Relationship using AWS Managed Microsoft AD
        - Create an AD Connector

### AWS Control Tower

- Easy way to set up and govern a secure and compliant multi-account AWS environment based on best practices
- AWS Control Tower uses AWS Organizations to create accounts
- Benefits:
    - Automate the set up of your environment in a few clicks
    - Automate ongoing policy managements using guardrails
    - Detect policy violations and remediate them
    - Monitor compliance through an interactive dashboard
- AWS Control Tower - Guardrails
    - Provides ongoing governance for your Control Tower environment (AWS accounts)
    - Preventive Guardrail - using SCPs (e.g, Restrict Regions across all your accounts)
    - Detective Guardrail - using AWS config (e.g, identify untagged resources)

## AWS Security & Encryption

### Encryption 101

- Encryption in flight (SSL)
    - Data is encrypted before sending and decrypted after receiving
    - SSL certificates help with encryption (HTTPS)
    - Encryption in flight ensures no MITM(man in the middle attack) can happen
- Server side encryption at rest
    - Data is encrypted after being received by the server
    - Data is decrypted before being sent
    - It is stored in an encrypted from thanks to a key (usuallyt a data key)
    - The encryption/decryption keys must be managed somewhere and the server must have access to it
- Client side encryption
    - Data is encrypted by the client and never decrypted by the server
    - Data will be decrypted by a receiving client
    - The server should not be able to decrypt the data
    - Could leverage Envelope Encryption

### AWS KMS

- Anytime you hear "encryption" for an AWS service, it's most likely KMS
- AWS manages encryption keys for us
- Fully integrated with IAM for authorization
- Easy way to control access to your data
- Able to audit KMS Key usage using CloudTrail
- Seamlessly integrated into most AWS services (EBS,S3,RDS,SSM)
- Never ever store your secrets in plaintext, especailly in your code!
    - KMS Key Encryption is also available through API calls(SDK,CLI)
    - Encrypted secrets can be stored in the code/environment variables
- KMS Keys Types
    - KMS Keys is the new name of KMS Customer Master Key
    - Symmetric (AES-256 keys)
        - Single encryption key that is used to Encrypt and Decrypt
        - AWS services that are integrated with KMS use Symmetric CMKs
        - You never get access to the KMS Key unencrypted (must call KMS API to use)
    - Asymmetric (RSA & ECC key paris)
        - Public (Encrypt) and Private key (Decrypt) pair
        - Used for Encrypt/Decrypt, or Sign/Verify operations
        - The public key is downloadable, but you can't access the private key unencrypted
        - Use case: encryption outside of AWS by users who can't call the KMS API
- AWS KMS (Key Management Service)
    - Types of KMS Keys:
        - AWS Owned Keys(free): SSE-S3, SS3-SQS, SSE_DDB(default key)
        - AWS Managed Key: free (aws/service-name, example: aws/rds or aws/ebs)
        - Customer managed keys created in KMS: $1/month
        - Customer managed keys imported (must be symmetric key): $1/month
        - pay for API calls to KMS ($0.03/10000 calls)
    - Automatic Key rotation:
        - AWS-managed KMS Key: automatic every 1 year
        - Customer-managed KMS Key: (must be enabled) automatic every 1 year
        - Imported KMS Key: only manual rotation possible using alias
- Copying Snapshots across regions
    - EBS volume encrypted with KMS Key A
    - EBS Snapshot encrypted with KMS Key A
    - KMS ReEncrypt with KMS Key B
    - EBS Snapshot Encrypted with KMS Key B
    - EBS Volume Encrypted with KMS Key B
- KMS Key Policies
    - Control access to KMS keys, "similar" to S3 bucket policies
    - Difference: you can not control access without them
    - Default KMS Key Policy
        - Created if you don't provide a specific KMS Key Policy
        - Complete access to the key to the root user = entire AWS account
    - Custom KMS Key Policy
        - Define users, roles that can access the KMS key
        - Define who can administer the key
        - Useful for cross-account access of your KMS key
- Copying Snapshots across accounts
    - Create a Snapshot encrypted with your own KMS Key (customer managed key)
    - Attach a KMS key policy to authorize cross-account access
    - Share the encrypted snapshot
    - (in target) Create a copy of the snapshot, encrypt in with a CMK in your account
    - Create a volume from the snapshot

### KMS - Multi-Region Keys

- Identical KMS keys in different AWS Regions that can be used interchangeably
- Multi-Region keys have the same key ID, key material, automatic rotation
- Encrypt in one Region and decrypt in other Regions
- No need to re-encrypt or making cross-Region API calls
- KMS Multi-Region are NOT global (Primary + Replicas)
- Each Multi-Region key is managed independently
- Use cases: global client-side encryption, encryption on Global DynamoDB, Global Aurora
- DynamoDB Global Tables and KMS Multi-Region Keys Client-Side encryption
    - Encrypt speific attributes client-side in our DynamoDB table using the Amazon DynamoDB encryption Client
    - Combined with GlobalTables, the client-side encrypted data is replicated to other regions
    - If we use a multi-region key, replicated in the same region as the DynamoDB Global table, then clients in these
      regions can use low-latency API calss to KMS in their region to decrypt the data client-side
    - Guarantee only decrytpion if the client has access to an API key
- Global Aurora and KMS Multi-Region Keys Client-Side encryption
    - Similar with above
    - Protect specific fields even from database admins

### S3 Replication with Encryption

- Unencrypted objects and objects encrypted with SSE-S3 are replicated by default
- Objects encrypted with SSE-C (customer provided key) are never replicated
- For objects encrypted with SSE-KMS, you need to enable the option
    - Specify which KMS key to encrpyt the objects within the target bucket
    - Adopt the KMS Key Policy for the target key
    - An IAM Role with kms:Decrypt for the source KMS Key, and kms:Encrypt for the target KMS Key
    - You might get KMS throttling errors, in which case you can ask a Service Quotas increase
- You can use multi-region AWS KMS Keys, but they are currently treated as independent keys by Amazon S3 (the object
  will still be decrypted and then encrypted)

### AMI Sharing Process Encrypted via KMS

- AMI in Source Account is encrypted with KMS Key from Source Account
- Must modify the image attribute to add a Launch Permission which corresponds to the specified target AWS account
- Must share the KMS Keys used to encrypted the snapshot the AMI references with the target account / IAM Role
- The IAM Role/User in the target account must have the permission to DescribeKey, ReEncrypted, CreateGrant, Decrypt
- When launching an EC2 instance from the AMI, optionally the target account can specify a new KMS key in its own
  account to re-encrypt the volumes

### SSM Parameter Store

- Secure storage for configuration and secrets
- Optional Seamless Encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations/secrets
- Security through IAM
- Notifications with Amazon EventBridge
- Integration with CloudFormation
- SSM Parameter Store Hierarchy
    - /department
        - my-app/
            - dev/
                - db-url
                - dd-password
    - /aws/reference/secretsmanager/secret_ID_in_Secrets_Manager
    - /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 (public)
- Standard and advanced parameter tiers
    - Parameter Policies (for advanced parameters)
        - Allow to assign a TTL to a parameter (expiration data) to force updating or deleting sentitive data such as
          passwords
        - Can assign multiple policies at a time
        - Expiration (to delete a parameter)
        - ExpirationNotification (EventBridge)
        - NoChangeNotification (EventBridge)

### AWS Secrets Manager

- Newer service meant for strong secrets
- Capability to force rotation of secrets every X days
- Automate generation of secrets on rotation (uses Lambda)
- Integration with Amazon RDS (MySQL, PostgreSQL, Aurora)
- Secrets are encrypted using KMS
- Mostly meant for RDS integration
- AWS Secrets Manager - Multi-Region Secrets
    - Replicate Secrets across multiple AWS Regions
    - Secrets Manager keeps read replicas in sync with the primary Secret
    - Ability to promote a read replica Secret to a standalone Secret
    - Use cases: multi-region apps, disaster recovery strategies, multi-region DB...

### AWS Certificate Manager (ACM)

- Easily provision, manage, and deploy TLS Certificates
- Provide in-flight encryption for websites (HTTPS)
- Supports both public and private TLS certificates
- Free or charge for public TLS certificates
- Automatic TLS certificate renewal
- Integration with (load TLS certificates on)
    - Elastic Load Balancers (CLB, ALB, NLB)
    - CloudFront Distributions
    - APIs on API gateway
- Can not use ACM with EC2 (can't be extracted)
- ACM -Requesting Public Certificates
    - List domain names to be included in the certificate
        - Fully Qualified Domain Name (FQDN): corp.exmaple.com
        - Wildcard Domain: *.example.cpm
    - Select Validation Method: DNS Validation or Email validation
        - DNS Validation is preferred for automation purposes
        - Email validation will send emails to contact addresses in the WHOIS database
        - DNS Validation will leverage a CNAME record to DNS config (ex: Route53)
    - It will take a few hours to get verified
    - The Public Certificate will be enrolled for automatic renewal
        - ACM automatically renews ACM-generated certificates 60 days before expire
- ACM -Importing Public Certificates
    - Option to generate the certificate outside of ACM and then import it
    - No automatic renewal, must import a new certificate before expiry
    - ACM sends daily expiration events starting 45 days prior to expiration
        - The # of days can be configured
        - Events are appearing in EventBridge
    - AWS Config has a managed rule named acm-certificate-check to check for expiring certificates (configurable number
      of days)
- ACM - Integration with ALB
    - Application Load Balancer (With HTTP->HTTPS redirect rule)
- ACM -Integration wiht API Gateway
    - Create a Custom Domain Name in API Gateway
    - Edge-Optimized (default): For global clients
        - Request are routed through the CloudFront Edge locations (improves latency)
        - The API Gateway still lives in only one region
        - The TLS Certificate must be in the same region as CloudFront, in us-east-1
        - Then setup CNAME or (better) A-Alias record in Route53
    - Regional:
        - For clients within the same region
        - The TLS Certificate must be imported on API Gateway. in the same region as the API stage
        - Then setup CNAME or (better) A-Alias record in Route53

### AWS Web Application Firewall (WAF)

- Protects your web applications from common web exploits (Layer 7)
- Layer 7 is HTTP (vs Layer 4 is TCP/UDP)
- Deploy on
    - Application Load Balancer
    - API Gateway
    - CloudFront
    - AppSync GraphQL API
    - Cognito User Pool
- Define Web ACL (Web Access Control List) Rules:
    - IP Set: up to 10000 IP addresses - use multiple Rules for more IPs
    - HTTP headers, HTTP body, or URI strings Protects from common attach - SQL injection and Cross-Site Scripting (XSS)
    - Size constraints, geo-match(block countries)
    - Rate-based rules (to count occurrences of events) - for DDos protection
- Web ACL are Regional except for CloudFront
- A rule group is a reusable set of rules that you can add to web ACL
- WAF - Fixed IP while using WAG with a Load Balancer
    - WAF does not support the Network Load Balancer (Layer 4)
    - We can use Global Accelerator for fixed IP and WAF on the ALB

### AWS Shield

- Protect form DDos attack
- DDoS: Distributed Denial of Service - many requests at the same time
- AWS Shield Standard:
    - Free service that is activated for every AWS customer
    - Provides protection from attacks such as SYN/UDP Floods, Reflection attacks and other layer 3/layer 4 attacks
- AWS Shield Advanced:
    - Optional DDos mitigation service ($3000 per month per organization)
    - Protect against more sophisticated attach on
        - Amazon EC2
        - Elastic Load Balancing
        - Amazon CloudFront
        - AWS Global Accelerator
        - Route 53
    - 24/7 access to AWS DDos response team (DRP)
    - Protect against higher fees during usage spikes due to DDoS
    - Shield Advanced automatic application layer DDoS mitigation automatically creates, evaluates and deploys AWS WAG
      rules to mitigate layer 7 attacks

### Fire Wall Manager

- Manage rules in all accounts of an AWS Organization
- Security policy: common set of security rules
    - WAF rules (Application Load Balancer, API Gateways, CloudFront)
    - AWS Shield Advanced (ALB, CLB, NLB, Elastic IP, CloudFront)
    - SecurityGroups for EC2, Application Load Balancer and ENI resources in VPC
    - AWS Network Firewall (VPC Level)
    - Amazon Route53 Resolver DNS Firewall
    - Policies are creatred at the region level
- Rules are applied to new resources as they are created (good for compliance) across all and future accounts in your
  Organization
- WAF vs Firewall Manager vs Shield
    - WAF, Shield and Firewall Manager are used together for comprehensive protection
    - Define your Web ACL rules in WAF
    - For granular protection of your resources, WAF alone is the correct choice
    - If you want to use AWS WAF across accounts, accelerate WAF configuration, automate the protection of new
      resources, use Firewall Manager with AWS WAF
    - Shield Advanced adds additional features on top of AWS WAF, such as dedicated support from the Shield Response
      Team (SRT) and advanced reporting
    - If you are prone to frequent DDoS attacks, consider purchasing Shield Advanced

### DDoS Protection Best practices

- Edge Location Mitigation
    - CloudFront
        - Web Application delivery at the edge
        - Protect from DDoS Common Attacks (SYN floods, UDP reflection...)
    - Global Accelerator
        - Access your application from the edge
        - Integration with Shield from DDoS protection
        - Helpful if your backend is not compatible with CloudFront
    - Route53
        - Domain Name Resolution at the edge
        - DDoS protection mechanism
- Best practices for DDoS mitigation
    - Infrastructure layer defense
        - Protect Amazon EC2 against high traffic
        - That includes using Global Accelerator, Route53, CloudFront, Elastic Load Balancing
    - Amazon EC2 with Auto Scaling
        - Help scale in case of sudden traffics surges including a flash crowd or a DDoS attack
    - Elastic Load Balancing
        - Elastic Load Balancing scales with the traffic increases and will distribute the traffics to many EC2
          instances
- Application Layer Defense
    - Detect and filter malicious web requests
        - CloudFront cache static content and serve it from edge locations, protecting your backend
        - AWS WAF is used on top of Balancer to filter and block requests based on request signatures
        - WAF rate-based rules can automatically block the IPs of bad actors
        - Use managed rules on WAF to block attacks based on IP reputation, or block anonymous Ips
        - CloudFront can block specific geographies
    - Shield Advanced
        - Shield Advanced automatic application layer DDoS mitigation automatically creates, evaluates and deploys AWS
          WAF rules to mitigate layer 7 attacks
- Attach surface reduction
    - Obfuscating AWS resources
        - Using CloudFront, API Gateway, Elastic Load Balancing to hide your backend resources (Lambda functions, EC2
          instances)
    - Security groups and Network ACLs
        - USe security groups and NACLs to filter traffic based on specific IP at the subnet ot ENI-level
        - Elastic IP are protected by AWS Shield Advanced
    - Protecting API endpoints
        - Hide EC2, Lambda, elsewhere
        - Edge-optimized mode, or CloudFront + regional mode 9More control for DDoS
        - WAF + API Gateway: burst limits, headers filtering, use API keys

### Amazon GuardDuty

- Intelligent Threat discovery to protect your AWS account
- Use Machine Learning algorithms, anomaly detection, 3rd party data
- One click to enable (30 days trail), no need to install software
- Input data includes:
    - CloudTrail Events Logs - unusual API calls, unauthorized deployments
        - CloudTrail Management Events - create VPC subnet, create trail...
        - CloudTrail S3 Data Events - get object, list objects, delete object...
    - VPC Flow Logs - unusual internal traffic, unusual IP address
    - DNS Logs - Compromised EC2 instances sending encoded data within DNS queries
    - Optional Features - EKS Audit Logs, RDS & Aurora, EBS, Lambda, S3 Data Events...
- Can setup EventBridge rules to be notified in case of findings
- EventBridge rules can target AWS Lambda or SNS
- Can protect against CryptoCurrency attacks (has a dedicated "finding" for it)

### Amazon Inspector

- Automated Security Assessments
- For EC2 instances
    - Leveraging the AWS System Manager (SSM) agent
    - Analyze against unintended network accessibility
    - Analyze the running OS against known vulnerabilities
- For Container Images push to Amazon ECR
    - Assessment of Container Images as they are pushed
- For Lambda Functions
    - Identifies software vulnerabilities in function code and package dependencies
    - Assessment of functions as they are deployed
- Reporting & integration with AWS Security Hub
- Send findings to Amazon Event Bridge
- Only for EC2 instances, Container Images&Lambda functions
- Continuous scanning of the infrasturcture, only when needed
- Package vulnerabilities (EC2, ECR & Lambda) - database of CVE
- Network reachability (EC2)
- A risk score is associated with all vulnerabilities for prioritization

### Amazon Macie

- Fully managed data security and data privacy service that uses machine learning and pattern matching to discover and
  protect your sensitive data in AWS
- Macie helps identify and alert you to sensitive data, such as personal identifiable information (PII)

## Disaster Recovery & Migrations

### Disaster Recovery Overview

- Any event that has a negative impact on a company's business continuity of finances is a disaster
- Disaster recovery (DR) is about preparing for and recovering from a disaster
- What kind of disaster recovery?
    - On-premise => On-premiseL traditional DR, and very expensive
    - On-premise => AWS Cloud: hybrid recovery
    - AWS Cloud Region A => AWS Cloud Region B
- Need to define two terms
    - RPO: Recovery Point Objective
    - RTO: Recovery Time Objective
- Disaster Recovery Strategies
    - Backup and Restore
    - Pilot Light
        - A small version of the app is always running in the cloud
        - Useful for the critical core (pilot light)
        - Very similar to Backup and Restore
        - Faster than Backup and Restore as critical systems are already up
    - Warm Standby
        - Fully system is up and running, but a minimum size
        - Upon disaster, we can scale to production load
    - Hot Site / Multi Site Approach
        - Very low RTO (minutes or seconds) - very expensive
        - Full Production Scale is running AWS and On Premise
- Disaster Recover Tips
    - Backup:
        - EBS Snapshots, RDS automated backups/ Snapshots, etc...
        - Regular pushes to S3/S3 IA/Glacier, Lifecycle Policy, Cross Region Replication
        - From On-Premise: Snowball or Storage Gateway
    - High Availability
        - Use Route53 to migrate DNS over from Region to Region
        - RDS Multi-AZ, ElastiCache Multi-AZ, EFS, S3
        - Site to Site VPN as recovery from Direct Connect
    - Replication
        - RDS Replication (Cross Region), AWS Aurora + Global Databases
        - Database replication from on-premise to RDS
        - Storage Gateway
    - Automation
        - CloudFormation/Elastic Beanstalk to re-create a whole new environment
        - Recover/Reboot EC2 instances with CloudWatch if alarms fail
        - AWS Lambda functions for customized automations
    - Chaos
        - Netflix has a "simian-army" randomly terminating EC2

### Database Migration Service (DMS)

- Quickly and securely migrate databases to AWS, resilient, self healing
- The source database remains available during the migration
- Supports
    - Homogeneous migrations: ex Oracle to Oracle
    - Heterogeneous migration: ex Microsoft SQL Server to Aurora
- Continuous Data Replication using Change Data Capture (CDC)
- You must create an EC2 instance to perform the replication tasks
- DMS Sources and Targets
    - Sources
        - On-Premises and Ec2 instances databases: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, MongoDB, SAP, DB2
        - Azure: Azure SQL Database
        - Amazon RDS: all including Aurora
        - Amazon S3
        - DocumentDB
    - Targets
        - On-Premises and EC2 instances databases: Oracle, MS SQL Server, MySQL, MariaDB, PostgreSQL, SAP
        - Amazon RDS
        - Redshift, DynamoDB, S3
        - OpenSearch Service
        - Kinesis Data Streams
        - Apache Kafka
        - DocumentDB & Amazon Neptune
        - Redis & Babelfish
- AWS Schema Conversion Tool (SCT)
    - Convert your Database's Schema from one engine to another
    - Example OLTP: (SQL Server or Oracle) to MySQL, PostgreSQL, Aurora
    - Example OLAP: (Teradata or Oracle) to Amazon Redshift
    - You do not need to use SCT if you migrating the same DB engine
        - Ex: On-Premise PostgreSQL => RDS PostgreSQL
        - The DB engine is till PostgreSQL (RDS is the platform)
- DMS - Continuous Replication
- AWS DMS -Multi-AZ Deployment
    - When Multi-AZ Enabled, DMS provisions and maintains a synchronously stand replica in different AZ
    - Advantages:
        - Provide Data Redundancy
        - Eliminates I/O freezes
        - Minimize latency spikes

### RDS & Aurora Migrations

- RDS & Aurora MySQL Migrations
    - RDS MySQL to Aurora MySQL
        - Option 1: DB Snapshots from RDS MySQL restored as MySQL Aurora DB
        - Options 2: Create an Aurora Read Replica from your RDS MySQL, and when the replication lag is 0, promote it as
          its own DB cluster (can take time and cost $)
    - External MySQL to Aurora MySQL
        - Option 1:
            - Use Percona XtraBackup to create a file backup in Amazon S3
            - Create an Aurora MySQL DB from Amazon S3
        - Option 2:
            - Create an Aurora MySQL DB
            - Use the mysqldumb utility to migrate MySQL into Aurora (slower than S3 method)
    - Use DMS if both databases are up and running
- RDS & Aurora PostgreSQL Migrations
    - RDS PostgreSQL to Aurora PostgreSQL
        - Option 1: DB Snapshots from RDS PostgreSQL restored as PostgreSQL Aurora DB
        - Options 2: Create an Aurora Read Replica from your RDS PostgreSQL, and when the replication lag is 0, promote
          it as
          its own DB cluster (can take time and cost $)
    - External PostgreSQL to Aurora PostgreSQL
        - Create a backup and put it in Amazon S3
        - Import it using the aws_s3 Aurora extension
    - Use DMS if both databases are up and running

### On-Premise strategy with AWS

- Ability to download Amazon Linux 2 AMI as a VM (.iso format)
    - VMWare, KVM, VirtualBox (Oracle VM), Microsoft Hyper-V
- VM import/Export
    - Migrate existing applications into EC2
    - Create a DR repository strategy for your on-premise VMs
    - Can export back the VMs from EC2 to on-premise
- AWS Application Discovery Service
    - Gather information about your on-premise servers to plan a migration
    - Server utilization and dependency mappings
    - Track with AWS Migration Hub
- AWS Database Migration Service (DM)
    - Replicate On-premise => AWS, AWS => AWS, AWS => On-premise
    - Works with various database technologies (Oracle, MySQL, DynamoDB, etc...)
- AWS Server Migration Service (SMS)
    - Incremental replication of on-premise live servers to AWS

### AWS Backup

- Fully managed service
- Centrally manage and automate backups across AWS services
- No need to create custom scripts and manual process
- Supported services:
    - Amazon EC2 / Amazon EBS
    - Amazon S3
    - Amazon RDS (all DBs engines) / Amazon Aurora / Amazon DynamoDB
    - Amazon DocumentDB / Amazon Neptune
    - Amazon EFS / Amazon FSx (Lustre & Windows File Server)
    - AWS Storage Gateway (Volume Gateway)
- Supports cross-region backups
- Supports cross-account backups
- Supports PITR for supported services
- On-Demand and Scheduled backups
- Tag-based backup policies
- You create backup policies as Backup Plans
    - Backup frequency (every 12 hours, daily, weekly, monthly, cron expression)
    - Backup window
    - Transition to Cold Storage (Never, Days, Weeks, Months, Years)
    - Retention Period (Always, Days, Weeks, Months, Years)
- AWS Backup Vault Lock
    - Enforce a WORM (Write Once Read Many) state for all the backups that you store in your AWS Backup Vault
    - Additional layer of defense to protect your backups against
        - Inadvertent or malicious delete operations
        - Updates that shorten or alter retention periods
    - Even the root user cannot delete backups when enabled

### Application Migration Service (MGN)

- AWS Application Discovery Service
    - Plan migration projects by gathering information about on-premises data centers
    - Server utilization data and dependency mapping are important for migrations
    - Agentless Discovery (AWS Agentless Discovery Connector)
        - VM inventory, configuration, and performance history such as CPU, memory and disu usage
    - Agent-based Discovery (AWS Application Discovery Agent)
        - System configuration, system performance, running processes, and details of the network connections between
          systems
    - Resulting data can be viewed within AWS Migration Hub
- AWS Application Migration Service (MGN)
    - The "AWS evolution" of CloudEndure Migration, replacing AWS Server Migration Service (SMS)
    - Lift-and-shift(rehost) solution which simplify migrating applications to AWS
    - Converts your physical, virtual and cloud-based servers to run natively on AWS
    - Supports wide range of platforms, Operating Systems, and databases
    - Minimal downtime, reduced costs

### Transferring large amount of data into AWS

- Example: transfer 200TB of data in the cloud. We have a 100 Mps internet connection
- Over the internet / Site-to-Site VPN
    - Immediate to setup
    - Will take 185d
- Over direct connect 1Gbps:
    - Long for the one-time setup (over a month)
    - Will take 18.5d
- Over Snowball
    - Will take 2 to 3 snowballs in parallel
    - Takes about 1 week for the end-to-end transfer
    - Can be combined with DMS
- For on-going replication / transfer : Site-to-Site VPN or DX with DMS or DataSync

### VMware Cloud on AWS

- Some customer use VMware Cloud to manage their on-premises Data Center
- They want to extend the Data Center capacity to AWS, but keep using the VMware Cloud software
- Use cases:
    - Migrate your VMware vSphere-based workloads to AWS
    - Run your production workloads across VMware vSphere-based private, public and hybrid cloud environments
    - Have a disaster recovery strategy

## More Solution Architectures

### Event Processing in AWS

- Lambda, SNS & SQS
    - SQS -> Try, retry -> Lambda -> SQS -> DLQ -> SQS FIFO -> Try, retry, blocking -> DLQ
    - SNS -> asynchronous -> Lambda -> DLQ -> SQS
- Fan Out Pattern: deliver to multiple SQS
    - SDK -> multiple SQS
    - SDK -> SNS -> SQS subscribe
- S3 Event Notifications
    - S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectResto re, S3:Replication
    - Object name filtering possible (*.jpg)
    - Use case: generate thumbnails of images uploaded to S3
    - Can create as many "S3 events" as desired
    - S3 event notifications typically deliver events in seconds but can sometime take a minute or longer
- S# Event Notifications wiht Amazon EventBridge
    - Advanced filtering options with JSON rules (metadata, object size, name)
    - Multiple Destinations - ex Step Functions, Kinesis Streams / Firehose
    - EventBridge Capabilities - Archive, Replay Events, Reliable delivery
- Amazon EventBridge - Intercept API Calls
    - User -> DeleteTable API call -> DynamoDB -> Log API call -> CloudTrail (any API call) -> event -> Amazon
      EventBridge -> SNS
- API Gateway - AWS Service Integration Kinesis Data Streams example
    - Client -> API Gateway -> Kinesis Data Streams -> Kinesis Data Firehose -> Amazon S3

### Caching Strategies in AWS

- Caching, TTL, Network, Computation, Cost, Latency

### Blocking an IP address in AWS

- Blocking an IP address
    - NACL
    - Security Group may not help, only have allow rule only
- Blocking an IP address - with an ALB
    - Application Load Balancer Connection Termination
    - ALB can have a Security Group
- Blocking an IP address - with an NLB
    - Network Load Balancer Traffic goes through, No Security Group
- Blocking an IP address - ALB + WAF
    - WAF - IP address filtering
- Blocking an IP address - ALB + WAF
    - CloudFront Geo Restriction
    - WAF IP address filtering
    - NACL not helpful because only know CloudFront Public IPs

### High Performance Computing (HPC) on AWS

- High Performance Computing (HPC)
    - The cloud is the perfect place to perform HPC
    - You can create a very high number of resources in no time
    - You can speed up time to results by adding more resources
    - You can pay only for the systems you have used
    - Perform genomics, computational chemistry, financial risk modeling, weather prediction, machine learning, deep
      learning, autonomous driving
- Data Management & Transfer
    - AWS Direct Connect
        - Move GB/s of data to the cloud, over a private secure network
    - Snowball & Snowmobile
        - Move PB of data to the cloud
    - AWS DataSync
        - Move large amount of data between on-premise and S3, EFS, FSx for Windows
- Compute and Networking
    - EC2 instances:
        - CPU optimized, GPU optimized
        - Spot Instances / Spot Fleets for cost savings + Auto Scaling
    - EC2 Placement Groups: Cluster for good network performance
    - EC2 Enhanced Networking (SR-IOV)
        - Higher bandwidth, higher PPS (packet per second), lower latency
        - Option 1: Elastic Network Adapter (ENA) up to 100 Gbps
        - Option 2: Intel 82599VF up to 10 Gbps - LEGACY
    - Elastic Fabric Adapter (EFA)
        - Imporved ENA for HPC, only works for Linux
        - Greate for inter-node communications, tightly coupled workloads
        - Leverages Message Passing Interface (MPI) standard
        - Bypasses the underlying Linux OS to provide low-latency, reliable transport
- Storage
    - Instance-attache storage
        - EBS: scale up to 256000 IOPS with io2 Block Express
        - Instance Store: scale to millions of IOPS, linked to EC2 instance, low latency
    - Network storage
        - Amazon S3: large blob, not a file system
        - Amazon EFS: scale IOPS based on total size, or use provisioned IOPS
        - Amazon FSx for Lustre
            - HPC optimized distributed file system, millions of IOPS
            - Backed by S3
- Automation and Orchestration
    - AWS Batch
        - AWS Batch supports multi-node parallel jobs, which enables you to run single jobs that span multiple EC2
          instances
        - Easily schedule jobs and launch EC2 instances accordingly
    - AWS ParallelCluster
        - Open-source cluster management tool to deploy HPC on AWS
        - Configure with text files
        - Automate creation of VPC, Subnet, cluster type and instance types
        - Ability to enable MFA on the cluster (improves network performance)

### EC2 Instance High Availability

- Creating a highly available EC2 instance
    - Elastic IP address attach to Public EC2, and has a Standby EC2 instance
    - CloudWatch Event (or Alarm based on metric) trigger the Lambda
    - Lambda start the instance and attach the Elastic IP
- Creating a highly available EC2 instance with an Auto Scaling Group
    - ASG settings: 1 min, 1 max, 1 desired, >=2AZ
    - EC2 user data to attach the Elastic IP
    - EC2 instance role to Allow API calls to attach the Elastic IP
- Creating a highly available EC2 instance with ASG + EBS
    - EBS Snapshot on ASG Terminate lifecycle hook
    - EBS Volume created + attached on AWS Launch lifecycle hook
    - EC2 User Data Attachment Based on Tag

## Other AWS Services

### Amazon Simple Email Service (Amazon SES)

- Fully managed service to send emails securely, globally and at scale
- Allows inbound/outbound emails
- Reputation dashboard, performance insights, anti-spam feedback
- Provides statistics such as email delivers, bounces, feedback loop results, email open
- Supports DomainKeys Identified Mail (DKIM) and Sender Policy Framework (SPF)
- Flexible IP deployment: shared, dedicated, and customer-owned IPs
- Send emails using your application using AWS Console, APIs or SMTP
- Use cases: transactional, marketing and bulk email communications

### Amazon Pinpoint

- Scalable 2-way (outbound.inbound) marketing communicates services
- Supports email, SMS, push, voice and in-app messaging
- Ability to segment and personalize messages with the right content to customers
- Possibility to receive replies
- Scales to billions of messages per day
- Use cases: run campaigns by sending marketing, bulk transactional SMS messages
- Versus Amazon SNS or Amazon SES
    - In SNS&SES you managed each message's audience, content and delivery schedule
    - In Amazon Pinpoint, you create message templates, delivery schedules, highly-targeted segments, and full campaigns

### System Manager - SSM Session Manager

- Allows you to start a secure shell on your EC2 and on-premises servers
- No port 22 needed (better security)
- No SSH access, bastion hosts, or SSH keys needed
- Supports Linux, macOS and Windows
- Send session log data to S3 or CloudWatch Logs

### Systems Manager - Run Command

- Execute a document(= scrypt) or just run a command
- Run command across multiple instances(using resource groups)
- No need for SSH
- Command Output can be shown in the AWS Console, sent to S3 bucket or CloudWatch Logs
- Send notifications to SNS about command status (In progress, Success, Failed...)
- Integrated with IAM & CloudTrail
- Can be invoked using EventBridge

### Systems Manager - Patch Manager

- Automates the process of patching managed instances
- OS updates. applications updates, security updates
- Supports EC2 instances, and on-premises servers
- Support Linux, macOS and Windows
- Patch on-demand or on a schedule using Maintenance Windows
- Scan instances and generate patch compliance report (missing patches)

### System Manager - Maintenance Windows

- Defines a schedule for when to perform actions on your instances
- Example: OS patching, updating drivers, installing software...
- Maintenance Window contains
    - Schedule
    - Duration
    - Set of registered instances
    - Set of registered tasks

### Systems Manager - Automation

- Simplifies common maintenance and deployment tasks of EC2 instances and other AWS resources
- Examples: restart instances, create an AMI, EBS snapshot
- Automation Runbook - SSM Documents to define actions performed on your EC2 instances or AWS resources (pre-defined or
  custom)
- Can be triggered using:
    - Manually using AWS Console, AWS CLI or SDK
    - Amazon EventBridge
    - On a schedule using Maintenance Windows
    - By AWS Config for rules remediation

### AWS Cost Explorer

- Visualize, understand, and manage your AWS costs and usage over time
- Create custom reports that analyze costs and usage data
- Analyze your data ata high level: total costs and usage across all accounts
- Or Monthly, hourly, resource level granularity
- Choose an optimal Savings Plan (to lower prices on your bill)
- Forecast usage up to 12 months based on previous usage

### Amazon Elastic Transcoder

- Used to convert media files stored in S3 into media files in the formats required by consumer playback devices (phones
  etc...)
- Benefits:
    - Easy to use
    - Highly scalable - can handle large volumes of media files and large file sizes
    - Cost effective - duration-based pricing model
    - Fully managed & secure, pay for what you use

### AWS Batch

- Fully managed batch processing at any scale
- Efficiently run 100,000s of computing batch jobs on AWS
- A "batch" job is a job with a start and an end (opposed to continuous)
- Batch will dynamically launch EC2 instances or Spot Instances
- AWS Batch provisions the right amount of compute/memory
- You submit or schedule batch jobs and AWS Batch does the rest
- Batch jobs are defined as Docker images and run on ECS
- Helpful for cost optimizations and focusing less on the infrastructure
- Batch vs Lambda
    - Lambda
        - Time limit
        - Limited runtimes
        - Limited temporary dish space
        - Serverless
    - Batch
        - No time limit
        - Any runtime as long as it's packaged as a Docker image
        - Rely on EBS/Instance store for disk space
        - Relies on EC2 (can be managed by AWS)

### Amazon AppFlow

- Fully managed integration service that enables you to securely transfer data between Software-as-a-Service(SaaS)
  applications and AWS
- Sources: Salesforce, SAP, Zendesk, Slack and ServiceNow
- DestinationsL AWS Services like Amazon S3, Amazon Redshift or non-AWS such as SnowFlake and Salesforce
- Frequency: on a schedule, in response to events or on demand
- Data transformation capabilities like filtering and validation
- Encrypted over the public internet or privately over AWS PrivateLink
- Don't spend time writing the integrations and leverage APIs immediately

## WhitePapers and Architectures

### AWS Well-Architected Framework

- Well Architected Framework General Guiding Principles
    - Stop guessing your capacity needs
    - Test systems at production scale
    - Automate to make architectural experimentation easier
    - Allow for evolutionary architectures
        - Design based on changing requirements
    - Drive architectures using data
    - Improve through game days
- Well Architected Framework 6 Pillars
    - Operational Excellence
    - Security
    - Reliability
    - Performance Efficiency
    - Cost Optimization
    - Sustainability
    - They are not something to balance, or trade-offs, they're a synergy
- AWS Well-Architected Tool
    - Free tool to review your architectures against the 6 pillars Well-Architected Framework and adopt architectural
      best practices
    - How does it work?
        - Select your workload and answer questions
        - Review your answers against the 6 pillars
        - Obtain advice: get videos and documentations, generate a report, see the results in the dashboard

### AWS Trusted Advisor

- No need to install anything - high level AWS account assessment
- Analyze your AWS account and provides recommendation on 5 categories
    - Cost optimization
    - Performance
    - Security
    - Fault tolerance
    - Service limits
- Trusted Advisor - Support plans
    - 7 Core checks (Basic & Developer Support Plan)
        - S3 buckets permissions
        - Security Group - Specific Ports Unrestricted
        - IAM User (one IAM user minimum)
        - RDS Public snapshots
        - EBS Public snapshots
        - MFA on Root Account
        - Service Limits
    - Full checks (Business & Enterprise Support Plan)
        - Full checks available on the 5 categories
        - Ability to set CloudWatch alarms when reaching limits
        - Programmatic Access using AWS Support API

### Examples of Architecture

- aws.amazon.com/architecture/
- aws.amazon.com/solutions/

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