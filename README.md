EC2
Reserved, Spot, on-Demand,Dedicated host
EC2 instance user data
EC2 instance user data is the data that you specified in the form of a bootstrap script or configuration parameters
while launching your instance.'

EC2 instance metadata
EC2 instance metadata is data about your instance that you can use to manage the instance.

EC2 Instance Type:

- General Purpose M,T,A
- Compute Optimized C
- Memory Optimized R,X,Z
- Storage Optimized I,G,H

EC2 Instances Purchasing options:

- On-Demand Instances
- Reserved (1&3 years)
    - Reserved Instances
    - Convertible Reserved instances
- Saving Plans
- Spot Instances
- Dedicated Hosts
- Dedicated Instances
- Capacity Reservation

Security Group
Only contain **allow** rules
All outbound traffic is **authorised** by default
All inbound traffic is **blocked** by default

EBS volume
It's a network drive
It's locked to an AZ
Have a provisioned capacity

- EBS snapshot
    - EBS snapshot archive tier
    - EBS recycle bin

Amazon FSx
3rd party high-performance file system on AWS

- FSx for Lustre
- FSx for Windows File Server
- FSx for NetApp ONTAP

Scalability

- Vertical scalability (scale up/down)
- Horizontal scalability (scale out/in)

High availability

- at least 2 AZ

ELB (Elastic load balancer)

- Application load balancer (HTTP/HTTPS)
    - static URL
- Network LB (TCP/UDP) layer 4
    - High performance
- Gateway LB layer 3
    - Route traffic to firewalls
    - intrusion detection

Auto Scaling Group

- scale out
- scale in
- minimum and maximum
- register new instance to LB
- replace unhealthy instances
- strategies
    - Manual scaling
    - Dynamic scaling
        - Simple/step scaling
        - Target tracking scaling
        - Scheduled scaling
        - Predictive scaling

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
### DynamoDB Global Tables
- low latency in multiple-regions
- Active-Active replication (read/write to any AWS region)

### Redshift
- Based on PostgresSQL, not used for OLTP
- It's OLAP - online analytical processing (analytics and data warehousing)
- Columnar storage of data

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

