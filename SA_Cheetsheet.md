- AWS recommends that you should use AWS DataSync to migrate existing data to Amazon S3
- Upgrades to the database engine level required downtime, Even if your RDS DB instance uses a Multi-AZ deployment, both
  the primary and standby DB instances are upgraded at the same time
- An ElasticIP can only be attached to one EC2 instance at a time
- An instance profile is a container for a role that can be attached to an Amazon EC2 when launched, an instance profile
  can contain only one role, and that limit cannot be increased
- You can use CloudFront signed URLs and signed cookies to control who can access your content
- When the new AMI is copied from region A into region B, it automatically creates a snapshot in region B because AMIs
  are base`d on the underlying snapshots
- Amazon Kinesis Data Firehose is a fully managed services for delivering real-time streaming data to destinations such
  as Amazon S3, Amazon Redshift, Amazon OpenSearch Service, Splunk and any custom HTTP endpoint or HTTP endpoints owned
  by supported third-party service providers, including Datadog, Dynatrace, LogicMonitor, MongoDB, New Relic and Sumo
  Logic
- The minimum storage duration is 30 days before you can transition objects from S3 Standard to S3 Standard or One
  Zone-IA
- Spot block can only be used for a span of up to 6 hours, you should also note that AWS has stopped offering Spot
  blocks to new customers
- With S3TA, you pay only for transfers that are accelerated
- Global Accelerator is a good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT, or Voice iver IP, as well as
  for HTTP use cases that specifically require static IP addresses or deterministic, fast regional failover
- Amazon EC2 Auto Scaling does not immediately terminate instances with an Impaired status. Instead, Amazon EC2 Auto
  Scaling waits a few minutes, for instance, to recover
- Sharing resources from a central location instead of building them in each VPC may reduce administrative overhead ana
  cost, named "shared services VPC"
- Transit VPC uses customer-managed Amazon Elastic Compute Cloud (Amazon EC2) VPN instances in a dedicated transit VPC
  with an internet gateway
- When a Kinesis data stream is configured as the source of a Firehose delivery stream, FirehosePutRecord and
  PutRecordBatch operations are disabled an Kinesis Agent cannot write to Firehose delivery stream directly
- Route53 Latency routing policy - Use when you have resources in multiple AWS Regions and you want to route traffic to
  the region that provides the best latency
- DNS hostnames and DNS resolution are required settings for private hosted zones
- If you have private and public hosted zones that have overlapping namespaces, then the Resolver routes traffic based
  on the most specific match
- If you have a private hosted zone and a Resolver rule that routes traffic to your netowkr for the same domain name,
  the Resolver rule takes precedence
- By default, an S3 object is owned by the AWS account that uploaded it, to get access to the data files, an AWS IAM
  role with cross-account permissions must run the UNLOAD command again
- RDS stores the DB snapshots in the Amazon S3 bucket belonging to the same AWS region where the RDS instances is
  located. RDS stores these on your behalf and you do not have direct access to these snapshots in S3
- You cannot conver an exiting single-region key to a multi-region key, this design ensures that all data protected with
  existing single-region keys maintain the same data residency and data sovereignty properties
- AWS Resource Access Manager is a service that enables you to easily and securely share AWS resources with any AWS
  account or within your AWS Organization, You can share AWS Transit Gateways, Subnets, AWS License Manager
  configurations, and Amazon Route 53 Resolver rules resoures with RAM
- Delay queues let you postpone the delivery of new messages to a queue for several seconds, the default delay for a
  queue is 0 seconds, the maximum is 15 minutes
- If you have objects that are smaller than 1 GB or if the data set is less than 1 GB in size, you should consider using
  AMazon CloudFront's PUT?POST commands for optimal performance
- If the spot request is persistent, the request is opend again after your Spot instances is interrupted, If the request
  is persistent and you stop yout Spot Instance, the request only opens after you start your Spot Instance
- Trust policies define which principal entities (accounts, users, roles and federated users) can assume the role. The
  IAM service supports only one type of resource-based policy called a role trust policy, which is attahced to an IAM
  role
- Amazon EventBridge is recommended when you want to build an application that reacts to events from SaaS applications
  and/or AWS services
- You can configure your Lambda function to pull in additional code and content in the form of layers. A layer is a ZIP
  archive that contains libraries, a custom runtime, or other dependencies. A function can use up to 5 layers at a time
- For cross-account permissions to other AWS accounts or uses in another account, you must use a bucket policy
- You can configure a sinle CloudFront web distribution to serve difference types of requests from multiple origins
- Filed-level encryption allows you to enable your uses to securely upload senssitive information to your web servers.
  You can encrypt up to 10 data fields in a request
- AWS S3 console cannot be used to copy 1PB of data from one bucket to another as it's not feasible,
- IAM database authentication works with MySQL and PostgreSQL
- Amazon S3 supports the following destinations where it can publish event: Amazon SNS, Amazon SQS, AWS Lambda
- You can not set up multiple consumers for Kinesis Data Firehose delivery streams as it can dump data in a single data
  repository at a time
- You can not have applications consuming data streams from Kinesis Data Firehose
- RAID 5 and 6 are not recommended for Amazon EBS because the parity wreite operations of these RAID modes consume some
  of the IOPS available to your volumes
- For each VPC that you want to associate with the Route53 hosted zone, change the following VPC settings to true:
  enableDnsHostnames, enableDnsSupport
- For replication to operate effectivelyt, each read replica should have the same amount of compute and storage
  resources as the resource DB instance. If you scale the source DB instance, als scale the read replicas
- When you use server-side encryption with Amazon S3 managed keys (SSE-S3), each object is encrypoted with a unique key,
  As an additional safegurad, it encrypts the key itself with a root key that it regularly rotates
- Amazon EBS fast snapshot restore enables you to create a volume from a snapshot that is fully initalized at creation
- An on-demand capacity reservation is a type of Amazon EC2 reservation that enables you t ocreate and manage reserved
  capacity on Amazon Ec2, you can specify the Region and AZ where you want to reserve capacity
- Use AWS SSM State Manager to set up automatic start and stop for RDS
- AWS Backup retention time is 1-35 days
- Transfer family for FTP, FTPS, SFTP
- When the state of an Amazon Machine Image changes, Amazon EC2 generates an event that is sent to Amazon EventBridge
- S3 Standard-IA and S3 One Zone-IA have a minimum storage duration charge of 30 days. S3 Standard-IA and S3 One Zone-IA
  also have retrieval charges
- Kinesis Data Analytics cannot directly ingest data from the source as it ingests data either from Kinesis Data Streams
  or Kinesis Data Firehose
- Once you version-enable a bucket, it can never return to an unversioned state. Versioning can only be suspended once
  it has been enabled.
- You can put an instance that is in the InService state into the Standby state, update some software or troubleshoot
  the instance, and then return the instance to service.
- Firehose cannot directly write into a DynamoDB table
- The minimum storage duration is 30 days before you can transition objects from S3 Standard to S3 One Zone-IA.
- FSx for Lustre integrates with Amazon S3, making it easy to process data sets with the Lustre file system
- By default, an S3 object is owned by the AWS account that uploaded it. This is true even when the bucket is owned by
  another account
- RDS stores these on your behalf and you do not have direct access to these snapshots in S3
- ASG default termination policy: Allocation strategy for the On-demand or Spot instances -> Oldest launch
  configuration/template -> Closest to next billing hour
- With ACLs, you can only grant other AWS accounts (not specific users) access to your Amazon S3 resources
- If you have objects that are smaller than 1GB or if the data set is less than 1GB in size, you should consider using
  Amazon CloudFront's PUT/POST commands for optimal performance
- NAT instance support port forwarding
- To view all categories of instance metadata from within a running instance, use the following IPv4 or IPv6 URIs.
    - http://169.254.169.254/latest/meta-data/
- 3iops per GB for GP EBS
- Using VPC sharing, an account that owns the VPC (owner) shares one or more subnets with other accounts (participants)
  that belong to the same organization from AWS Organizations.
- AWS CloudFormation StackSet extends the functionality of stacks by enabling you to create, update, or delete stacks
  across multiple accounts and regions with a single operation.
- AWS Resource Access Manager (RAM) is a service that enables you to easily and securely share AWS resources with any
  AWS account or within your AWS Organization
- Depending on your Region, your Amazon S3 website endpoints follow one of these two formats.
    - s3-website dash (-) Region ‐ http://bucket-name.s3-website.Region.amazonaws.com
    - s3-website dot (.) Region ‐ http://bucket-name.s3-website-Region.amazonaws.com
- Auto Scaling group lifecycle hooks enable you to perform custom actions as the Auto Scaling group launches or
  terminates instances
- Redis has purpose-built commands for working with real-time geospatial data at scale.
- DynamoDB are encrypted by default
- Use reboot if instance check failed, use recovery if system check failed for cloudwatch alarm action
- If you specify targets using an instance ID, traffic is routed to instances using the primary private IP address
  specified in the primary network interface for the instance.
- If you specify targets using IP addresses, you can route traffic to an instance using any private IP address from one
  or more network interfaces
- Storage class analysis does not give recommendations for transitions to the ONEZONE_IA or S3 Glacier storage classes.
- AWS Compute optimizer does not recommend instance purchase options.
- AWS S3 console cannot be used to copy 1PB of data from one bucket to another as it's not feasible.
- Amazon EventBridge is recommended when you want to build an application that reacts to events from SaaS applications
  and/or AWS services
- Both AWS Snowball Edge Storage Optimized and AWS Snowball Edge Compute Optimized offer the storage clustering feature.
- A function can use up to 5 layers at a time.
- . You can now package and deploy Lambda functions as container images.
- Using AWS Firewall Manager, you can centrally configure AWS WAF rules, AWS Shield Advanced protection, Amazon Virtual
  Private Cloud (VPC) security groups, AWS Network Firewalls, and Amazon Route 53 Resolver DNS Firewall rules across
  accounts and resources in your organization
- Using the Range HTTP header in a GET Object request, you can fetch a byte-range from an object, transferring only the
  specified portion
- Please note that with Amazon S3 Select, you can scan a subset of an object by specifying a range of bytes to query
  using the ScanRange paramete
- AWS recommends using Snowmobile to migrate large datasets of 10PB or more in a single location.
- AWS recommends snowball only if you want to transfer greater than 10 TB of data between your on-premises data centers
  and Amazon S3.
- Dynamic port mapping with an Application Load Balancer makes it easier to run multiple tasks on the same Amazon ECS
  service on an Amazon ECS cluster.
- When you stop, hibernate, or terminate an instance, every block of storage in the instance store is reset.
- EMR is used for launching Hadoop / Spark clusters
- If you are going to use the role with Amazon EC2 or another AWS service that uses Amazon EC2, you must store the role
  in an instance profile
- ELB cannot distribute incoming traffic for targets deployed in different regions.
- Temporary queues help you save development time and deployment costs when using common message patterns such as
  request-response.
- You can create up to five read replicas from one DB instance.
- we do not recommend customers use AWS Site to Site VPN as a backup for AWS Direct Connect connections with speeds
  greater than 1 Gbps.
- There are two possible values for the x-amz-server-side-encryption header: AES256, which tells S3 to use S3-managed
  keys, and aws:kms, which tells S3 to use AWS KMS–managed keys.
- Multi-Attach is supported exclusively on Provisioned IOPS SSD volumes.
- Amazon S3 server-side encryption uses one of the strongest block ciphers available, 256-bit Advanced Encryption
  Standard (AES-256), to encrypt your data
- When you use server-side encryption with Amazon S3 managed keys (SSE-S3), each object is encrypted with a unique key.
- Both ElastiCache for Redis and ElastiCache for Memcached are HIPAA Eligible.
- The Amazon VPC console wizard provides the following four configurations:
    - VPC with a single public subnet
    - VPC with public and private subnets (NAT)
    - VPC with public and private subnets and AWS Site-to-Site VPN access
    - VPC with a private subnet only and AWS Site-to-Site VPN access
- Compute Optimizer helps you choose the optimal Amazon EC2 instance types, including those that are part of an Amazon
  EC2 Auto Scaling group, based on your utilization data.
- Redis authentication tokens enable Redis to require a token (password) before allowing clients to execute commands,
  thereby improving data security.
- ElastiCache supports IAM Authentication for Redis
- VPC sharing (part of Resource Access Manager) allows multiple AWS accounts to create their application resources such
  as EC2 instances, RDS databases, Redshift clusters, and Lambda functions, into shared and centrally-managed Amazon
  Virtual Private Clouds (VPCs).
- It is not possible to modify a launch configuration once it is created.
- Kinesis Each shard allows for 1 MB/s incoming data and 2 MB/s outgoing data
- Kinesis Data Firehose is now supported for SNS, but not Kinesis Data Streams.
- KMS may can only rotate every one year
- the maxsize of CIDR is 16, min size is 28
- 5 reserved IPs in each VPC, 0, 1, 2, 3, 255
- Only Network Load Balancer provides both static DNS name and static IP. While, Application Load Balancer provides a static DNS name but it does NOT provide a static IP
- You can not create encrypted Read Replicas from an unencrypted RDS DB instance.
- DynamoDB Streams enable DynamoDB to get a changelog and use that changelog to replicate data across replica tables in other AWS Regions.
- You cannot directly integrate Cognito User Pools with CloudFront distribution as you have to create a separate Lambda@Edge function to accomplish the authentication via Cognito User Pools.
-  If you have an existing Identity Provider (IdP), you can use an API Gateway Lambda authorizer to invoke a Lambda function to authenticate/validate a given user against your IdP. 
- Amazon RDS supports Cross-Region Automated Backups. Manual snapshots and Read Replicas are also supported across multiple Regions.
- 






