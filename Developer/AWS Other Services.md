## AWS Other Services

### AWS SES - Simple Email Service

- Send emails to people using
    - SMTP interface
    - Or AWS SDK
- Ability to receive email. Integrates with:
    - S3
    - SNS
    - Lambda
- Integrated with IAM for allowing to send emails

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

### ACM Private CA

- AWS Private Certificate Authority (CA)
- Manages service allows you to create private Certificate Authorities (CA), including root and subordinates CAs
- Can issue and deploy end-entity X.509 certificates
- Certificates are trusted only by your Organization (not the public Internet)
- Works for AWS services that are integrated with ACM
- Use cases:
    - Encrypted TLS communicate, Cryptographically signing code
    - Authenticate users, computers, API endpoints, and IoT devices
    - Enterprise customers building a Public Key Infrastructure (PKI)

### Amazon Macie

- Fully managed data security and data privacy service that uses machine learning and pattern matching to discover and
  protect your sensitive data in AWS
- Macie helps identify and alert you to sensitive data, such as personal identifiable information (PII)

### AWS AppConfig

- Configure, validate, and deploy dynamic configurations
- Deploy dynamic configuration changesto your applications independently of any code deployments
    - You don't need to restart the application
- Feature flags, application tuning, allow/block listing...
- Use with apps on EC2 instances, Lambda, ECS, EKS...
- Gradually deploy the configuration and rollback if issues occur
- Validate configuration changes before deployment using:
    - JSON Schema (syntactic check) or
    - Lambda Function - run code to perform validation (semantic check)

### CloudWatch Evidently

- Safely validate new features by serving them to a specified % of your users
    - Reduce risk and identity unintended consequences
    - Collect experiment data, analyze using stats, monitor performance
- Launches (=feature flags): enable and disable features for a subset of users
- Experiments (=A/B testing): compare multiple versions of the same feature
- Overrides: pre-define a variation for a specific user
- Store evaluation events in CloudWatch Logs or S3
