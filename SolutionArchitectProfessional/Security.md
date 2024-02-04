# Security

## CloudTrail

### AWS CloudTrail

- Provides governance, compliance and audit for your AWS Account
- CloudTrail is enabled by default!
- Get a history of events/ API calls made within your AWS Account by:
    - Console
    - SDK
    - CLI
    - AWS Services
- Can put logs from CloudTrail into CloudWatch Logs or S3
- A trail can be applied to All Regions (default) or a single Region
- If a resource is deleted in AWS,investigate CloudTrail first!

### CloudTrail Events

- Management Events
    - Operations that are performed on resources in your AWS account
    - Examples:
        - Configuring security (IAM `AttachRolePolicy`)
        - Configuring rules for routing data (Amazon EC2 `CreateSubnet`)
        - Setting up logging (AWS CloudTrail `CreateTrail`)
    - By default, trails are configured to log management events
    - Can separate Read Events (that don't modify resources) from Write Events (that may modify resources)
- Data Events:
    - By default, data events are not logged (because high volume operations)
    - Amazon S3 object-level activity (ex: `GetObject`, `DeleteObject`, `PutObject`): can sperate Read and Write Events
    - AWS Lambda functions execution activity (the Invoke API)
- CloudTrail Insights Events

### CloudTrail Insights

- Enable CloudTrail Insights to detect unusual activity in your account
    - inaccurate resource provisioning
    - hitting service limits
    - Bursts of AWS IAM actions
    - Gaps in periodic maintenance activity
- CloudTrail Insights analyzes normal management events to create a baseline
- And then continuously analyzes write events to detect unusual patterns
    - Anomalies appear in the CloudTrail console
    - Event is sent to Amazon S3
    - An EventBridge event is generated (for automation needs)

### CloudTrail Events Retention

- Events are stored for 90 days in CloudTrail
- To keep events beyond this period, log them to S3 and use Athena

## CloudTrail - Event Bridge Integration

### Amazon EventBridge - Intercept API Calls

- User -> `DeleteTable` API Call -> DynamoDB -> Log API call -> CloudTrail (any API call) -> event -> Amazon
  EventBridge -> alert -> SNS

### Amazon EventBridge + CloudTrail

- User -> `AssumeRole` -> IAM Role -> API Call logs -> CloudTrail -> event -> EventBridge -> SNS
- Uer -> edit SG inbound Rules (`AuthorizeSecurityGroupIngress`) -> EC2 (Security Group) -> API Call logs ->
  CloudTrail -> event -> EventBridge -> SNS

## CloudTrail - SA Pro

### CloudTrail - Solution Architecture: Delivery to S3

- CloudTrail -> Every 5 minutes -> SSE-S3 (default) or SSE-KMS -> S3 -> Lifecycle Policy -> Glacier
  -> Delivery notifications -> SNS -> SQS / Lambda  
  -> S3 Events -> SQS, SNS, Lambda
- S3 Enhancements:
    - Enable Versioning
    - MFA Delete Protection
    - S3 Lifecycle Policy (S3 IA, Glacier...)
    - S3 Object Lock
    - SSE-S3 or SSE-KMS encryption
    - Feature to perform CloudTrail Log File Integrity validation (SHA-256 for hashing and signing)

### CloudTrail - Solution Architecture: Multi Account, Multi Region Logging

- Account A (CloudTrail) + Account B (CloudTrail) -> Security Account (S3 + S3 Bucket Policy)
- Observations:
    - The S3 bucket policy is necessary for cross-account delivery
    - If Account A wants to access its CloudTrail files:
        - Option 1: create a cross-account role and assume the role
        - Option 2: edit the bucket policy

### CloudTrail - Solution Architecture: Alert for API calls

- CloudTrail -> stream -> CW Logs -> Metric Filters -> CW Alarm -> SNS
- Log filter metrics can be used to detect a high level of API happening
- Ex: Count occurrences of EC2 `TerminateInstances` API
- Ex: Count of API calls per user
- Ex: Detect high level of Denied API calls

### CloudTrail - Solution Architecture: Organization Trail

- The Organizational Trail is created in the management account

### CloudTrail: How to react to events the fastest?

- Overall, CloudTrail may take up to 15 minutes to deliver events
- EventBridge:
    - Can be triggered for any API call in CloudTrail
    - The fastest, most reactive way
- CloudTrail Delivery in CloudWatch Logs:
    - Events are streamed
    - Can perform a metric filter to analyze occurrences and detect anomalies
- CloudTrail Delivery in S3:
    - Events are delivered every 5 minutes
    - Possibility of analyzing logs integrity, deliver cross account, long-term storage

## KMS

### AWS KMS (Key Management Service)

- Anytime you hear "encryption" for an AWS service, it's most likely KMS
- Easy way to control access to your data, AWS manages keys for us
- Fully integrated with IAM for authorization
- Seamlessly integrated into:
    - Amazon EBS: encrypt volumes
    - Amazon S3: Server-side encryption of objects
    - Amazon Redshift: encryption of data
    - Amazon RDS: encryption of data
    - Amazon SSM: Parameter store
    - Etc...
- But you can also use the CLI / SDK

### KMS - KMS Key Types

- Symmetric (AES-256 keys)
    - First offering of KMS, single encryption key that is used to Encrypt and Decrypt
    - AWS services that are integrated with KMS use Symmetric KMS keys
    - Necessary for envelope encryption
    - You nevert get access to the KMS key unencrypted (must call KMS API to use)
- Asymmetric (RSA & ECC key pairs)
    - Public (Encrypt) and Private Key (Decrypt) pair
    - Used for Encrypt/Decrypt, or Sign/Verify operations
    - The public key is downloadable, but you can't access the Private Key unencrypted
    - Use case: encryption outside of AWS by users who can't call the AWS API

### Type of KMS Keys

- Customer Managed Keys
    - Create, manage and use, can enable or disable
    - Possibility of rotation policy (new key generated every year, old key preserved)
    - Can add a Key Policy (resource policy) & audit in CloudTrail
    - Leverage for envelope encryption
- AWS Managed Keys
    - Used by AWS service (aws/s3, aws/ebs, aws/redshift)
    - Managed by AWS (automatically rotated every 1 year)
    - View Key Policy & audit in CloudTrail
- AWS Owned Keys
    - Created and managed by AWS, use by some AWS services to protect your resources
    - Used in multiple AWS accounts, but they are not in your AWS account
    - You can't view, use, track or audit

### KMS KEy Material Origin

- Identified the source of the key material in the KMS key
- Can't be changed after creation
- KMS (AWS_KMS) - default
    - AWS KMS creates and manages the key material in its own key store
- External (EXTERNAL)
    - You import the key material into the KMS key
    - You're responsible for securing and managing this key material outside of AWS
- Custom Key Store (AWS_CLOUDHSM)
    - AWS KMS creates the key material in a custom key store (CloudHSM Cluster)

### KMS Key Source - Custom Key Store (CloudHSM)

- IntegrateKMS with CloudHSM cluster as a Custom Key Store
- Key material are stored in a CloudHSM cluster that you own and manage
- The cryptographic operations are performed in the HSMs
- Use cases:
    - You need direct control over the HSMs
    - KMS keys needs to be stored in a dedicated HSMs
- AWS KMS -> Custom Key Store Connector -> CloudHSM Cluster in VPC (At least 2 active HSMs)

### KMS Key Source - External

- Import your own key material into KMS key, Bring Your Own Key (BYOK)
- You're responsible for key material's security, availability, and durability outside of AWS
- Must be 256-bit Symmetric key (Asymmetric is NOT supported)
- Can't be used with Custom Key Store (CloudHSM)
- Manually rotate you KMS key (Automatic Key Rotation is NOT supported)

1. Create KMS key (SYMMETRIC & EXTERNAL)
2. Download (Public Key & Import Token)
3. Encrypt Key Material
4. Import (Encrypted Key Material & Import Token)

### KMS Mutli-Region Keys

- A set of identical KMS keys in different AWS Regions that can be used interchangeably (~ same KMS key in multiple
  Regions)
- Encrypt in one Region and decrypt in other Regions (No need to re-encrypt or making cross-Region API calls)
- Multi-Region keys have the same key ID, key material, automatic rotation
- KMS Multi-Region are NOT global (Primary + Replicas)
- Each Mutli-Region key is managed independently
- Only one primary key at a time, can promote replicas into their own primary
- Use cases: Disaster Recovery, Global Data Management (e.g, DynamoDB Global Tables), Active-Active Applications that
  span multiple Regions, Distributed Signing applications,...

## Parameter Store

### SSM Parameter Store

- Secure storage for configuration and secrets
- Optional Seamless Encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations / secrets
- Security through IAM
- Notifications with Amazon EventBridge
- Integration with CloudFormation
- Applications -> Encrypted/Plaintext configuration -> SSM Parameter Store -> Check IAM permissions -> Decryption
  Service -> AWS KMS

### SSM Parameter Store Hierarchy

- /my-department/
    - my-app/
        - dev/
            - db-url
            - db-password
        - prod/
            - db-url
            - db-password
    - other-app/
- other-department/
- /aws/reference/secretsmanager/secret_ID_in_Secrets_Manager
- /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 (public)
- `GetParamters` or `GetParamtersByPath` API

### Standard and advanced parameter tiers

- Standard
    - 10000 number of parameters allowed (per AWS account and Region)
    - 4KB maximum size of parameter value
    - No parameter polcieis available
    - No additional charge
    - Free storage pricing
- Advanced
    - 100000 number of parameters allowed (per AWS account and Region)
    - 8KB maximum size of parameter value
    - Parameter policies available
    - Charges apply
    - $0.05 per advanced parameter per month

### Parameters Policies (for advanced parameters)

- Allow to assign a TTL to a parameter (expiration date) to force updating or deleting sensitive data such as passwords
- Can assign multiple policies at a time
- Expiration (to delete a parameter)
- ExpirationNotification (EventBridge)
- NoChangeNotification (EventBridge)

## Secrets Manager

### AWS Secrets Manager

- Meant for storing secrets (e.g, passwords, API keys,...)
- Capability to force rotation of secrets every X days
    - Automate generation of secrets on rotation (use Lambda)
    - Natively supports Amazon RDS (all supported DB engines), Redshift, DocumentDB
    - Support other databases and service (custom Lambda function)
- Control access to secrets using Resource-based Policy
- Integration with other AWS Services to natively pull secrets from Secrets Manager: CloudFormation, CodeBuild, ECS,
  EMR, Fargate, EKS, Parameter Store
- ECS Task -> Pull at boot -> Secret (Database Password) -> Inject secret as envxironment variable -> access RDS

### Secrets Manager - with CloudFormation

- Secret is generated
- Reference secret in RDS DB instance
- Link the secret to RDS DB instance

### Secrets Manager - Sharing Across Accounts

- Share using KMS Policy
    - "Action": "kms:Decrypt"
    - "KMS:ViaService": "secretsmanager.{region}.amazonaws.com"
- Share using Resource-based Policy
    - "Action": "secretsmanager:GetSecretValue"

### SSM Parameter Store vs Secrets Manager

- Secrets Manager ($$$):
    - Automatic rotation of secrets with AWS Lambda
    - Lambda function is provided for RDS, Redshift, DocumentDB
    - KMS encryption is mandatory
    - Can integration with CloudFormation
- SSM Parameter Store ($):
    - Simple API
    - No secret rotation (can enable rotation using Lambda triggered by EventBridge)
    - KMS encryption is optional
    - Can integration with CloudFormation
    - Can pull a Secrets Manager secret using the SSM Parameter Store API

### SSM Parameter Store vs. Secrets Manager Rotation

- AWS Secrets Manager
    - AWS Secrets Manager -> every 30 days -> invoke -> Lambda Function (can be provided) -> change password -> Amazon
      RDS
- SSM Parameter Store
    - Amazon EventBridge -> every 30 days -> invoke -> Lambda function -> changed password in RDS & change value in SSM
      Parameter Store

## RDS Security

- KMS encryption at rest for underlying EBS volumes / snapshots
- Transparent Data Encryption (TDE) for Oracle and SQL Server
- SSL encryption to RDS is possible for all DB (in-flight)
- IAM authentication for MySQL, PostreSQL and MariaDB
- Authorization still happens within RDS (not in IAM)
- Can copy an un-encrypted RDS snapshot into an encrypted one
- CloudTrail cannot be used to track queries made within RDS

## SSL Encryption, SNI & MITM

### SSL/TLS - Basics

- SSL refers to Secure Sockets Layer, used to encrypt connections
- TLS refers to Transport Layer Security, which is a newer version
- Nowadays, TLS certificates are mainly used, but people still refer as SSL

- Public SSL certificates are issued by Certificate Authorities (CA)
- Comodo, Symantec, GoDaddy, GlobalSign, Digicert, Letsencrypt, etc...

- SSL certificates have an expiration date (you set) and must be renewed

### SSL Encryption - How it works

- Asymmetric Encryption is expensive (SSL)
- Symmetric encryption is cheaper
- Asymmetric handshake is used to exchange a per-client random symmetric key
- Possibility of client sending an SSL certificate as well (two-way certificate)

1. Client sends hello, cipher suits & random
2. Server response with server random & SSL certificate (Public Key)
3. Client verifies SSL certificate
4. Master key (symmetric) generated and sent encrypted using the Public Key
5. Server verifies Client SSL cert (optional)
6. Master key is decrypted using Private Key
7. Secure Symmetric Communication in Place

### SSL - Server Name Indication (SNI)

- SNI solves the problem of loading multiple SSL certificates onto one web server (to serve multiple websites)
- It's a "newer" protocol, and requeires the client to indicate the hostname of the target server in the initial SSL
  handshake
- The server will then find the correct certificate, or return the default one
- Note
    - Only works for ALB & NLB (newer generation), CloudFront
    - Does not work for CLB (older gen)

### SSL - Man in the Middle Attacks

- User -> HTTP -> Pirate Server (can intercept packets) -> HTTP -> Good Server
- User (If infected, the user may trust the "private SSL certificate") -> HTTPS -> Pirate Server (Send fake SSL cert to
  User Decrypts and re-encrypt packets) -> HTTPS -> Good Server

### SSL - Man in the Middle Attack - How to prevent

- Don't use public-facing HTTP, use HTTPS (meaning, use SSL/TLS certificates)
- Use a DNS that has DNSSEC
    - To send a client to a pirate server, a DNS response needs to be "forged" by a server which intercepts them
    - It is possible to protect your domain name by configuring DNSSEC
    - Amazon Routes 53 supports DNSSEC for domain registration
    - Route 53 supports DNSSEC for DNS servie as of December 2020 (using KMS)
    - You could also run a custom DNS server on Amazon EC2 for example (Bind is the most popular, dnsmasq, KnotDNS,
      PowerDNS)

## AWS Certificate Manager (ACM)

- To host public SSL certificates in AWS, you can:
    - Buy your own and upload them using the CLI
    - Have ACM provision and renew public SSL certificates for you (free of cost)
- ACM loads SSL certificates on the following integrations:
    - Load Balancers (including the one created by EB)
    - CloudFront distributions
    - APIs on API Gateways
- SSL certifiactes is overall a pain to manually manage, so ACM is greate to leverage in your AWS infrastructure!
- Public www HTTPS Request -> SSL termination -> Provision and Maintain Cert (ACM) -> Private AWS HTTP request (LEss CPU
  cost in EC2, Thanks to SSL termination for the ELB)

### ACM - Good to know

- Possibility of creating public certificates
    - Must verify public DNS
    - Must be issued by a trusted public certificate authority (CA)
- Possibility of creating private certificates
    - For your internal applications
    - You create your onw private CA
    - Your applications must trust your private CA
- Certificate renewal
    - Automatically done if generated provisioned by ACM
    - Any manually uploaded certificates must be renewed manually and re-uploaded
- ACM is reginal service
    - To use with a global application (multiple ALB for example), you need to issue and SSL certificate in each region
      where you application is deployed
    - You cannot copy certs across regions

## CloudHSM

- KMS => AWS manages the software for encryption
- CloudHSM => AWS provisions encryption hardware
- Dedicated Hardware (HSM = Hardware Security Module)
- You manage your own encryption keys entirely (not AWS)
- HSM device is tamper resistant, FIPS I 140-2 Level 3 compliance
- NO free tier available
- Must use the CloudHSM Client Software
- Redshift supports CloudHSM for database encryption and key management
- Good option to use with SSE-C encryption

### CloudHSM Diagram

- CloudHSM Client -> SSL Connection (User manages the Keys) -> AWS CloudHSM (AWS manages the Hardware)
- IAM permissions:
    - CRUD an HSM Cluster
- CloudHSM Software:
    - Manage the Keys
    - Manage the Users

### CloudHSM - High Availability

- CloudHSM clusters are spread across Multi AZ (HA)
- Great for availability and durability
- CloudHSM Client -> CloudHSM 1 (Availability Zone 1)
  -> CloudHSM 2 (Availability Zone 2)

### CloudHSM vs. KMS

- AWS KMS
    - Tenancy
        - Multi-Tenant
    - Standard
        - FIPS 1402-2 Level 3
    - Master Keys
        - AWS Owned Keys
        - AWS Manager Keys
        - Customer Manager KMS Keys
    - Key Types
        - Symmetric
        - Asymmetric
        - Digital Signing
    - Key Accessibility
        - Accessible in multiple AWS regions
        - KMS Key Replication
    - Cryptographic Acceleration
        - None
    - Access & Authentication
        - AWS IAM
    - High Availability
        - AWS Managed Service
    - Audi Capability
        - CloudTrail
        - CloudWatch
    - Free Tier
        - Yes
- AWS CloudHSM
    - Tenancy
        - Single-Tenant
    - Standard
        - FIPS 1402-2 Level 3
    - Master Keys
        - Customer Manager CMK
    - Key Types
        - Symmetric
        - Asymmetric
        - Digital Signing & Hashing
    - Key Accessibility
        - Deployed and managed in a VPC
        - Can be shared across VPCs (VPC Peering)
    - Cryptographic Acceleration
        - SSL/TLS Acceleration
        - Oracle TDE Acceleration
    - Access & Authentication
        - You create users and manage their permissions
    - High Availability
        - Add multiple HSMs over different AZs
    - Audi Capability
        - CloudTrail
        - CloudWatch
        - MFA support
    - Free Tier
        - No

## Solution Architecture - SSL on ELB

### Solution Architecture: SSL on ALB

- User -> HTTPS -> ALB with SSL cert from ACM -> HTTP -> ASG

### Solution ArchitectureL: SSL on web server EC2 instances

- User -> TCP -> NLB -> HTTPS -> ASG -> Retrieve SSL private at EC2 boot time (user data), Install certs on EC2 -> SSM
  Parameter store (IAM permissions)
- Performing SSL encryption / decryption can use CPU resources

### Solution Architecture: CloudHSM - SSL Offloading

- You can offloadSSL to CloudHSM (SSL Acceleration)
- Supported by NGINX, Apache Web servers and IIS for Windows Server
- Extra security: the SSL private key never leaves the HSM device
- Must setup a cryptographic user (CU) on the CloudHSM device
- User -> TCP -> NLB -> HTTPS -> ASG -> SSL offloading -> CloudHSM (mutli-AZ)

## S3 Security

### S3 Encryption for Objects

- SSE-S3: encrypts S3 objects using keys handled & managed by AWS
- SSE-KMS: leverage KMS to manage encryption keys
    - Key usage appears in CloudTrail
    - objects made public can never be read
    - On `s3:PutObject`, make the permission `kms:GenerateDataKey` is allowed
- SSE-C: when you want to manage your own encryption keys
- Client-Side Encryption
- Glacier: all data is AES-256 encrypted, key under AWS control

### Encryption in transit (SSL/TLS)

- Amazon S3 exposes:
    - HTTP endpoint: non encrypted
    - HTTPS endpoint: encryption in flight
- You're free to use the endpoint you want, but HTTP is recommended
- HTTPS is mandatory for SSE-C
- To enforce HTTPS, use a Bucket Policy with `aws:SecureTransport`

### S3 Security

- S3 Access Logs:
    - Detailed records for the requests that are made to bucket
    - Might take hours to deliver
    - Might be incomplete (best effort)
- S3 Events Notifications:
    - Receive notifications when certain events happen in your bucket
    - E.g: new objects created, object removal, restore objects, replication events
    - Destinations: SNS, SQS queue, Lambda
    - Typically delivered in seconds but can take minutes, notification for every object if versioning is enabled, else
      risk of one notification for two same object write done simultaneously
- Trusted Advisor
    - Check the bucket permission (is the bucket public)
- Amazon EventBridge
    - Need to enable CloudTrail object level logging on S3 first
    - Target can be Lambda, SQS, SNS, etc...

### S3 Security

- User based
    - IAM policies - which API calls should be allowed for a specific user from IAM console
- Resource Based
    - Bucket Policies - bucket wide rules from the S3 console - allows cross account
    - Object Access Control List (ACL) - fine grain
    - Bucket Access Control List (ACL) - less common

### S3 Bucket Policies

- Use S3 bucket for policy to:
    - Grant public access to the bucket
    - Force objects to be encrypted at upload
    - Grant access to another account (Cross Account)
- Optional Conditions on:
    - SourceIp: Public IP or Elastic IP | VpcSourceIp: Private IP (through VPC Endpoint)
    - SourceVPC or SourceVPCEndpoint - only works with VPC Endpoints
    - CloudFront Origin Identity
    - MFA

### S3 pre-signed URLs

- Can generate pre-signed URLs using SDK or CLI
    - For downloads (easy, can use the CLI)
    - For uploads (harder, must use the SDK)
- Valid for a default of 3600 seconds, can change timeout with `--expires-in [TIME_BY_SECONDS]` argument
- User given a pre-signed URL inherit the permissions of the person who generated the URL for GET/PUT
- Examples:
    - Allow only logged-in users to download a premium video on your S3 bucket
    - Allow an ever changing list of users to download files by generating URLs dynamically
    - Allow temporarily a user to upload a file to a precise location in our bucket

### VPC Endpoint Gateway for S3

- Public Instance -> Internet Gateway -> Public www -> S3 Bucket (Bucket policy by `AWS:SourceIp` (public IP))
- Private Instance -> private -> VPC Endpoint Gateway -> S3 Bucket (Bucket policy by `AWS:SoucreVpce` (one or few
  endpoints) OR `AWS:SourceVpc` (encompass all possible VPC endpoints))

### S3 Object Lock $ Glacier Vault Lock

- S3 Object Lock
    - Adopt a WORM (Write Once Read Many) model
    - Block an object version deletion for a specified amount of time
- Glacier Vault Lock
    - Adopt a WORM (Write Once Read Many) model
    - Lock the policy for future edits (can no longer be changed)
    - Helpful for compliance and data retention

## S3 Access Points

### S3 - Access Points

- Access Points simplify security management for S3 Buckets
- Each Access Point has:
    - its onw DNS name (Internet Origin or VPC Origin)
    - an access point policy (similar to bucket policy) - manage security at scale
- Users (Finance) -> Finance Access Point (Policy Grant R/W to /finance prefix)
- Users (Sales) -> Sales Access Point (Policy Grant R/W to /sales prefix)
- Users (Analytics) -> Sales Analytics Point (Policy Grant R to entire bucket)
- S3 Bucket (Simple Bucket Policy)
    - /finance/...
    - /sales/...

### S3 -Access Points - VPC Origin

- We can define the access point to be accessible only from within the VPC
- You must create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint)
- The VPC Endpoint Policy must allow access to the target bucket and Access Point

## S3 Multi-Region Access Points

### S3 Multi-Region Access Points

- Provide a global endpoint that span S3 buckets in multiple AWS regions
- Dynamically route requests to the nearest S3 bucket (lowest latency)
- Bi-directional S3 bucket replication rules are created to keep data in sync across regions
- Failover Controls - allows you to shift requests across S3 buckets in differenct AWS regions within minutes (
  Active-Active or Active-Passive)
- Application -> request S3 object -> S3 Multi-Region Access Point -> Request routed to AWS region with the lowest
  latency

### Multi-Region Access Points - Failover Controls

- Active/Passive Failover Controls
- Works with active/active or active/passive setups

## S3 Object Lambda

- Use AWS Lambda Functions to change the object before it is retrieved by the caller application
- Only one S3 bucket is needed, on top of which we create S3 Access Point and S3 Object Lambda Access Points
- Use Cases:
    - Redacting personally identifiable information for analytics or non-prodution environments
    - Converting across data formats, such as converting XML to JSON
    - Resizing and watermarking images on the fly using caller-speicifc details, such as the user who requested the
      object
- E-Commerce Application -> Original Object
- Analytics Application -> Redacted Object -> S3 Object Lambda Access Point -> Redacting Lambda Function -> Supporting
  S3 Access Point
- Marketing Application -> Enriched Object -> S3 Object Lambda Access Point -> Enriching Lambda Function -> Customer
  Loyalty Database

## DDos and AWS Shield

### Type of Attacks on your infrastructure

- Distributed Denial of Service (DDoS):
    - When your service is unavailable because it's receiving too many requests
    - SYN Flood (Layer 4): send too many TCP connection requests
    - USD Reflection (Layer 4): get other servers to send many big UDP requests
    - DNS flood attack: overwhelm the DNS so legitimate users can't find the site
    - Slow Loris attack: a lot of HTTP connections are opened and maintained
- Application level attacks:
    - more complex, more specific (HTTP level)
    - Cache bursting strategies: overload the backed database by invalidating cache

### DDoS Protection on AWS

- AWS Shield Standard: protects against DDoS attach for your website and applications, for all customers at no
  additional costs
- AWS Shield Advanced: 24/7 premium DDoS protection
- AWS WAF: Filter specific requests based on rules
- CloudFront and Route 53:
    - Availability protection using global edge network
    - Combined with AWS Shield, provides DDoS attack mitigation at the edge
- Be ready to scale - leverage AWS Auto Scaling
- Separate static resources (S3/CloudFront) from dynamic ones (EC2/ALB)

### AWS Shield

- AWS Shield Standard
    - Free service that is activated for every AWS customer
    - Provides protection from attacks such as SYN/UDP Floods, Reflection attack and other layer 3/layer 4 attacks
- AWS Shield Advanced
    - Optional DDoS mitigation service ($3000 per month per organization)
    - Protect against more sophisticated attack on Amazon EC2, Elastic Load Balancing (ELB), Amazon CloudFront, AWS
      Global Accelerator and Route 53
    - 24/7 access to AWS DDoS response team (DRP)
    - Protect against higher fees during usage spike due to DDoS

## AWS WAF - Web Application Firewall

- Protects your web applications from common web exploits (Layer 7)
- Deploy on Application Load Balancer (localized rules)
- Deploy on API Gateway (rules running at the regional or edge level)
- Deploy on CloudFront (rules globally on edge locations)
    - Used to front other solution: CLB, EC2 instances, custom origins, S3 websites
- Deploy on AppSync (protect your GraphQL APIs)
- WAF is not for DDoS protection
- Define Web ACL (Web Access Control List)
    - Rules can include IP addresses, HTTP headers, HTTP body, or URI strings
    - Protects from common attack - SQL injection and Cross-Site Scripting (XSS)
    - Size constraints, Geo match
    - Rate-based rules (to count occurrences of events)
- Rule Actions: Count | Allow | Block | CAPTCHA

### AWS WAF - Managed Rules

- Library of over 190 managed rules
- Ready-to-use rules that are managed by AWS and AWS Marketplace Sellers
- Baseline Rule Groups - general protection from common threats
    - AWSManagedRulesCommonRuleSet, AWSManagedRulesAdminProtectionRuleSet,...
- Use-case Specific Rule Groups - protection for many AWS WAF use cases
    - AWSManagedRulesSQLRuleSet, AWSManagedRulesWindowsRuleSet, AWSManagedRulesPGPRuleSet,
      AWSManagedRulesWordPressRuleSet,...
- IP Reputation Rule Groups - block requests based on source (e.g. malicious IPs)
    - AWSManagedRulesAmazonIpReputationList, AWSManagedRulesAnonymousIpList
- Bot Control Managed Rule Group - block and manage requests from bots
    - AWSManagedRulesBotControlRuleSet

### WAF - Web ACL - Logging

- You can send your logs to an:
    - Amazon CloudWatch Logs log group - 5 MB per second
    - Amazon Simple Storage Service (Amazon S3) bucket - 5 minutes interval
    - Amazon Kinesis Data Firehose - limited by Firehose quotas
        - Amazon S3
        - Amazon Redshift
        - Amazon OpenSearch

### Solution Architecture - Enhance CloudFront Origin Security with AWS WAF & AWS Secret Manager

- Users -> AWS WAF (WACL) -> Amazon CloudFront -> Customer HTTP Header (X-Origin-Verify: xxxxxxx) -> AWS WAF (filtering
  Rule) -> Application Load Balancer -> Auto Scaling Group (EC2 instances)
- AWS Secrets Manager (auto-rotate) -> invoke -> Lambda Function -> Update Cutom HTTP Header with the new value ->
  Filtering Rule & Amazon CloudFront

## AWS Firewall Manager

- Manage rules in all accounts of an AWS Organization
- Security policy: common set of security rules
    - WAF rules (Application Load Balancer, API Gateways, CloudFront)
    - AWS Shield Advanced (ALB, CLB, NLB, Elastic IP, CloudFront)
    - Security Groups for EC2, Application Load Balancer and ENI resources in VPC
    - AWS Network Firewall (VPC Level)
    - Amazon Route53 Resolver DNS Firewall
    - Policies are created at the region level
- Rules are applied to new resources as they are created (good for compliance) across all and future accounts in your
  Organization

### WAF vs. Firewall Manager vs. Shield

- WAF, Shield and Firewall Manager are used together for comprehensive protection
- Define your Web ACL rules in WAF
- For granular protection of your resources, WAF alone is the correct choice
- If you want to use AWS WAF across accounts, accelerate WAF configuration, automate the protection of new resources,
  use Firewall Manager with AWS WAF
- Shield Advanced adds additional features on top of AWS WAF, such as dedicated support from the Shield Response Team (
  SRT) and advanced reporting
- If you're prone to frequent DDoS attacks, consider purchasing Shield Advanced

## Blocking an IP address

- Client -> VPC (NACL) -> Security Group -> EC2 Instance Public IP + Optional Firewall Software in EC2
- Client -> VPC (NACL) -> ALB Security Group -> Application Load Balancer Connection Termination -> EC2 Security
  Group -> EC2 Instance Private IP
- Client -> VPC (NACL) -> NLB Security Group -> Application Load Balancer Connection Termination -> EC2 Security
  Group -> EC2 Instance Private IP
- Client -> VPC (NACL) -> ALB Security Group -> WAF IP address filtering -> EC2 Security Group -> EC2 Instance Private
  IP
- Client -> CloudFront Geo Restriction -> WAF IP address filtering -> CloudFront Public IPs -> VPC (NACL not helpful) ->
  ALB Security Group -> Public ALB -> EC2 Security Group -> EC2 Instance Private IP

## Amazon Inspector

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
- Reporting & Integration with AWS Security Hub

### What does Amazon Inspector evaluate?

- Remember: only for EC2 instances, Container Images & Lambda functions
- Continuous scanning of the infrastructure, only when needed
- Package vulnerabilities (EC2, ECR & Lambda) - database of CVE
- Network reachability (EC2)
- A risk score is associated with all vulnerabilities for prioritization

## AWS Config

- Helps with audting and recording compliance if your AWS resources
- Helps record configurations and changes over time
- AWS Config Rules does not prevent actions from happening (no deny)
- Questions that can be solved by AWS Config:
    - Is there unstricted SSH access to my security groups?
    - Do my buckets have any public access?
    - How has my ALB configuration changed over time?
- You can receive alerts (SNS notifications) for any changes
- AWS Config is a per-regions service
- Can be aggregated across regions and accounts

### AWS Config Resource

- View compliance of a resource over time
- View configuration of a resource over time
- View CloudTrail API calls if enabled

### AWS Config Rules

- Can use AWS managed config rules (over 75)
- Can make cusotm config rules (must be defined in AWS Lambda)
    - Evaluate if each EBS disk is of type gp2
    - Evaluate if each EC2 instance is t2.micro
- Rules can be evaluated / triggered:
    - For each config change
    - And / or: at regular time intervals
- Trigger Amazon EventBridge if the rule is non-compliant (chain with Lambda)
- Rules can have auto remediations through SSM Automations
    - If a resource is not compliant, you can trigger an auto remediations
    - Ex: remediate security group rules, stop instances with non-approved tags

## AWS Managed Logs

- Load Balancer Access Logs (ALB, NLB, CLB) => to S3
    - Access logs for your Load Balancers
- CloudTrail Logs => to S3 and CloudWatch Logs
    - Logs for API calls made within your account
- VPC Flow Logs => to S3, CloudWatch Logs, Kinesis Data Firehose
    - Information about IP traffic going to and from network interfaces in your VPC
- Route 53 Access Logs => to CloudWatch Logs
    - Log information about the queries that Route 53 receives
- S3 Access Logs => to S3
    - Sever access logging provdes detailed records for the reuqests that are made to a bucket
- CloudFront Access Logs => to S3
    - Detailed information about every user request that CloudFront receives
- AWS Config => to S3

## Amazon GuardDuty

- Intelligent Threat discovery to protect your AWS Account
- Use Machine Learning algorithms, anomaly detection, 3rd party data
- One click to enable (30 days trail), no need to install software
- Input data includes:
    - CloudTrail Events Logs - unusual API calls, unauthorized deployments
        - CloudTrail Management Events - create VPC subnet, create trail,...
        - CloudTrail S3 Data Events - get object, list objects, delete object...
    - VPC Flow Logs - unusual internal traffic, unusual IP address
    - DNS Logs - compromised EC2 instances sending encoded data within DNS queries
    - Optional Features - EKS Audit Logs, RDS& Aurora, EBS, Lambda, S3 Data Events...
- Can setup EventBridge rules to be notified in case of findings
- EventBridge rules can target AWS Lambda or SNS
- Can protect against CryptoCurrency attacks (has a dedicated "finding" for it)

### GuardDuty - Delegated Administrator

- AWS Organization member accounts can be designated to be a GuardDuty Delegated Administrator
- Have full permission to enable and manage GuardDuty for all accounts in the Organization
- Can be done only using the Organization Management Account

## IAM Advanced Policies

### IAM Conditions

- `aws:SourceIp` restrict the client IP from which the API calls are being made
- `aws:RequestedRegion` restrict the region the API calls are made to
- `ec2:ResourceTag`, `aws;PrincipalTag` restrict based on tags
- `aws:MultiFactorAuthPresent` to force MFA

### IAM for S3

- `s3:ListBucket` permission applies to `arn:aws:s3:::test` => bucket level permission
- `s3:GetObject`, `s3:PutObject`, `s3:DeleteObject` applies to `arn:aws:s3:::test/*` => object level permission

### Resource Policies & aws:PrincipalOrgID

- `aws:PrincipalOrgID` can be used in any resource policies to restrict access to accounts that are member of an AWS
  Organization

## EC2 Instance Connect

### EC2 Instance Connect (SendSSHPublicKey API)

- User (IPv4: 1.2.3.4) -> EC2 Console, EC2 Instance Connect CLI -> EC2 Instance Connect API -> `SendSSHPublicKeyAPI` (
  push one-time SSH public key (valid for 60 secs)) -> EC2 Instance (Security Group) -> Inbound Rules (Allow AWS IP
  Range)
- All connections are logged in CloudTrail

## AWS Security Hub

- Central security tool to manage security across several AWS accounts and automate security checks
- Integrated dashboards showing current security and compliance status to quickly take actions
- Automatically aggregates alerts in predefined or personal findings formats from various AWS services & AWS partner
  tools
    - Config
    - GuardDuty
    - Inspector
    - Macie
    - IAM Access Analyzer
    - AWS Systems Manager
    - AWS Firewall Manager
    - AWS Health
    - AWS Partner Network Solutions
- Must first enable the AWS Config Service
- Security Hub (Multi account) -> Collect potential issues & findings
    - -> generate EventBridge event
    - -> Automated checks -> Security Hub Findings
    - -> investigate -> Amazon Detective

## Amazon Detective

- GuardDuty, Macie and Security Hub are used to identify potential security issues, or findings
- Sometimes security findings require deeper analysis to isolate the root cause and take action - it's complex process
- Amazon Detective analyzes, investigates, and quickly identifies the root cause of security issues or suspicious
  activities (using ML and graphs)
- Automatically collects and processes events from VPC Flow Logs, CloudTrail, GuardDuty and creat a unified view
- Produces visualizations with details and context to get to the root cause



















