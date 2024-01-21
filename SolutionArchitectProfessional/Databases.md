# Databases

## DynamoDB

### DynamoDB - in short

- NoSQL database, fully managed, massive scale (1,000,000 rps)
- Similar to Apache Cassandra (can migrate to DynamoDB)
- No disk space to provision, max object size is 400KB
- Supports CRUD (Create Read Update Delete)
- Read: eventually or stronglly consistency
- Supports transactions across multiple tables (ACID support)
- Backups available, point in time recovery
- Table classes: Standard and Infrequent Access (IA)

### DynamoDB - Basics

- DynamoDB is made of tables
- Each table has a primary key (must be decided at creation time)
- Each table can have an infinite number of items (=rows)
- Each item hs attributes (can be added over time - can be null)
- Maximum size of a item is 400KB
- Data types supported are:
    - Scalar Types: String, Number, Binary, Boolean, Null
    - Document Types: List, Map
    - Set Types: Set, Number Set, Binary Set

### DynamoDB - Primary Keys

- Option 1: Partition key only (HASH)
    - Partition Key must be unique for each item
    - Partition key must be "diverse" so that the data is distributed
    - Example: user_id for users table
- Option 2: Partition key + Sort Key
    - The combination must be unique
    - Data is grouped by partition key
    - Sort key == range key
    - Example: user-games table
        - user_id for the partition key
        - game_id for the sort key
    - Example good sort key: timestamp

### DynamoDB - Indexes

- Object = partition key + optional sort key + attributes
- LSI - Local Secondary Index
    - Keep the same primary key
    - Select an alternative sort key
    - Must be defined at table creation time
- GSI - Global Secondary Index
    - Change the primary key and optional sort key
    - Can be defined after the table is created
- You can only query by PK + sort key on the main table & indexes (!=RDS)

### DynamoDB - Important Features

- TTL: automatically expire row after a specified epoch date
- DynamoDB Streams
    - react to changes to DynamoDB tables in real time
    - Can be read by AWS Lambda, EC2...
    - 24 hours retention of data
    - CRUD -> Table -> Streams -> Lambda -> Amazon OpenSearch / Kinesis
- Global Tables (Cross region replication)
    - Active Active replication, many regions
    - Must enable DynamoDB Streams
    - Useful for low latency, DR purposes

### Amazon Kinesis Data Streams for DynamoDB

- You can use Kinesis Data Streams to capture item-level changes in DynamoDB
- Custom and longer data retention period (>24 hours in DynamoDB Streams)
- DynamoDB Table -> item-level changes -> Kinesis Data Streams
    - -> Kinesis Data Firehose -> Store -> Amazon S3 /
      Redshift / OpenSearch
    - -> Kinesis Data Analytics ->  Real-time computations (filter, aggregate, transform,...) -> Kinesis Data Streams,
      Kinesis Data Firehose, Lambda

### DynamoDB Solution Architecture - Indexing objects in DynamoDB

- wires -> Amazon S3 -> Lambda Function -> DynamoDB Table
- API for object metadata
    - Search by date
    - Total storage used by a customer
    - List of all objects with certain attributes
    - Find all objects uploaded within a data range

### DynamoDB - DAX

- DAX = DynamoDB Accelerator
- Seamless cache for DynamoDB, no application re-write
- Writes go through DAX for DynamoDB
- Micros second latency for cached reads & queries
- Solves the Hot Key Problem (too many reads)
- 5 minutes TTL for cache by default
- Up to 10 nodes in the cluster
- Multi AZ (3 nodes minimum recommended for production)
- Secure (Encryption at rest with KMS, VPC, IAM, CloudTrail)

### DynamoDB - DAX vs ElastiCache

- Client
    - -> Store Aggregation Result -> ElastiCache
    - -> Individual objects cache Query / Scan cache -> DynamoDB

## Amazon OpenSearch

### Amazon OpenSearch (ex ElasticSearch)

- New name is Amazon OpenSearch
- ElasticSearch -> OpenSearch
- Kibana -> OpenSearch Dashboards
- Managed version of OpenSearch (open-source project, fork of ElasticSearch)
- Needs to run on servers (not a serverless offering)
- Use cases
    - Log Analytics
    - Real Time application monitoring
    - Security Analytics
    - Full Text Search
    - Clickstream Analytics
    - Indexing

### OpenSearch - OS Dashboards + Logstash

- OpenSearch (ex ElasticSearch): provide search and indexing capability
    - You must specify instance types, multi-AZ, etc
- OpenSearch Dashboards (ex Kibana)
    - Provide real-time dashboards on top of the data that sits in OpenSearch
    - Alternative to CloudWatch dashboards (more advanced capabilities)
- Logstash:
    - Log ingestion mechanism, use the "Logstash Agent"
    - Alternative to CloudWatch Logs (you decide on retention and granularity)

### OpenSearch patterns - DynamoDB

- CRUD -> DynamoDB Table -> DynamoDB Stream -> Lambda Function -> Amazon OpenSearch -> API to search items, API to
  retrieve items

### OpenSearch patterns - CloudWatch Logs

- CloudWatch Logs -> Subscription Filter -> Lambda Function (managed by AWS) -> Real time -> Amazon OpenSearch
- CloudWatch Logs -> Subscription Filter -> Kinesis Data Firehose -> Near Real Time -> Amazon OpenSearch

## RDS

- Engines: PostgreSQL, MySQL, MariaDB, Oracle, Microsoft SQL Server
- Managed DB: provisioning, backups, patching, monitoring
- Launched within a VPC, usually in private subnet, control network access using security groups (important when using
  Lambda)
- Storage by EBS (gp2 or io1), can increase volume size with auto-scaling
- Backups: automated with point-in-time recovery. Backups expire
- Snapshots: manual, can make copies of snapshots cross region
- RDS Events: get notified via SNS for events (operations, outages...)

### RDS - Multi AZ & Read Replicas

- Multi-AZ: Standby instance for failover in case of outage
    - Application -> reads / writes -> One DNS name - automatic failover -> Master Instance (AZ - A) -> SYNC
      replication -> Standby instance (AZ - B)
- Read Replicas: Increase read throughput. Eventual consistency. Can be cross-region
    - Application -> reads -> RDS Read Replica
    - Application -> writes/reads -> RDS Instance -> ASYNC replication

### RDS - Distributing Reads across Replicas

- Application -> requests -> Amazon Route 53 ( + health check), Weighted Record Set
    - -> 25% -> ReadReplica 1
    - -> 25% -> ReadReplica 2

### RDS - Security (reminder)

- KMS encryption at rest for underlying EBS volumes / snapshots
- Transparent Data Encryption (TDE) for Oracle and SQL Server
- SSL encryption to RDS is possible for all DB (in-flight)
- IAM authentication for MysQL, PostgreSQL and MariaDB
- Authorization still happens within RDS (not in IAM)
- Can copy an un-encrypted RDS snapshot into an encrypted one
- CloudTrail cannot be used to track queries made within RDS

### RDS - IAM Authentication

- IAM database authentication works with MariaDB, MySQL and PostgreSQL
- You don't need a password, just an authentication token obtained through IAM & RDS API calls
- Auth token has a lifetime of 15 minutes
- EC2 with IAM Role -> API Call, Get Auth Token -> RDS Service
    - -> SSL encryption Pass Auth Token -> RDS Security Group -> MySQL
- Benefits
    - Network in/out must be encrypted using SSL
    - IAM to centrally manage users instead of DB
    - Can leverage IAM Roles and EC2 instance profiles for easy integration

### About RDS for Oracle - Exam Tips

- Backups
    - Use RDS Backups for backups & restore to Amazon RDS for Oracle
        - RDS DB Instance -> backup -> RDS Backup -> restore -> RDS DB Instance
    - Use Oracle RMAN (Recovery Manager) for backups & restore to-non RDS (RDS not supported)
        - RDS DB Instance -> backup -> Oracle RMAN Backup -> upload -> S3 Bucket -> restore -> Oracle DB (external)
- Real Application Clusters (RAC)
    - RDS for Oracle does NOT support RAC
    - RAC is working on Oracle on EC2 Instances becasue you have full control
- RDS for Oracle supports Transparent Data Encryption (TDE) to encrypt data before it's written to storage
- DMS works on Oracle RDS

### About RDS for MySQL

- You can use the native `mysqldump` to migrate a MySQL RDS DB to non-RDS
- The external MySQL database can run either on-premises in your data center, or on a Amazon EC2 instance
- DB Admin
    - Export using `mysqldump` from RDS MySQL DB Instance (Source) in AWS Cloud
    - Import using `musqldump` to MySQL DB Instance in on-premises Data Center (Target)
    - Start Replication
    - Stop Replication after completion

### RDS Proxy for AWS Lambda

- When using Lambda functions with RDS, it opens and maintains a database connection
- This can result in a `TooManyConnections` exceptions
- With RDS Proxy, you no longer need code that handles cleaning up idle connections and managing connection pools
- Supports IAM authentication or DB authentication, auto-scaling
- The Lambda function must have connectivity to the Proxy (public proxy => public Lambda, private proxy => Lambda in
  VPC)

### RDS Solution Architecture - Cross Region Failover

- RDS Main (us-east-1)
    - -> Option1: EC2 with /health-db route
    - -> Option2: CW Alarm
- Health check -> CW Alarm linked to Health Check -> CW Event linked to CW Alarm (or SNS topic) -> trigger -> Lambda ->
  Update DNS -> Promoted Read Replicas -> RDS Read Replica (us-west-2), Async replication

## Aurora

- DB Engine: PostgreSQL - compatible & MySQL - compatible
- Storage: automatically grows up to 128TB. 6 copies of data, multi-AZ
- Read Replicas: up to 15RR, reader endpoint to access them all
- Cross Region RR: entire database is copied (not select tables)
- Load / Offload data directly from / to S3: effiecnt use of resources
- Backup, Snapshots & Restore: same as RDS

### Aurora High Availability and Read Scaling

- 6 copies of your data across 3 AZ
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 need for reads
    - Self healing with peer-to-peer replication
    - Storage is striped across 100s of volumes
- Automated failover for master in less than 30 seconds
- Master + up to 15 Aurora Read Replicas serve reads
- Support for Cross Region Replication

### Aurora DB Cluster

- Writer Endpoint - Pointing to the master
- Reader Endpoint - Connection Load Balancing
- Shared storage Volume - Auto Expanding

### Aurora Endpoints

- Endpoint = Host Address + Port
- Cluster Endpoint (Writer Endpoint)
    - Connects to the current primary DB instance in the Aurora cluster
    - Used for all write operations in the DB cluster (insert, updates, deletes, and queries)
- Reader Endpoint
    - Provides load-balancing for read only connections to all Aurora Replicas in the Aurora cluster
    - Used only for read operations (queries)
- Custom Endpoint
    - Represents a set on DB instances that you choose in the Aurora cluster
    - Used when you want to connect to difference subsets of DB instances with different capacities and configuration (
      e.g, differenc DB to parameter group)
- Instance Endpoint
    - Connects to a specific DB instance in the Aurora cluster
    - Used when you want to diagnosis and fine tune a specific DB instance

### Aurora - Custom Endpoints

- Define a subeset of Aurora Instances as a Custom Endpoint
- Example: Run analytical queries on specific replicas
- The Reader Endpoint is generally not used after defining Custom Endpoints

### Aurora Logs

- You can monitor the following types of Aurora MySQL log files:
    - Error log
    - Slow query log
    - General log
    - The audit log
- These log files are either downloaded or published to CloudWatch Logs

### Troubleshooting RDS & Aurora Performance

- Performance Insights: find issues by waits. SQL statements, hosts and users
- CloudWatch Metrics: CPU, Memory, Swap Usage
- Enhanced Monitoring Metrics: at host level, process view, per-second metric
- Slow Query logs

### Aurora Serverless

- Automated database instantiation and auto-scaling based on actual usage
- Good for infrequent, intermittent or unpredictable workloads
- No capacity planning needed
- Pay per second, can be more cost-effective
- Client -> Proxy Fleet (managed by Aurora) -> Shared storage Volume

### Aurora Serverless - Data API

- Access Aurora Serverless DB with a simple API endpoint (no JDBC connection needed)
- Secure HTTPS endpoint to run SQL statements
- No persistent DB connections management
- Users must be granted permissions to Data API and Secrets Manager (where credentials are checked)
- Application -> HTTP API Calls (without DB credentials) -> API -> transparent allow / deny -> Secrets Manager -> Aurora
  Serverless

### RDS Proxy for Aurora

- Users -> Application Load Balancer -> Lambda Function -> RDS Proxy -> Aurora (Primary Instance, Read & Write)
- Ability to create an additional read-only endpoint that connects to Aurora Read Replicas only
- Lambda Functions -> read only endpoint -> RDS Proxy Read-only endpoint -> Aurora Read Replica 1,2,...

### Global Aurora

- Aurora Cross Region Read Replicas
    - Useful for disaster recovery
    - Simple to put in place
- Aurora Global Database (recommended)
    - 1 Primary Region (read / write)
    - Up to 5 secondary (read-only) regions, replication lag is less than 1 second
    - Up to 16 Read Replicas per secondary region
    - Helps for decreasing latency
    - Promoting another region (for disaster recovery) has an RTO of < 1 minute
    - Ability to manage the RPO in Aurora for PostgreSQL

### Aurora Global - Write Forwarding

- Enables Secondary DB Clusters to forward SQL statement that perform write operations to the Primary DB Cluster
- Data is alwasy changed first on the Primary DB Cluster, then replicated to the Secondary DB Clusters
- Primary DB Cluster always has an up-to-date copy of all data
- Reduces the number of endpoints to manage

### Convert RDS to Aurora

- RDS DB Instance -> snapshot -> DB Snapshot -> restore -> Aurora DB Instance
- RDS DB Instance -> New replica -> Aurora Read Replica -> promote -> Aurora DB Instance






























