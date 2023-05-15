EC2
Reserved, Spot, on-Demand,Dedicated host
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

Lambda
ECS
EKS
ECR
LightSail
CloudWatch

**Think resource performance monitoring, events, and alerts; think CloudWatch.**

Config

CloudTrail
**Think account-specific activity and audit; think CloudTrail.**
Management Event, Data event, Insight Event 


Amazon Elastic Transcoder
transcode

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

AWS CloudHSM

AWS Wavelength ： 5G

AWS Direct connect
AWS LocalZone

Amazon EMR

AWS Batch

AWS Elastic Beanstalk: Web applications

Internet Gateway

Amazon Lightsail 

AWS Acceptable Use Policy

Global Accelerator

Amazon MQ

Amazon Kinesis data stream

AWS Storage Gateway
File,tape,volume

Amazon Kendra


Amazon Personalize
real-time personalized recommendations

Amazon Comprehend
**natural-language processing (NLP) service**
uses machine learning to find insights and relationships in a text


Amazon Lex
lifelike conversational interactions.

AWS Knowledge Center 

AWS CodeDeploy

AWS Cost and Usage Reports (AWS CUR) 

**Just remember that only S3 and DynamoDB support VPC Endpoint Gateway.**
Others are VPC endpoint interface

AWS Glue
Discover, prepare, and combine data for analytics, machine learning, and application development

Basic/Developer/Business/Enterprise

Enterprise: TAM, AWS Concierge service

Business: Contextual guidance based on customer use-case,

AWS Firewall Manager

AWS PrivateLink
AWS PrivateLink provides private connectivity between VPCs and services hosted on AWS or on-premise

Amazon Quicksight - Amazon QuickSight is a scalable, serverless, embeddable, machine learning-powered business intelligence (BI) service built for the cloud.

AWS Quick Starts - Template for CloudFormation

CloudEndure Disaster Recovery

AWS codeStar
Code Commit,Code build, Code Deploy, Code pipeline integrated, Cloud9

Amazon WorkSpace
Amazon WorkSpaces is a managed, secure Desktop-as-a-Service (DaaS) solution

AWS Sumerian


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
AWS EdgeLocation


AWS Management Console
AWS CLI
AWS SDK

AWS CloudTrail


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

- Object storage: 
  - Simple storage service (11 9 Durability) Regional but global accessible
    - Key (reference path), Metadata, Data
    - Bucket (global unique)
    - Access policy
    - Storage class
      - S3 standard, at least 3 AZs
      - S3 IA (Infrequent access) less storage fee, more retrieve fee
      - S3 IA Single Zone, less storage fee
      - S3 Intelligent Tier, a littler monitoring and automation fee
      - S3 Glacier, retrieve objects minutes to hours
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
  - Amazon QLDB
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
  - AWS Organisation
    - OU (Organisation unit)
    - SCP (Service control policy)
  - AWS Artifact
  - AWS customer compliance center
  - AWS WAF (Reject rule Distributed Denial-of-Service (DDoS))
  - AWS Shield
  - AWS Inspector (Security assess)
  - AWS KMS (Key management service)
  - AWS GuardDuty (account threat detected)
    - VPC flowLog
    - DNS
    - CloudTrail insights

- Monitoring and Analyze
  - CloudWatch (metrics, threshold, alert) (on-cloud and on-prem)
  - CloudTrail (Audit API Request)
    - Event: What, Who, When, How
  - Amazon Trusted Advisor
    - cost optimisation
    - service limit
    - performance
    - security
    - fault tolerance

- Pricing
  - Pricing calculator
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
    - Performance Efficiency
    - Cost optimisation
    - Sustainability
  - AWS cloud practitioner
