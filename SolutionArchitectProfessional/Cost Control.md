# Cost Control

## Cost Allocation Tags

### AWS Cost Allocation Tags

- With Tags we can track resources that relate to each other
- With Cost Allocation Tags we can enable detailed costing reports
- Just like Tags, but they show up as columns in Reports
- AWS Generated Cost Allocation Tags
    - Automatically applied to the resource you create
    - Starts with Prefix `aws: ` (e.g. `aws:createdBy`)
    - They're not applied to resources created before the activation
- User tags
    - Defined by the user
    - Starts with Prefix `user:`
- Cost Allocation Tags just appear in the Billing Console
- Takes up to 24 hours for the tags to show up in the report

## AWS Tag Editor

- Allows you to manage tags of multiple resources at once
- You can add/update/delete tags
- Search tagged/untagged resources in all AWS Regions

### Trusted Advisor

- No need to install anything - high level AWS account assessment
- Analyze your AWS accounts and provides recommendation
    - Cost Optimization
    - Performance
    - Security
    - Fault Tolerance
    - Service Limits
    - Operation Excellence
- Core Checks and recommendations - all customers
- Can enable weekly email notifications from the console
- Full Trusted Advisor - Available for Business & Enterprise support plans
    - Ability to set CloudWatch alarms when reaching limits
    - Programmatic Access using AWS Support API

### AWS Support Plans

- Basic Support
- Developer
- Business
    - Full set of checks + Programmatic Access using AWS Support API
- Enterprise
    - Full set of checks + Programmatic Access using AWS Support API

### Trusted Advisor - Good to know

- Can check if an S3 bucket is made public
    - But cannot check for S3 objects that are public inside of your bucket!
    - Use Amazon EventBridge / S3 Events instead / AWS Config Rules
- Service Limits
    - Limits can only be monitored in Trusted Advisor (cannot be changed)
    - Cases must be created manually in AWS Support Centre to increase limits
    - OR use the AWS Service Quotas service

## AWS Service Quotas

- Notify you when you're close to a service quota value threshold
- Create CloudWatch Alarms on the Service Quotas console
- Example: Lambda concurrent executions
- Helps you know if you need to request a quota increase or shutdown resources before limit is reached

## EC2 Launch Types & Savings Plan

### EC2 Instance Launch Types

- On Demand Instances: short workload, predictable pricing, reliable
- Spot Instances: short workloads, for cheap, can lose instances (not reliable)
- Reserved (MINIMUM 1 year)
    - Reserved Instances: long workloads
    - Convertible Reserved Instances: long workloads with flexible instances
- Dedicated Instances: no other customers will share your hardware
- Dedicated Hosts: book an entire physical server, control instance placement
    - Great for software licenses that operate at the core, or socket level
    - Can define host affinity so that instance reboots are kept on the same host

### AWS Savings Plan

- New pricing model to get a discount based on long-term usage
- Commit to a certain type of usage: ex $10 per hour for 1 to 3 years
- Any usage beyond the saving plan is billed at the on-demand price
- EC2 Instance Savings Plan (up to 72% - same discount as Standard RIs)
    - Select instance family (e.g. M5, C5...) and locked to a specific region
    - Flexible across size (m5.large to m5.4xlarge), OS (Windows to Linux), tenancy (dedicated or default)
- Compute Savings plan (up to 66% - same discount as Convertible RIs)
    - Ability to move between instance family (move from C5 to M5), region (Ireland to US), compute type (EC2, Fargate, Lambda), OS & tenancy
- SageMaker Savings plan (up to 64% off)

## S3 Cost Savings

### S3 Storage Classes

- Amazon S3 Standard - General Purpose
- Amazon S3 Standard - Infrequent Access (IA)
- Amazon S3 One Zone - Infrequent Access
- Amazon S3 Intelligent Tiering
- Amazon S3 Glacier Instant Retrieval
- Amazon S3 Glacier Flexible Retrieval
- Amazon S3 Glacier Deep Archive
- Can move between classes manually or using S3 Lifecycle configurations

### S3 - Other Cost Savings

- S3 Select & Glacier Select: save in network and CPU cost
- S3 Lifecycle Rules: transition objects between tiers
- Compress objects to save space
- S3 Requester Pays:
    - In general, bucket owners pay for all Amazon S3 storage and data transfer costs associated with their bucket
    - With Requester Pays buckets, the requester instead of the bucket owner pays the cost of the reuqest and the data download from the bucket
    - The bucket owner always pay the cost of storing data
    - Helpful when you want to share large datasets with other accounts
    - If an IAM role is assumed, the owner account of that role pays for the request

## S3 Storage Classes - Reminder

### S3 Durability and Availability

- Durability
    - High durability (99.999999999%, 11 9's) of objects across multiple AZ
    - If you store 10,000,000 objects with Amazon S3, you can on average expect to incur a loss of a single object once every 10,000 years
    - Same for all storage classes
- Availability
    - Measures how readily availability a service is
    - Varies depending on storage class
    - Example: S3 standard has 99.99% availability = not available 53 minutes a year

### S3 Standard - General Purpose

- 99.99% Availability
- Used for frequently accessed data
- Low latency and high throughput
- Sustain 2 concurrent facility failures
- Use cases: Big Data analytics, mobile & gaming applications, content distribution...¬

### S3 Storage Classes - Infrequent Access

- For data that is less frequently accessed, but requires rapid access when needed
- Lower cost than S3 Standard
- Amazon S3 Standard-Infrequent Access (S3 Standard-IA)
    - 99.9% Availability
    - Use cases: Disaster Recovery, backups
- Amazon S3 One Zone-Infrequent Access (S3 One Zone-IA)
    - High durability (11 9's) in a single AZ; data lost when AZ is destroyed
    - 99.5% Availability
    - Use cases: Storing secondary backup copies of on-premises data, or data you can recreate

### Amazon S3 Glacier Storage Classes

- Low-cost object storage meant for archiving / backup
- Pricing: price for storage + object retrieval cost
- Amazon S3 Glacier Instant Retrieval
    - Millisecond retrieval, great for data accessed once a quarter
    - Minimum storage duration of 90 days
- Amazon S3 Glacier Flexible Retrieval (formerly Amazon S3 Glacier)
    - Expedited (1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours) - free
    - Minimum storage duration of 90days
- Amazon S3 Glacier Deep Archive - for long term storage
    - Standard (12 hours), Bulk (48 hours)

### S3 Intelligent-Tiering

- Small monthly monitoring and auto-tiering fee
- Move objects automatically between Access Tiers based on usage
- There are no retrieval charges in S3 Intelligent-Tiering
- Frequent Access tier (automatic): default tier
- Infrequent Access tier (automatic): objects not accessed for 30 days
- Archive Instance Access tier (automatic): objects not accessed for 90 days
- Archive Access tier (optional): configurable from 90 days to 700+ days
- Deep Archive Access tier (optional): config from 180 days to 700+ days

## AWS Budgets & Cost Explorer

### AWS Budgets

- Create budget and send alarms when costs exceeds the budget
- 4 types fo budgets: Usage, Cost, Reservation, Savings Plans
- For Reserved Instances (RI)
    - Track utilization
    - Supports EC2, ElastiCache, RDS, Redshift
- Up to 5 SNS notifications per budget
- Can filter by: Service, Linked Account, Tag, Purchase Option, Instance Type, Region. Availabiltiy Zone, API Operation, etc...
- Same options as AWS Cost Explorer
- 2 budgets are free, then $0.02/day/budget

### Budget Actions

- Run actions on your behalf when a budget exceeds a certain cost or usage threshold
- Supports 3 action types:
    - Applying an IAM policy to a user, group, or IAM role
    - Applying Service Control Policy (SCP) to an OU
    - Stop EC2 or RDS Instances
- Actions can be executed automatically or require a workflow approval process
- Reduces unintentional overspending in your account

### Centralized Budget Management

- Budget set by Management account for Member account using account filter
- Budget threshold breach, sends a notification
- Trigger Lambda function
- Move account to restrictive OU
- Send an Email about this move

### DeCentralized Budget Management

- StackSet to deploy Budget configuration to Member accounts
- Warning Notification
- Critical Notification

### Cost Explorer

- Visualize, understand, and manage your AWS costs and usage over time
- Create custom reports that analyze cost and usage data
- Analyze your data at a high level: total costs and usage across all accounts
- Or Monthly, hourly, resource level granularity
- Choose an optimal Savings Plan (to lower prices on your bill)
- Forecast usage up to 12 months based on previous usage

## AWS Compute Optimizer

- Reduce costs and improve performance by recommending optimal AWS resources for your workloads
- Helps you choose optimal configurations and right-size your workloads (over/under provisioned)
- Use Machine Learning to analyze your resources' configurations and their utilization CloudWatch metrics
- Supported resources
    - EC2 instances
    - EC2 Auto Scaling Groups
    - EBS volumes
    - Lambda functions
- Lower your costs by up to 25%
- Recommendations can be exported to S3

### Compute Optimizer - CloudWatch Agent

- Needed to analyze Memory Utilization
- Not needed for CPU. NetworkIn/Out, DiskReadOps, DiskWriteOps,..

## EC2 Reserved Instance

- Reserved Instances in an AWS Organization
    - all accounts share the Reserved Instances and Savings Plan
    - The payer account (Management account) of an organization can turn off Reserved Instance (RI) discount and Savings Plan discount sharing for any accounts in that organization, including the payer account
- Renewal of Reserved Instances
    - You can queue (schedule and reserve ahead of time) your reserved instances
    - To renew a RI, just queue an RI purchase whenever the previous one expires




