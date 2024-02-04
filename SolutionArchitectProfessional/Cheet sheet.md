- Amazon DocumentDB clusters are deployed within an Amazon VPC, they can be accessed directly by Amazon EC2 instances or other AWS services that are deployed in the same Amazon VPC, neither the interface nor gateway is supported for DocumentDB
- You can test the failover of your Multi-AZ file system by modifying its throughput capacity for Amazon FSx
- AWS KMS doesn't support anonymous requests, as a result, any Amazon S3 bucket that allows anonymous or public access will not apply to objects that you want to serve using the Amazon S3 static website endpoint, instead of using AWS KMS encryption, use ASE-256 to encrypt your objects
- To allow public access to objects, the AWS account that owns the bucket must also own the objects
- A bucket or object is owned by the account of the AWS IAM identity that created bucket or object
- The object-ownership requirement applies to public read access granted by a bucket policy. It doesn't apply to public read access granted by the object's ACL
- For data transfer between on-premises and AWS Storage services, a single DataSync task is capable of fully utilizing 10Gbps network link
- AWS Transfer family for FTP related transfer
- S3 Intelligent-Tiering is the only cloud storage class that delivers automatic storage cost savings when data access patterns change, without performance impact or operational overhead
- Amazon EFS cannot be configured as an origin for a CloudFront distribution
- AWS Elemental MediaConvert: AWS Elemental MediaConvert is a file-based video processing service that provides scalable video processing for content owners and distributors with media libraries of any size. MediaConvert offers advanced features that enable premium content experiences, including
    - professional broadcast codecs that support increased bit depth and HDR content creation
    - still graphic overlays
    - advanced audio
    - digital rights management (DRM)
    - closed captioning support
- Use AWS Glue involves significant development efforts to write custom migration scripts to copy the database data into Redshift
- To find the ID addresses for object-level requests to Amazon S3 (uploads and downloads), you must first enable one of the following logging methods
    - Amazon S3 server access logging captures all bucket-level and object-level events
    - AWS CloudTrail data events capture the last 90 days of bucket-level events, and you can enable object-level logging
- You must perform a `RefershCache` operation to see the changes on the file share for after upload a file directly to Amazon S3 for Storage Gateway
- `ResetCache` resets all cache disks that have encountered an error, and make the disk available for reconfiguration as cache storage, this operation is only supported in the cached volume and tape gateway types
- You can use a subscription filter with Kinesis Streams, Lambda or Kinesis Data Firehose
- Interface VPC endpoints support traffic only over TCP
- AWS recommends that you disable ACLs by choosing the `bucket owner enforced` setting and use your bucket policy to share data with user outside of your account as needed
- If you use object ACLs for permission management before you applied the bucket owner-enforced setting and you didn't migrate these object ACL permissions to your bucket policy, after you re-enable ACLs, these permissions are restored. Additionaly, objects written to the bucket while the bucket owner enforced setting was applied are still owned by the bucket owner
- Apache Parquet and ORC are columnar storage formats that are optimized for fast retrieval of data and used in AWS analytical applications
- You can configure your Aurora MySQL DB cluster to publish general, slow, audit, and error log data to a log group in Amazon CloudWatch Logs. Error logs are enabled by default, but you must enable the other types of logs explicitly
- CloudTrail provides a record of actions taken by a user, role or AWS serivce in Amazon Aurora, however, CloudTrail does not capture any query activity in Aurora
- After you create an Amazon S3 bucket, up to 24 hours can pass before the bucket name propagates across all AWS Regions, during this time, you might receive the 307 Temporary Redirect response for requests to Regional endpoints that aren't in the same Region as your bucket, to avoid the 307, send request only to the Regional endpoint in the same Region as your S3 bucket, CloudFront forwards requests to the default S3 endpoint, the default endpoint is in the us-east-1 Region, If you must access Amazon S3 within the first 24 hours of creating the bucket, you can change the origin domain name of the distribution
- You need to suspend `Terminate` process which will allow you to get access to the instance without being terminated even if it is marked unhealthy, `ReplaceUnhealthy` as well
- Enabling EC2 instance termination protection (DisableApiTermination attribute) does not prevent Amazon EC2 Auto Scaling from terminating an instance
- Amazon RDS supports single Region or cross-Region automated backups
- RAID configuration options can only be used for EC2 instance-host databases
- You can monitor storage capacity and file system activity using Amazon CloudWatch, you can monitor end-user actions with file access auditing at any time (during or after the creation of a file system) via the AWS Management Console or the Amazon FSx CLI or API, you can also change the destination for publishing user access events by logging these events to CloudWatch Logs or streaming to Kinesis Data Firehose
- The EFS Standard Storage pricing is $0.3 per GB per month
- For EBS GP2 volumes, the charges are $0.10 per GB-month
- For S3 Standard storage, the pricing is $0.023 per GB per month
- AWS recommends that you should use Snowmobile to migrate large datasets of 10PB or more in a single location
- To troubleshoot an API Gateway REST API or WebSocket API that you're developing, enable execution logging and access logging to Amazon CloudWatch Logs
- AWS has a built-in tool called AWSSupportSetupIPMonitoringFromVPC that you can use to monitor metrics such as latency and the percnetage of packet loss across a network path
- The AWS Transfer Familiy provides fully managed support for rile transfers directly into and out of Amazon S3
- To use a certificate with an ALB for the same site ina different region, you must request a new ceetificate for each Region in which you plan to use it
- All the dependencies can be packaged into the single Lambda deployment package without any performance impact
- KDA only supports the following streaming sources for an application:
    - Kinesis data streams
    - Kinesis data firehose delivery stream
- You cannot use the `aws::SourceIp` condition in your IAM policies for requests to Amazon S3 through a VPC endpoint
- For RDS MySQL in Multi-AZ configuration, database engine version upgrades happen on both the primary and standby DB instances at the same time, for Aurora MySQL, all instances are upgraded at the same time
- Some key features of S3 Access Points:
    - Access Points contain a hostname, an AWS ARN, and an AWS IAM resource policy
    - Access Points by default have a specific setting to Block Public Access
    - Access Points are unique to an account and region
- For GP EBS, baseline performance scales linearly at 3 IOPS per GiB of volume size
- The maximum ration of provisioned IOPS to requested volume size (in GiB) us 50:1 for io1 volumes ,and 500:1 for io2 volumes
- By default, Amazon S3 stores object replicas using the same storage class as the source object, you can also specify a different storage calss for the replicas
- Before you transition objects from the S3 Standard or S3 Standard-IA storages classes to S3 Standard-IA or S3 One Zone-IA, you must store them at least 30 days in the S3 Standard storage class
- The S3 Intelligent-Tiering, S3 Standard-IA, and S3 One Zone-IA Storage classes have a minimum 30-day storage charge
- Proxy methods PUT/POST/PATCH/OPTIONS/DELETE go directly to the origin from the POPs and do not proxy through the regional edge caches
- Dynamic requests, as determined at request time, do not flow through regional edge caches, but go directly to the origin
- You can use message timers to set an initial invisibility period for a message added to a queue
- AWS Global Accelerator has the following types of endpoints only - NLB, ALB, EC2, Elastic IP
- To copy snapshots for AWS KMS-encrypted clusters to another AWS Region, you need to create a grant for Redshift to use a KMS Customer master key (CMK) in the destination AWS Region
- The logging destinations that you can choose from for you AWS WAF logs are
    - Amazon CloudWatch Logs
    - Amazon S3
    - Amazon Kinesis Data Firehose
- Redis authentication tokens enable Redis to require a token (password) before allowing clients to run commands, thereby improving data security
- With AWs Global Accelerator, you can shift traffic gradually or all at once, between the blue and the green environment and vic-versa without being subject to DNS caching on client devices and internet resolvers, traffic dials and endpoint weights changes are efffective within seconds
- PowerUserAccess = AdministrativeAccess - IAM
- To meet your connectivity and workload requirements, Aurora Auto Scaling dynamically adjusts the number of Aurora Replicas provisioned for an Aurora DB cluster using single-master replication
- DNS hostnames and DNS resolution are required settings for private hosted zones
- The following are the default rules for a default security group
    - Allow inbound traffic from network interfaces (and their associated instances) that are assigned to the same security group
    - Allows all outbound traffic
- 1Gbps connection at full utilization can transfer approximately 10TB of data in a day
- The AWS-ApplyPatchBaseline SSM document supports patching on Windows instances only and doesn't support Linux instances, For applying patch baselines to both Windows Server and Linux instances, the recommended SSM document is AWS-RunPatchBaseline
- With cached volumes, the AWS Volume Gateway full volume in its Amazon S3 service bucket, and just the recently accessed data is retained in the gateway's local cache for low-latency access
- With stored volumes, your entire data volume is available locally in the gateway, for fast read access
- You can use AWS DMS data validation to ensure that your data has migrated accurately from the source to the target
- You can use table metrics to capture statistics such as insert, update, delete, and DDL statements completed for the tables being migrated
- If you have multiple AWS Site-to-Site VPN connections, you can provide secure communication between sites using the AWS VPN CloudHub
- API Gateway supports payload size of only up to 10MB
- You can share manual DB snapshots with up to 20 AWS accounts, automated Amazon RDS snapshots cannot be shared directly with other AWS accounts
- Transit VPC uses customer-managed Amazon EC2 VPN instances in a dedicated transit VPC with an Internet gateway
- If VPC A and VPC B are both connected to an on-premises network using AWS Direct Connection connections, then the two VPCs can be connected to each other via AWS Direct Connect
- You can stop and restart your Elastic Beanstalk environments with the API calls terminate-environment and rebuild-environment. You can only rebuild terminated environments within six weeks (42 days) of their termination
- The `aws:PrincipalOrgID` global condition key can be used with the Principal element in a resource-based policy with AWS KMS
- You can use `aws:PrincipalIsAWSService` to check whether the call to your resource is being made directly by an AWS service principal
- IP address management (IPAM) is a core part if planning and managing the assignment and use of IP address space of a network, in order to request available CIDR block from IPAM of VPCs, you can use AWS CloudFormation Custom Resources
- The `dry-run` flag checks whether you have the required permissions for the action, without actually making the request
- Currently, you can only use Amazon SSE-S3 encryption for audit logging for Redshift
- A complete on-demand streaming solution typically makes use of Amazon S3 for storage, AWS Elemental MediaConvert for file-based video processing, and Amazon CloudFront for delivery
- VPC Flow log data can be published to Amazon CloudWatch Logs or Amazon S3 or Firehose, you can create a flow log for a VPC, subnet or network interface
- AWS PrivateLink is a highly available, scalable technology that enables you to privately connect your VPC to supported AWS services, services hosted by other AWS accounts (VPC endpoints services), and supported AWS Marketplace partner services
- AWS Elastic Beanstalk provides seversal options for how deployments are processed, including deployment policies (All at once, Rolling, rolling with additional batch, Immutable, and Traffic splitting)
- A PTR record maps an IP address to the corresponding domain name
- When you enable log file integrity validation, CloudTrail creates a hash for every log file that it delivers
- ELB access logs are stored in Amazon S3 buckets and it is not possible to directly write the logs to Kinesis Data Firehose
- You cannot detach a primary network interface from an instance
- Amazon Inspector is an automated security assessment service that helps you test the network accessibility of your Amazon EC2 instances and the seucirty state of your applications running on the instances
- Don't enable versioning for the S3 bucket you use as intermediate storage for your Amazon Redshift Target when DMS
- DMS doesn't support custom DNS names when configuring an endpoint for a Redshift cluster, and you need to use the Amazon provided DNS name
- If you use the AWS DMS console to create the endpoint, the DMS creates the required IAM roles and policies automatically, if you use the AWS CLI or AWS DMS API, you must create the IAM roles and policies manually
- AWS allow CIDR range only between /28 and /16
- While customer can place their interfaces into promiscuous mode, the hypervisor will not deliver any traffic to them that is not addressed to them
- For origins other than ELB, you must use a certificate that is signed by a trusted third-party certificate authority (CA)
- Customer routing accelerators support VPC subnet endpoints with a maximum size of /17 and route traffic only to EC2 instances within each subnet
- If you routing traffic to any AWS resources that you can create alias records for, set `Evaluate Target Health` to `Yes` instead
- By default, you can create up to 100 buckets in each of your AWS account
- For FSx, Single-AZ 2 is the latest generation of Single-AZ file systems, and it supports both SSD and HDD storage
- Route 53 must be able to establish a TCP connection with the endpoint within four seconds, in addition, the endpoint must responsd with an HTTP status code of 2xx or 3xx within two seconds after connecting
- If you choose HTTPS for the value of `Protocol` in Route53 configuration, an additional charge applies
- If you choose Yes for the value of String matching, an additional charge applies
- If you specify a non-AWS endpoint, an additional charge applies, charges for a health check apply even when the health check is disabled
- Using VPC Interface endpoint, you cannot access the internal load balancer in another VPC
- AWS Migration Portfolio Assessment (MPA) is a detailed portfolio assessment (server right-sizing, pricing, TCP comparisons, migrations cost analysis), as well as migration planning, can be done online
- You can use S3 Select to retrieve a subset of data using SQL clauses, like `SELECT` and `WHERE`, from objects stored in CSV, JSON or Apache Parquet format, it also works with objects that are compressed with GZIP of BZIP2 (for CSV and JSON objects only) and server-side encryption objects
- Using S3 Glacier Select, you can use SQL commands to query S3 Glacier archive objects that are in the uncompressed format
- ACM certificates cannot be installed on Amazon EC2 instances, an exception to this is a public ACM certificate that can be installed on Amazon EC2 instances that are connected to a Nitro Enclave
- Amazon QuickSight Enterprise edition is fully integrated with the Amazon VPC service, by creating a VPC connection in QuickSight, you're adding an elastic network interface in your VPC
- Amazon EventBridge does not support receiving S3 object replication events
- When you create an ALB in the AWS Management Console, you can optionally add an accelerator at the same time
- AWS DX provides three types of virtual interfaces
    - Public: A public virtual interface can access all AWS public services using public IP addresses
    - Private: A private virtual interface should be used to access an Amazon VPC using private IP addresses
    - Transit: A transit virtual interface should be used to access one or more Amazon VPC Transit Gateway associated with Direct Connect Gateways
- Route 53 health check can send SNS when failed
- Default FullAWSAccess policy is attached to all OUs by default if you have SCP enabled.
- Enhanced infrastructure metrics is a paid feature of Compute Optimizer that applies to Amazon EC2 instances and instances that are part of Auto Scaling groups. This recommendation preference extends the utilization metrics analysis look-back period to up to 93 days, compared to the default 14-day 2-week period.
- You can add any AWS account managed using AWS Organizations to IAM Identity Center. You need to enable all features in your organizations to manage your accounts single sign-on.
- SES
    - To set up a TLS Wrapper connection, the SMTP client connects to the Amazon SES SMTP endpoint on port 465 or 2465.
    - To set up a STARTTLS connection, the SMTP client connects to the Amazon SES SMTP endpoint on port 25, 587, or 2587, issues an EHLO command, and waits for the server to announce that it supports the STARTTLS SMTP extension.
- You can put a DeletionPolicy on any resource to control what happends when the CloudFormation template is deleted
- A managed prefix list is a set of one or more CIDR blocks. You can use prefix lists to make it easier to configure and maintain your security groups and route tables
- Client VPN is compatible with VPC Peering
- Graviton2 - 40% better price performance onver comparable 5th generation x86-based instances
    - Supports many Linux OS, Amazon Linux 2, RedHat, SUSE, Ubuntu
- Outpost rack for EC2 and S3, Outpost servers for EC2
- Permission boundary can only use for user and roles
- CloudWatch Synthetics Canary
    - Configurable script that monitor your APIs, URLs, Websites,..
- You can change your environment type to a single-instance or load-balanced, scalable environment by editing your environment's configuration in ElasticBeanstalk
- Rebuilding an environment terminates all of its resources and replaces them with new resources with the same configuration.
- An Config aggregator is an AWS Config resource type that collects AWS Config configuration and compliance data from the following:
- OpsCenter, a capability of AWS Systems Manager, provides a central location where operations engineers and IT professionals can manage operational work items
- SendTemplatedEmail , SendEmail ,SendRawEmail are email api methods used in SES
- 3 nodes are required for a DAX cluster to be fault-tolerant.
- AWS IoT Greengrass supports offline operation by enabling devices to continue processing data even when they are disconnected from the internet.
- Amazon Monitron is a machine-learning based end-to-end condition monitoring system that detects potential failures within equipment. You can use it to implement a predictive maintenance program and reduce lost productivity from unplanned machine downtime. Need internet
- Yes, you can increase the storage capacity, and increase or decrease the throughput capacity of your file system for FSx
- You can not have both users defined in AWS SSO and in Active Directory. SSO only allows for one identity source (SSO, AD or IDP)
- API Gateway supports payload size of only up to 10 MB
- DynamoDB only support Gateway endpoint
- To meet your connectivity and workload requirements, Aurora Auto Scaling dynamically adjusts the number of Aurora Replicas provisioned for an Aurora DB cluster using single-master replication.
- Permission boundary can only be assigned to user / role









``








