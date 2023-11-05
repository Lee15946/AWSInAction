## Amazon S3

- One of the main building blocks of AWS
- It's advertised as "infinitely scaling" storage

### Amazon S3 Use cases

- Backup and storage
- Disaster Recovery
- Archive
- Hybrid Cloud Storage
- Application hosting
- Data lakes & big data analytics
- Software delivery
- Static website

### Amazon S3 - Buckets

- Amazon S3 allows people to store objects(files) in "buckets"(directories)
- Buckets must have a globally unique name(across all regions all accounts)
- Buckets are defined at the region level
- S3 looks like a global service but buckets are created in a region
- Naming convention
    - No uppercase, No underscore
    - 3-63 characters long
    - Not an IP
    - Must start with a lowercase letter or number
    - Must NOT start with the prefix xn--

### Amazon S3 - Objects

- Objects(files) have a key
- The key is the FULL path
- The key is composed of prefix + object name
- No concept of "directories," just keys with very long names that contains slashes
- Object values are the content of the body
    - Max object size is 5TB (5000GB)
    - If uploading more than 5GB, must use "multi-part upload"
- Metadata (list of text key/value pairs - system or user metadata)
- Tags (Unicode key/value pair - up tp 10) - useful for security/lifecycle
- Version ID (if versioning is enabled)

### Amazon S3 - Security

- User-Based
    - IAM polices - which API calls should be allowed for a specific user from IAM
- Resource-Based
    - Bucket policies - bucket wide rules from the S3 console - allows cross account
    - Object Access Control List(ACL) - finer grain (can be disabled)
    - Bucket Access Control List(ACL) - less common (can be disabled)
- Note: an IAM principal can access an S3 object if:
    - The user IAM permissions ALLOW it OR the resource policy ALLOWS it
    - AND there's no explicit DENY
- Encryption: encrypt objects in Amazon S3 using encryption keys
- S3 Bucket Policies
    - JSON based policies
        - Resources: buckets and objects
        - Effect: Allow/Deny
        - Actions: Set of API to Allow or Deny
        - Principal: The account or user to apply the policy to
    - Use S3 bucket for policy to:
        - Grant public access to the bucket
        - Force objects to be encrypted at upload
        - Grant access to another account(Cross Account)
- Bucket settings for Block Public Access
    - These settings were created to prevent company data leaks
    - If you know your bucket should never be public, leave these on
    - Can be set at the account level

### Amazon S3 - Static Website Hosting

- S3 can host static websites and have them accessible on the Internet
- If you get 403, make sure the bucket policy allows public reads
- The website URL will be (depending on the region)
    - http://bucket-name.s3-webstie-aws-region.amazonaws.com
    - http://bucket-name.s3-website.aws-region.amzaonaws.com

### Amazon S3 - Versioning

- Can version your files in Amazon S3
- It's enabled at the bucket level
- Same key overwrite will change the version
- It's best practice to version your buckets
    - Protect against unintended deletes(ability to restore a version)
    - Easy roll back to previous version
- Notes:
    - Any file is not versioned prior to enabling version will have version "null"
    - Suspending versioning does not delete the previous versions

### Amazon S3 - Replication (CRR&SRR)

- Must enable Versioning in source and destination buckets
- Cross-Region Replication (CRR)
- Same-Region Replication (SRR)
- Buckets can be in different AWS accounts
- Copying is asynchronous
- Must give proper IAM permissions to S3
- Use cases:
    - CRR - compliance, lower latency access, replication across accounts
    - SRR - log aggregation, live replication between production and test accounts
- Notes:
    - After you enable Replication, only new objects are replicated
    - Optionally, you can replicate existing objects using S3 Batch Replication
        - Replication existing objects and objects that failed replication
    - For DELETE operations:
        - Can replicate delete markers from source to target (optional setting)
        - Deletions with a version ID are not replicated (to avoid malicious deletes)
    - There is no "chaining" of replication
        - If bucket 1 has replication into bucket 2, which has replication into bucket 3
        - Then objects created in bucket 1 are not replicated to bucket 3

### S3 storage classes

- Overall
    - Amazon S3 Standard - General Purpose
    - Amazon S3 Standard-Infrequent Access (IA)
    - Amazon S3 One Zone-Infrequent Access
    - Amazon S3 Glacier Instant Retrieval
    - Amazon S3 Glacier Flexible Retrieval
    - Amazon S3 Glacier Deep Archive
    - Amazon S3 Intelligent Tiering
    - Can move between classed manually or using S3 Lifecycle configurations
- S3 Durability and Availability
    - Durability
        - High durability (11 9's) of objects across multiple AZ
        - Same for all storage classes
    - Availability:
        - Measures how readily available a service is
        - Varies between on storage class
- S3 Standard - General Purpose
    - 4 9's Availability
    - Used for frequently accessed data
    - Low latency and high throughput
    - Sustain 2 concurrent facility failures
    - Use Cases: Big Data analytics, mobile&gaming applications, content distributions
- S3 Storage Classes -Infrequent Access
    - For data that is less frequently accessed, but requires rapid access when needed
    - Lower cost than S3 standard
    - Standard-IA
        - Use cases: Disaster Recovery, backups
    - One Zone-IA
        - High durability in a single AZ; data lost when AZ is destroyed
        - 99.5% Availability
- Amazon S3 Glacier Storage Classes
    - Low-cost object storage meant for archiving/backup
    - Price for storage + object retrieval cost
    - S3 Glacier Instant Retrieval
        - Millisecond retrieval, great for data accessed once a quarter
        - Minimum storage duration of 90 days
    - S3 Glacier Flexible Retrieval (formerly Amazon S3 Glacier)
        - Expedited(1 to 5 minutes), Standard (3 to 5 hours), Bulk (5 to 12 hours) - free
        - Minimum storage duration of 90 days
    - S3 Glacier Deep Archive - for long term storage
        - Standard (12 hours), Bulk(48 hours)
        - Minimum storage duration of 180 days
- S3 Intelligent-Tiering
    - Small monthly monitoring and auto-tiering fee
    - Move objects automatically between Access Tiers based on usage
    - No retrieval charges
    - Frequent Access tier (automatic): default tier
    - Infrequent Access tier (automatic): objects not accessed for 30 days
    - Archive Instance Access tier (automatic): objects not accessed for 90 days
    - Archive Access tier (optional): configurable from 90 days to 700+ days
    - Deep Archive Access tier (optional): config. from 180 days to 700+ days

## Advanced Amazon S3

### Amazon S3 - Moving between Storage Classes

- You can transition objects between storage classes
- For infrequently accessed object, move them to Standard IA
- For archive objects that you don't need fast access to, move them to Glacier or Glacier Deep Archive
- Moving objects can be automated using a Lifecycle Rules

### Amazon S3 - Lifecycle Rules

- Transition Actions - configure objects to transition to another storage class
    - Move objects to Standard IA class 60 days after creation
    - Move to Glacier for archiving after 6 months
- Expiration actions- configure objects to expire(delete) after some time
    - Access log files can be set to delete after a 365 days
    - Can be used to delete old versions of files (if versioning is enabled)
    - Can be used to delete incomplete Multi-Part uploads
- Rules can be created for a certain prefix
- Rules can be created for certain objects Tags

### Amazon S3 Analytics - Storage Class Analysis

- Help you decide when to transition objects to the right storage class
- Recommendations for Standard and Standard IA
    - Does not work for One-Zone IA or Glacier
- Report is updated daily
- 24 to 48 hours to start seeing data analysis
- Good first step to put together Lifecycle Rules (or improve them)

### S3 - Requester Pays

- In general, bucket owners pay for all Amazon S3 storage and data transfer costs associated with their bucket
- With Requester Pay buckets, the requester instead of the bucket owner pays the cost of the request and the data
  download from the bucket
- Helpful when you want to share large datasets with other accounts
- The requester must be authenticated in AWS (can not be anonymous)

### S3 Event Notifications

- Object created, removed, restore, replication...
- Object name filtering possible (*.jpg)
- Use case: generate thumbnail of images uploaded to S3
- Can create as many S3 events as desired
- S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer
- IAM permissions:
    - SNS Resource (Access) Policy
    - SQS Resource (Access) Policy
    - Lambda Resource (Access) Policy
- With Amazon EventBridge
    - Advanced filtering options with JSON rules (metadata, object size, name...)
    - Multiple Destinations - ex Step Functions, Kinesis Streams / Firehose...
    - EventBridge Capabilities - Archive, Replay Events, Reliable delivery

### S3 Performance

- Baseline Performance
    - Amazon S3 automatically scales to high request rates, latency 100-200ms
    - Your application can achieve at least 3500 PUT?COPY?POST?DELETE or 5500 GET?HEAD requests per second per prefix in
      a bucket
    - There are no limits to the number of prefixes in a bucket
    - If you spread reads across all your prefixes evenly, you can achieve 22000 requests per second for GET and HEAD
- Multi-Part upload:
    - Recommend for files > 100MB, must use for files > 5GB
    - Can help parallelize uploads (speed up transfers)
- S3 Transfer Acceleration
    - Increase transfer speed by transferring file to an AWS edge location which forward the data to the S3 bucket in
      the target region
    - Compatible with multi-part upload
- S3 Byte-Range Fetches
    - Parallelize GETs by requesting specific byte ranges
    - Better resilience in case of failures
    - Can be used to speed up downloads (request in parallel)
    - Can be used to retrieve only partial data (for example the head of a file)

### S3 Select & Glacier Select

- Retrieve less data using SQL by performing server-side filtering
- Can filter by rows&columns (simple SQL statements)
- Less network transfer, less CPU cost client-side

### S3 Object Tags & Metadata

- S3 User-Defined Object Metadata
    - When uploading an object, you can also assign metadata
    - Name-value (key-value) pairs
    - User-defined metadata names must begin with "x-amz-meta-"
    - Amazon S3 stores user-defined metadata keys in lowercase
    - Metadata can be retrieved while retrieving the objects
- S3 Object Tags
    - Key-value pairs for objects in Amazon S3
    - Useful for fine-grained permissions (only access specific objects with specific tags)
    - Useful for analytics purposes (using S3 Analytics to group by tags)
- You cannot search the object metadata or object tags
- Instead, you must use an external DB as a search index such as DynamoDB

### S3 Batch Operations

- Perform bulk operations on existing S3 objects with a single request, example:
    - Modify object metadata&properties
    - Copy objects between S3 buckets
    - Encrypt un-encrypted objects
    - Modify ACLs, tags
    - Restore objects from S3 Glacier
    - Invoke Lambda function to perform custom action on each object
- A job consists of a list of objects, the action to perform, and optional parameters
- S3 Batch Operations manages retries, tracks progress, send completion notifications, generate reports...
- You can use S3 Inventory to get object list and use S3 Select to filter you objects

## Amazon S3 Security

### Amazon S3 - Object Encryption

- You can encrypt objects in S3 buckets using one of 4 methods
- Server-Side Encryption (SSE)
    - Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3) - Enable by Default
        - Encrypts S3 objects using keys handled, managed and owned by AWS
    - Server-Side Encryption with KMS Keys stored in AWS KMS (SSE-KMS)
        - Leverage AWS Key Management Service (KMS) to manage encryption keys
    - Server-Side Encryption with Customer-Provide Keys (SSE-C)
        - When you want to manage your own encryption keys
- Client-Side Encryption
- It's important to understand which one are for which situation for the exam

### Amazon S3 Encryption - SSE-S3

- Encryption using keys handled managed, and owned by AWS
- Object is encrypted server-side
- Encryption type is AES-256
- Must set header "x-amz-server-side-encryption":"AES256"
- Enable by default for new buckets & new objects

### Amazon S3 Encryption - SSE-KMS

- Encryption using keys handled and managed by AWS KMS
- KMS advantages: user control + audit key usage using CloudTrail
- Object is encrypted server side
- Must set header "x-amz-server-side-encryption":"aws:kms"
- Limitation
    - Maybe impacted by the KMS limits
    - When upload, it calls the GenerateDataKey KMS API
    - When download, it calls the Decrypt KMS API
    - Count towards the KMS quota pert second (5500, 10000, 30000 req/s based on region)
    - Can request a quota increase using the Service Quotas Console

### Amazon S3 Encryption - SSE-C

- Server-Side Encryption using keys fully managed by the customer outside of AWS
- Amazon S3 does NOT store the encryption key you provide
- HTTPS must be used
- Encryption key must provide in HTTP headers, for every HTTP request made

### Amazon S3 Encryption - Client-Side Encryption

- Use client libraries such as Amazon S3 Client-Side Encryption Library
- Clients must encrypt data themselves before sending to Amazon S3
- Clients must decrypt data themselves when retrieving from Amazon S3
- Customer fully manages the keys and encryption cycle

### Amazon S3 - Encryption in transit (SSL/TLS)

- Encryption in flight is also called SSL/TLS
- Amazon S3 exposed two endpoints:
    - HTTP Endpoint - non encrypted
    - HTTPS Endpoint - encryption in flight
- HTTPS is recommended
- HTTPS is mandatory for SSE-C
- Force encryption is Transit: aws:SecureTransport in access policy
- Most clients would use the HTTPS endpoint by default

### Amazon S3 Encryption - DSSE-KMS

- Dual-layer Server-Side encryption
- Double encryption based on KMS

### Amazon S3- Default Encryption vs Bucket Policies

- SSE-S3 encryption is automatically applied to new objects stored in S3 bucket
- Optionally, you can "force encryption" using a bucket policy and refuse any API call to PUT an S3 object without
  encrypt ion headers (SSE-KMS or SSE-C)
- Note: Bucket Policies are evaluated before "Default Encryption"

### S3 CORS

- What is CORS?
    - Cross-Origin Resource Sharing (CORS)
    - Origin = scheme(protocol) + host(domain) + port
    - Web Browser based mechanism to allow requests to other origins while visiting the main origin
    - The results won't be fulfilled unless the other origin allows for the requests, using CORS Headers (example:
      Access-Control-Allow-Origin)
- Amazon S3 - CORS
    - If a client makes a cross-region request on our S3 bucket, we need to enable the correct CORS headers
    - You can allow for a specific origin or for * (all regions)

### Amazon S3- MFA Delete

- MFA (Multi-Factor Authentication) - force users to generate a code on a device (usually a mobile phone or hardware)
  before doing important operation on S3
- MFA will be required to:
    - Permanently delete an object version
    - Suspend Versioning on the bucket
- MFA won't be required to:
    - Enable Versioning
    - List deleted versions
- To use MFA delete. Versioning must be enabled on the bucket
- Only the bucket owner(root account) can enable/disable MFA Delete

### S3 Access Logs

- For audit purpose, you may want to log all access to S3 bucket
- Any request made to S3, from any account, authorized or denied, will be logged into another S3 bucket
- The data can be analyzed using data analysis tools...
- The target logging bucket must be in the same AWS region
- Do not set your logging bucket to be the monitored bucket
- It will create a logging loop, and your bucket will grow exponentially

### Amazon S3 - Pre-Signed URLs

- Generate pre-signed URLs using the S3 Console, AWS CLI or SDK
- URL Expiration
    - S3 Console - 1 min up to 720 minutes (12 hours)
    - AWS CLI - configure expiration with -expires-in parameter in seconds (default 3600 ses, max 604800 secs ~ 168
      hours)
- Users given a pre-signed URL inherit the permissions of the user that generated the URL for GET/PUT
- Examples:
    - Allow only logged-in users to download a premium video from your S3 bucket
    - Allow an ever-changing list of users to download files by generating URLs dynamically
    - Allow a temporary user to upload a file to a precise location in your S3 bucket

### Glacier Vault Lock & S3 Object Lock

- S3 Glacier Vault Lock
    - Adopt a WORM (Write Once Read Many) model
    - Create a Vault Lock Policy
    - Lock the policy for future edits (can no longer be changed or deleted)
    - Helpful for compliance and data retention
- S3 Object Lock (versioning must be enabled)
    - Adopt a WORM model
    - Block an object version deletion for a specified amount of time
    - Retention mode - Compliance
        - Object versions can't be overwritten or deleted by any user, including the root user
        - Objects retention modes can't be changed, and retention periods can't be shortened
    - Retention mode - Governance
        - Most users can't overwrite or delete an object version or alter it's lock settings
        - Some users have special permissions to change the retention or delete the object
    - Retention Period: protect the object for a fixed period, it can be extended
    - Legal Hold:
        - Protect the object indefinitely, independent from retention period
        - Can be freely placed and removed using the s3:PutObjectLegalHold IAM permission

### S3 Access Points

- Access Points simplify security management for S3 Buckets
- Each Access Point has:
    - Its own DNS name (Internet Origin or VPC Origin)
    - An access point policy (similar to bucket policy) - manage security at scale
- S3 - Access Point - VPC Origin
    - We can define the access point to be accessible only from within the VPC
    - You must create a VPC Endpoint to access the Access Point (Gateway or Interface Endpoint)
    - The VPC Endpoint Policy must allow access to the target bucket and Access Point

### S3 Object Lambda

- Use AWS Lambda Functions to change the object before it is retrieved by the caller application
- Only one S3 bucket is needed, on top of which we create S3 Access Point and S3 Object Lambda Access Points
- S3 Bucket -> Supporting S3 Access Point -> Redacting/Enriching Lambda Function -> S3 Object Lambda Access Point ->
  Application
- Use Cases:
    - Redacting personally identifiable information for analytics or non-production environment
    - Converting across data formats, such as converting XML to JSON
    - Resizing and watermarking images on the fly using caller-specific details, such as the user who requested the
      object