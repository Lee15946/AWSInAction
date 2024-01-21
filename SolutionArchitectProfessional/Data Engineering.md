# Data Engineering

## Kinesis Data Streams

### AWS Kinesis Overview

- Kinesis is a managed "data streaming" service
- Great for application logs, metrics, IoT, clickstreasm
- Great for "real-time" big data
- Great for streaming processing frameworks (Spark, NiFi, etc...)
- Data is automatically replicated synchronously to 3 AZ
- Kinesis Streams: low latency streaming ingest at scale
- Kinesis Analytics: perform real-time analytics on streams using SQL
- Kinesis Firehose: load stream into S3, Redshift, ElasticSearch & Splunk

### Kinesis Streams Overview

- Streams are divided in ordered Shards / Partitions
- Data retention is 24 hours by default, can go up to 365 days
- Ability to reprocess / replay data
- Multiple applications can consume the same stream
- Read-time processing with scale of throughput
- Once data is inserted in Kinesis, it can't be deleted (immutability)

### Kinesis Streams Shards

- Tow modes for capacity
    - On-demand: no capacity planning, Kinesis scales shards automatically
    - Provisioned: you manage the shards over time
- Batching available or per message calls
- The number of shards can evolve over time (reshard/merge)
- Records are ordered per shard

### Kinesis Producers & Consumers

- Kinesis Producers
    - AWS SDK: simple producer
    - Kinesis Producer Library (KPL): batch, compression, retries, C++, Java
    - Kinesis Agent
        - Monitor log files and sends them to Kinesis directly
        - can wrtie to Kinesis Data Streams AND Kinesis Data Firehose
- Kinesis Consumers
    - AWS SDK: simple consumer
    - Lambda (through Event source mapping)
    - KCL: checkpointing, coordinated reads

### Kinesis Data Streams Limits to know

- Producer
    - 1MB/s or 1000 messages/s at write PER SHARD
    - `ProvisionedThroughputException` otherwise
- Consumer Classic
    - 2MB/s at read PER SHARD across all consumers
    - 5 API calls per second PER SHARD across all consumers
- Consumer Enhanced Fan-Out
    - 2MB/s at read PER SHARD, PER ENHANCED CONSUMER
    - No API calls needed (push model)
- Data Retention
    - 24 hours data retention by default
    - Can be extended to 365 days

## Kinesis Data Firehose

- Fully Managed Service, no administration, automatic scaling, serverless
    - AWS: Redshift / Amazon S3 / OpenSearch
    - 3rd party partner: Splunk / MongoDB / DataDog / NewRelic / ...
    - Custom: send to any HTTP endpoint
- Pay for data going through Firehose
- Near Real Time
    - 60 seconds latency minimum for non full batches
    - Or minimum 1MB of data at a time
- Supports many data formats, conversions, transformations, compressions
- Supports custom data transformations using AWS Lambda
- Can send failed or all data to a backup S3 bucket

### Firehose Buffer Sizing

- Firehose accumulates records in a buffer
- The buffer is flushed based on time and size rules
- Buffer Size (ex: 32MB): if that buffer size is reached, it's flushed
- Buffer Time (ex: 1 minute): if that time is reached, it's flushed
- Firehose can automatically increase the buffer size to increase throughput
- High throughput =? Buffer Size will be hit
- Low throughput => Buffer Time will be hit
- If rea-time flush from Kinesis Data Streams to S3 is needed, use Lambda

### Kinesis Data Streams vs Firehose

- Kinesis Data Streams
    - Streaming service for ingest at scale
    - Write custom code (producer / consumer)
    - Real-time (~200ms)
    - Manage scaling (shard splitting / merging)
    - Data storage for 1 to 365 days
    - Supports replay capability
- Kinesis Data Firehose
    - Load streaming data into S3 / Redshift / OpenSearch / 3rd party / custom HTTP
    - Fully managed
    - Near real-time (buffer time min. 60 sec)
    - Automatic scaling
    - No data storage
    - Doesn't support replay capability

## Kinesis Data Analytics

### Kinesis Analytics, Conceptually...

- Use cases
    - Streaming ETL: select columns, make simple transformations, on streaming data
    - Continuous metric generation: live leaderboard for a mobile game
    - Responsive analytics: look for certain criteria and build alerting (filtering)
- Features
    - Pay only for resources consumed (but it's not cheap)
    - Serverless: scales automatically
    - Use IAM permissions to access streaming source and destination(s)
    - SQL or Flink to write the computation
    - Schema discovery
    - Lambda can be used for pre-processing

## Steaming Architectures

### Full data Engineering Pipeline - Real-Time Layer

- producers -> Amazon Kinesis Data Streams -> Amazon Kinesis Data Analytics
    - -> AWS Lambda
    - -> Amazon Kinesis Data Streams -> Amazon EC2
    - -> Amazon Kinesis Data Firehose -> Amazon S3 -> Amazon Redshift
        - -> Amazon ElasticSearch Service
- producers -> Amazon Kinesis Data Firehose -> Amazon S3

### Streaming Architectures - 3000 messages of 1KB per second

- Kinesis
    - 3 shards: 3MB/s in
    - 3 * $0.015/hr = $32.4/mth
    - Must use KDJ for output to S3
- DynamoDB + Streams
    - 3000WCU = 3MB/s
    - = $1450.90/month
    - Storage in DynamoDB

## Amazon MSK

### Amazon Managed Streaming for Apache Kafka (Amazon MSK)

- Alternative to Amazon Kinesis
- Fully managed to create, update, delete clusters
    - Allow you to create, update, delete clusters
    - MSK creates & manages Kafka brokers nodes & Zookeeper nodes for you
    - Deploy the MSK cluster in your VPC, multi-AZ (up to 3 for HA)
    - Automatic recovery from common Apache Kafka failures
    - Data is stored on EBS volumes for as long as you want
- MSK Serverless
    - Run Apache Kafka on MSK without managing the capacity
    - MSK automatically provisions resources and scales compute & storages

### Kinesis Data Streams vs. Amazon MSK

- Kinesis Data Streams
    - 1MB message size limit
    - Data Streams with Shards
    - Shard Splitting & Merging
    - TLS In-flight encryption
    - KMS at-rest encryption
- Amazon MSK
    - 1MB default, configure for higher (ex: 10MB)
    - Kafka Topics with Partitions
    - Can only add partitions to a topic
    - PLAINTEXT or TLS In-flight Encryption
    - KMS at-rest encryption

### Amazon MSK Consumers

- Amazon MSK
    - -> Kinesis Data Analytics for Apache Flink
    - -> AWS Glue Streaming ETL Jobs, Powered by Apache Spark Streaming
    - -> Lambda
    - -> Applications running on Amazon EC2, ECS, EKS

## AWS Batch

- Run batch jobs as Docker images
- Two options:
    - Run on AWS Fargate (fully serverless offering)
    - Dynamic provisioning of the instances (EC2 & Spot Instances) - in VPC
- Optimal quantity and type based on volume and requirements
- No need to manage clusters, fully serverless
- You just pay for the underlying resources used
- Example: batch process of images, running thousands of concurrent jobs
- Schedule batch Jobs using Amazon EventBridge
- Orchestrate Batch Jobs using AWS Step Functions

### AWS Batch - Solution Architecture

- User -> upload -> Amazon S3 ->
    - -> Event Notifications -> Lambda -> API call
    - -> event -> Amazon EventBridge -> trigger
- -> AWS Batch
    - Amazon ECS
        - EC2 Instance
        - Spot Instance (Spot Fleet)
        - AWS Fargate
- -> processed file -> Amazon S3
- -> pull Docker images -> Amazon ECR
- -> retrieve file (API call) -> Amazon S3

### Batch vs. Lambda

- Lambda
    - Time limit
    - Limited runtimes (built in runtimes, or Docker images built for Lambda)
    - Limited temporary disk space
    - Serverless
- Batch
    - No time limit
    - Any runtime as long as it's packaged as a Docker image
    - Rely on EBS / instance store for disk space
    - Relies on EC2 (can be managed by AWS) or AWS Fargate

### AWS Batch - Compute Environments

- Managed Compute Environment
    - Managed Compute Environment
        - AWS Batch managed the capacity and instance types within the environment
        - You can choose On-Demand or Spot Instances
        - You can set a maximum price for Spot Instance
        - Launched within your onw VPC
            - if you launch within your own private subnet, make sure it has access to the ECS service
            - Either using a NAT gateway / instance or using VPC Endpoints for ECS
- Unmanaged Compute Environment
    - You control and manage instance configuration, provisioning, and scaling

### AWS Batch - Managed Compute Environment

- SDK -> Add jobs -> AWS Batch Job Queue -> Distribute jobs -> AWS Batch (set min & max vCPU)
    - (Spot instances) Automatically created to respond, to increase/decrease in jobs

### AWS Batch - Mutli Node MOde

- Multi Node: large scale, good for HPC (high performance computing)
    - Leverages multiple EC2/ECS instances at the same time
    - Good for tightly coupled workloads
    - Represents a single job, and specified how many nodes to create for the job
    - 1 main node, and many child node
    - Does not work with Spot Instances
    - Works better if your EC2 launch mode is a placement group "cluster"

## Amazon EMR

- EMR stands for "Elastic MapReduce"
- EMR helps creating Hadoop clusters (Big Data) to analyze and process vast amount of data
- The clusters can be made of hundreds of EC2 instances
- Also supports Apache Spark, HBase, Presto,Flink...
- EMR takes care of all the provisioning and configuration of EC2
- Auto-scaling with CloudWatch
- Use cases: data processing, machine learning, web indexing, big data...

### EMR - Integrations

- EMR
    - VPC - Single AZ
    - EC2 -> EBS Volume (HDFS, Temporary Storage)
    - Hive -> Read from DynamoDB
    - EMRFS (native integration) -> S3 (EMRFS) Permanent Storage, Server-side encryption

### Amazon EMR - Node types & purchasing

- Master Node: Manage the cluster, coordinate, manage health - long running
- Core Node: Run tasks and store data - long running
- Task Node (optional): Just to run tasks - usually Spot
- Purchasing options
    - On-demand: reliable, predictable, won't be terminated
    - Reserved (min 1 year): cost savings (EMR will automatically use if available)
    - Spot Instances: cheaper, can be terminated, less reliable
- Can have long-running cluster, transient (temporary) cluster

### Amazon EMR - Instance Configuration

- Uniform instance groups: select a singe instance type and purchasing option for each node (has auto scaling)
- Instance fleet: select target capacity, mix instance types and purchasing options (no Auto Scaling)

## Running Jobs on AWS

- Provision EC2 instance (long running - CRON jobs)
- Amazon EventBridge + Lambda (corn)
- Reactive Workflow
- use AWS Batch
- use Fargate
- Use EMR (step execution or cluster)

## AWS Glue

- Managed extract, transform, and load (ETL) service
- Useful to prepare and transform data for analytics
- Fully serverless service
- S3 Bucket, Amazon RDS -> Extract -> Glue ETL -> Transform -> Load -> Redshift Data Warehouse

### Glue Data Catalog

- Glue Data Catalog: catalog of datasets
- Amazon S3, Amazon RDS, Amazon DynamoDB, JDBC -> AWS Glue Data Crawler -> AWS Glue Data Catalog -> Data discovery -> Amazon Athena, Amazon Redshift Spectrum, Amazon EMR

## Redshift

### Redshift Overview

- Redshift is based on PostgreSQL, but it's not used for OLTP
- It's OLAP - online analytical processing (analytics and data warehousing)
- 10x better prformance than other data warehouses, scale to PBs of data
- Columnar storage of data (instead of row based)
- Massively Parallel Query Execution (MPP)
- Pay as you go based on the instances provisioned
- Has a SQL interface for performing the queries
- BI tools such as AWS Quicksight or Tableau integrate with it

### Redshift Continued...

- Data is loaded from S3, Kinesis Firehose, DynamoDB, DMS...
- Based on node type: up to 100+ nodes, up t 16TB of space per node
- Can provision multiple nodes, with Multi-AZ only for some clusters
- Leader node: for query planning, results aggregation
- Compute node: for performing the queries, send results to leader
- Backup & Restore, Security VPC / IAM / KMS, Monitoring
- Redshift Enhanced VPC Routing: COPY / UPLOAD goes through VPC
- Redshift is provisioned, so it's worth it when you have a sustained usage (use Athena if the queries are sporadic instead)

### Redshift - Snapshots & DR

- Snapshots are point-in-time backups of a cluster, stored internally in S3
- Snapshots are incremental (only what has changed is saved)
- You can restore a snapshot into a new cluster
- Automated: every 8 hours, every 5GB, or on a schedule, Set retention
- Manual: snapshot is retained until you delete it
- You can configure Amazon Redshift to automatically copy snapshots (automated or manual) of a cluster to another AWS Region

### Cross-Region Snapshots Copy for an KMS-Encrypted Redshift

- Snapshot copy grant: Enables Redshift to perform encryption operations in the destination Region

### Redshift Spectrum

- Query data that is already in S3 without loading it
- Must have a Redshift cluster available to start the query
- The query is then submitted to thousands of Redshift Spectrum nodes

### Redshift Workload Management (WLM)

- Enables you to flexibly manage queries' priorities within workloads
- Example: prevent short, fast0running queries from getting stuck behind long-running queries
- Define multiple query queues (Superuser queue, User-defined queues)
- Route queries to the appropriate queues at runtime
- Automatic WLM - queues and resources managed by Redshift
- Manual WLM - queues and resources managed by you

### Redshift Concurrency Scaling

- Enables you to provide consistently fast performance with virtually unlimited concurrent users and queries
- Redshift automatically adds additional cluster capacity (Concurrency-Scaling Cluster) to process an increase in requests
- Ability to decide which queries sent to the concurrency-Scaling Cluster using WLM
- Charge per second

## Amazon DocumentDB

- Aurora is an "AWS-implementation" of PostgreSQL / MySQL
- DocumentDB is the same for MongoDB (which is a NoSQL database)
- MongoDB is used to store, query, and index JSON data
- Similar "deployment concepts" as Aurora
- Fully Managed, highly available with replication across 3 AZ
- DocumentDB storage automatically grows in increments of 10GB
- Automatically scales to workloads with millions of requests per second

### DocumentDB - Pricing

- Pay for what you use, no upfront costs
- On-demand Instances (per second with minimum of 10 minutes)
- Database I/O - amount of I/O used when read and write (per million I/Os)
- Database Storage (per GB/month)
- Backup Storage (per GB/month)

## Amazon Timestream

- Fully managed, fast, scalable, serverless time series database
- Automatically scales up/down to adjust capacity
- Storage and analyze trillions of events per day
- 1000s times faster & 1/10th the cost of relational databases
- Scheduled queries, multi-measure records, SQL compatibility
- Data storage tiering: recent data kept in memory and historical data kept in a cost-optimized storage
- Built-in time series analytics functions (helps you identify patterns in your data in near real-time)
- Encryption in transit and at rest
- Use cases: IoT apps, operational applications, real-time analytics,...

### Amazon Timestream - Architecture

- AWS IoT, Kinesis Data Streams -> Lambda, Prometheus, telegraf, Kinesis Data Streams, Amazon MSK -> Kinesis Data Analytics For Apache Flink -> Amazon Timestream -> Amazon QuickSight, Amazon SageMaker, Grafana, Any JDBC connection

## Amazon Athena

- Serverless query service to analyze data stored in Amazon S3
- Use standard SQL language to query the files (built on Presto)
- Supports CSV, JSON, ORC, Avro, and Parquet
- Pricing: $5.00 per TB of data scanned
- Commonly used with Amazon Quicksight for reporting / dashboards
- Use cases: Business intelligence / analytics / reporting, analyze & query VPC Flow Logs, ELB Logs, CloudTrail trails, etc...
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

- Allows you to run SQL queries across data stored in relational, non-relational, object, and custom data sources (AWS or on-premises)
- Uses Data Source Connectors that run on AWS Lambda to run Federated Queries (e.g, CloudWatch Logs, DynamoDB, RDS,...)
- Store the results back in Amazon S3

## Amazon QuickSight

- Serverless machine learning-powered business intelligence service top create interactive dashboards
- Fast, automatcially scalable, embeddable, with per-sessin pricing
- Use cases:
    - Business analytics
    - Building visualizations
    - Perform ad-hoc analysis
    - Get business insights using data
- Integrated with RDS, Aurora, Athena, Redshift, S3...
- In-memory computation using SPICE engine is data is imported into QuickSight
- Enterprise edition: Possibility to setup Column-Level security (CLS)

### QuickSight - Dashboard & Analysis

- Define Users (standard versions) and Groups (enterprise version)
    - These users & groups only exist within QuickSight, not IAM!!
- A dashboard
    - is a read-only snapshot of an analysis that you can share
    - preserves the configuration of the analysis (filtering, parameters, controls, sort)
- You can share the analysis or the dashboard with Users or Groups
- To share a dashboard, you must first publish it
- Users who see the dashboard can also see the underlying data

## Big Data Architecture

### Full Data Engineering Pipeline - Analytics layer

- S3
    - -> Amazon EMR (Hadoop / Spark / Hive)
    - -> Redshift / Redshift Spectrum (Data Warehousing)
    - -> Amazon Athena (Serverless) -> Amazon QuickSight (Visualization)

### Big Data Ingestion Pipeline

- IoT Devices -> Real-time -> Amazon Kinesis Data Streams -> Amazon Kinesis Data Firehose (Every 1 minute) -> Amazon Simple Storage Servcie (S3, Ingestion Bucket) -> Amazon Simple Queue Service (optional) -> AWS Lambda -> trigger -> Amazon Athena -> Amazon S3 (Reporting bucket) -> Amazon QuickSight -> Amazon Redshift (not serverless)

### Comparsion of warehousing technologies

- EMS
    - Need to use Big Data tools such as Apache Hive, Spark
    - One long-running cluster, many jobs, with auto-scaling, or one cluster per job?
    - Purchasing options - Spot, On Demand, Reserved Instances
    - Can access data in DynamoDB and / or S3
    - Scratch data on EBS disks (HDFS) and long term storage in S3 (EMRFS)
- Athena
    - Simple queries and aggregations, data must live in S3
    - Serverless, simple SQL queries, out-of-the-box queries for many services (cost & billing)
    - Audit queries through CloudTrail
- Redshift
    - Advanced SQL queries, must provision servers
    - Can leverage Redshift Spectrum for serverless queries on S3


