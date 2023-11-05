## AWS Security & Encryption

### Encryption 101

- Encryption in flight (SSL)
    - Data is encrypted before sending and decrypted after receiving
    - SSL certificates help with encryption (HTTPS)
    - Encryption in flight ensures no MITM(man in the middle attack) can happen
- Server side encryption at rest
    - Data is encrypted after being received by the server
    - Data is decrypted before being sent
    - It is stored in an encrypted from thanks to a key (usuallyt a data key)
    - The encryption/decryption keys must be managed somewhere and the server must have access to it
- Client side encryption
    - Data is encrypted by the client and never decrypted by the server
    - Data will be decrypted by a receiving client
    - The server should not be able to decrypt the data
    - Could leverage Envelope Encryption

### AWS KMS (Key Management Service)

- Anytime you hear "encryption" for an AWS service, it's most likely KMS
- AWS manages encryption keys for us
- Fully integrated with IAM for authorization
- Easy way to control access to your data
- Able to audit KMS Key usage using CloudTrail
- Seamlessly integrated into most AWS services (EBS, S3, RDS, SSM...)
- Never ever store your secrets in plaintext, especially in your code!
    - KMS Key Encryption is also available through API calls(SDK, CLI)
    - Encrypted secrets can be stored in the code/environment variables
- KMS Keys Types
    - KMS Keys is the new name of KMS Customer Master Key
    - Symmetric (AES-256 keys)
        - Single encryption key that is used to Encrypt and Decrypt
        - AWS services that are integrated with KMS use Symmetric CMKs
        - You never get access to the KMS Key unencrypted (must call KMS API to use)
    - Asymmetric (RSA & ECC key paris)
        - Public (Encrypt) and Private key (Decrypt) pair
        - Used for Encrypt/Decrypt, or Sign/Verify operations
        - The public key is downloadable, but you can't access the private key unencrypted
        - Use case: encryption outside of AWS by users who can't call the KMS API
- AWS KMS (Key Management Service)
    - Types of KMS Keys:
        - AWS Owned Keys(free): SSE-S3, SS3-SQS, SSE_DDB(default key)
        - AWS Managed Key: free (aws/service-name, example: aws/rds or aws/ebs)
        - Customer managed keys created in KMS: $1/month
        - Customer managed keys imported (must be symmetric key): $1/month
        - pay for API calls to KMS ($0.03/10000 calls)
    - Automatic Key rotation:
        - AWS-managed KMS Key: automatic every 1 year
        - Customer-managed KMS Key: (must be enabled) automatic every 1 year
        - Imported KMS Key: only manual rotation possible using alias
- Copying Snapshots across regions
    - EBS volume encrypted with KMS Key A
    - EBS Snapshot encrypted with KMS Key A
    - KMS ReEncrypt with KMS Key B
    - EBS Snapshot Encrypted with KMS Key B
    - EBS Volume Encrypted with KMS Key B
- KMS Key Policies
    - Control access to KMS keys, "similar" to S3 bucket policies
    - Difference: you cannot control access without them
    - Default KMS Key Policy
        - Created if you don't provide a specific KMS Key Policy
        - Complete access to the key to the root user = entire AWS account
    - Custom KMS Key Policy
        - Define users, roles that can access the KMS key
        - Define who can administer the key
        - Useful for cross-account access of your KMS key
- Copying Snapshots across accounts
    - Create a Snapshot encrypted with your own KMS Key (customer managed key)
    - Attach a KMS key policy to authorize cross-account access
    - Share the encrypted snapshot
    - (in target) Create a copy of the snapshot, encrypt in with a CMK in your account
    - Create a volume from the snapshot

### KMS Encryption Patterns and Envelope Encryption

- API - Encrypt and Decrypt
    - Encrypt API -> KMS -> Check IAM permissions -> Perform encryption -> Send encrypted secret (<4KB)
    - Encrypt API -> KMS -> Check IAM permissions -> Perform decryption -> Send decrypted secret (<in plaintext)
- Envelope Encryption
    - KMS Encrypt API call has a limit of 4 KB
    - If you want to encrypt > 4KB, we need to use Envelope Encryption
    - The main API that will help us is the `GenerateDataKeyAPI`
    - For the exam: anything over 4KB of data that needs to be encrypted must use the Envelope
      Encryption == `GenerateDataKey` API
    - Encrypt
        - Call GenerateDataKey API -> Generate Data Key -> Send plaintext data key and encrypted data key -> Client side
          encryption using DEK
    - Decrypt
        - Decrypt API -> Decrypt data key using CMK -> Send plaintext data key -> Client side decryption using DEK
- Encryption SDK
    - The AWS Encryption SDK implemented Envelope Encryption for us
    - The Encryption SDK also exists as a CLI tool we can install
    - Implementations for Java, Python, C, JavaScript
    - Feature - Data Key Caching
        - re-use data keys instead of creating new ones for each encryption
        - Helps with reducint the number of calls to KMS with a security trade-off
        - Use LocalCryptoMaterialsCache (max age, max bytes, max number of messages)

### KMS Symmetric - API Summary

- Encrypt: encrypt up to 4KB of data through KMS
- GenerateDataKey: generates a unique symmetric data key (DEK)
    - returns a plaintext copy of the data key
    - AND a copy that is encrypted under the CMK that you specify
- GenerateDataKeyWithoutPlaintext:
    - Generate a DEK to use at some point (not immediately)
    - DEK that is encrypted under the CMK that you specify (must use Decrypt later)
- Decrypt: decrypt up to 4KB of data (including Data Encryption Keys)
- GenerateRandom: Returns a random byte string

### KMS Request Quotas

- When you exceed a request quota, you get a `ThrottlingException`
- To respond, use exponential backoff (backoff and retry)
- For cryptographic operations, they share a quota
- This includes requests made by AWS on your behalf (ex:SSE-KMS)
- For GenerateDataKey, consider using DEK caching from the Encryption SDK
- You can request a Request Quotas increase through API or AWS support

### S3 Bucket Key

- S3 Bucket Key for SSE-KMS encryption
    - New settings to decrease
        - Number of API calls made to KMS from S3 by 99%
        - Costs of overall KMS encryption with Amazon S3 by 99%
    - This leverages data keys
        - A "S3 bucket key" is generated
        - That key is ued to encrypt KMS objects with new data keys
    - You will see less KMS CloudTrail events in CloudTrail

### KMS Key Policies & IAM Principals

- Default KMS Key Policy allow every user in account to perform action if you get permissions
- Principal Options in IAM Policies
    - AWS Account and Root User
    - IAM Roles
    - IAM Role Sessions
    - IAM Users
    - Federated User Sessions
    - AWS Services
    - All Principals

### CloudHSM Overview

- KMS => AWS manages the software for encryption
- CloudHSM => AWS provisions encryption hardware
- Dedicated Hardware (HSM = Hardware Security Module)
- You manage your own encryption keys entirely (not AWS)
- HSM device is tamper resistant, FIPS 140-2 Level 3 compliance
- Supports both symmetric and asymmetric encryption (SSL/TLS keys)
- No free tier available
- Must use the CloudHSM Client Software
- Redshift supports CloudHSM for database encryption and key management
- Good option to use with SSE-C encryption

### CloudHSM Diagram

- CloudHSM Client -> SSL Connection (Use manages the Keys) -> AWS CloudHSM (AWS manages the Hardware)
- IAM permissions:
    - CRUD and HSM Cluster
- CloudHSM Software:
    - Manage the Keys
    - Manage the Users

### CloudHSM - High Availability

- CloudHSM clusters are spread across Multi AZ (HA)
- Great for availability and durability

### CloudHSM - Integration with AWS Services

- Thtough integrations with AWS KMS
- Configure KMS Custom Key Store with CloudHSM
- Example: EBS, S3, RDS

### CloudHSM vs KMS

- AWS KMS
    - Tenancy: Multi-Tenant
    - Standard: FIPS 140-2 Level 3
    - Master Keys
        - AWS Owned CMK
        - AWS Managed CMK
        - Customer Managed CMK
    - Key Types
        - Symmetric
        - Asymmetric
        - Digital Signing
    - Key Accessibility: Accessible in multiple AWS regions (can't access keys outside the region it's created in)
    - Cryptographic Acceleration: None
    - Access & Authentication: AWS IAM
    - High Availability: AWS Managed Service
    - Audit Capability
        - CloudTrail
        - CloudWatch
    - Free tier: Yes
- AWS CloudHSM
    - Tenancy: Single-Tenant
    - Standard: FIPS 140-2 Level 3
    - Master Keys
        - Customer Managed CMK
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
    - Access & Authentication: You create users and manage their permissions
    - High Availability: Add multiple HSMs over different AZs
    - Audit Capability
        - CloudTrail
        - CloudWatch
        - MFA support
    - Free tier: No

### SSM Parameter Store

- Secure storage for configuration and secrets
- Optional Seamless Encryption using KMS
- Serverless, scalable, durable, easy SDK
- Version tracking of configurations/secrets
- Security through IAM
- Notifications with Amazon EventBridge
- Integration with CloudFormation
- SSM Parameter Store Hierarchy
    - /department
        - my-app/
            - dev/
                - db-url
                - dd-password
    - /aws/reference/secretsmanager/secret_ID_in_Secrets_Manager
    - /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 (public)
- Standard and advanced parameter tiers
    - Standard
        - Total number of parameters allowed(per AWS account and Region): 10000
        - Maximum size of a parameter value: 4KB
        - Parameter policies available: No
        - Cost: No additional charge
        - Storage Pricing: Free
    - Advanced
        - Total number of parameters allowed(per AWS account and Region): 100000
        - Maximum size of a parameter value: 8KB
        - Parameter policies available: Yes
        - Cost: Charges apply
        - Storage Pricing: $0.05 per advanced parameter per month
- Parameter Policies (for advanced parameters)
    - Allow to assign a TTL to a parameter (expiration data) to force updating or deleting sentitive data such as
      passwords
    - Can assign multiple policies at a time
    - Expiration (to delete a parameter)
    - ExpirationNotification (EventBridge)
    - NoChangeNotification (EventBridge)

### AWS Secrets Manager

- Newer service meant for strong secrets
- Capability to force rotation of secrets every X days
- Automate generation of secrets on rotation (uses Lambda)
- Integration with Amazon RDS (MySQL, PostgreSQL, Aurora)
- Secrets are encrypted using KMS
- Mostly meant for RDS integration
- AWS Secrets Manager - Multi-Region Secrets
    - Replicate Secrets across multiple AWS Regions
    - Secrets Manager keeps read replicas in sync with the primary Secret
    - Ability to promote a read replica Secret to a standalone Secret
    - Use cases: multi-region apps, disaster recovery strategies, multi-region DB...

### Secrets Manager - CloudFormation Integration

- ManageMasterUserPassword - creates admin secret implicitly
- RDS, Aurora will manage the secret in Secrets Manager and its rotation
- Dynamic Reference
    - Generate secret
    - Reference secret in RDS DB instance
    - Link the secret to RDS DB instance

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
- Rotation
    - AWS Secrets Manager
        - Every 30 days -> Invoke Lambda Function (can be provided) -> Change RDS password
    - SSM Parameter Store
        - Every 30 days -> EventBridge -> Invoke Lambda Function -> Change password in RDS & Change value in SSM
          Parameter Store

### CloudWatch Logs - Encryption

- You can encrypt CloudWatch logs with KMS keys
- Encryption is enabled at the log group level, by associating a CMK with a log group, either when you create the log
  group ir after it exists
- You cannot associate a CMK with a log group using the CloudWatch console
- You must use the CloudWatch Logs API:
    - `assocaite-kms-key`: if the log group already exists
    - `create-log-group`: if the log group doesn't exist yet

### CodeBuild Security

- To access resources in your VPC, make sure you specif a VPC configuration for your CodeBuild
- Secrets in CodeBuild
- Don't store them as plaintext in environment variables
- Instead...
    - Environment variables can reference parameter store parameters
    - Environment variables can reference secrets manage secrets

### AWS Nitro Enclaves

- Process highly sensitive data in an isolated compute environment
    - Personally Identifiable Information (PII), healthcare, financial,...
- Fully isolated virtual machines, hardened, and highly constrained
    - Not a container, not persistent storage, no interactive access, no external networking
- Helps reduce the attack surface for sensitive data processing apps
    - Cryptographic Attestation - only authorized code can be running in your Enclave
    - Only Enclaves can access sensitive data (integration with KMS)
- Use cases: securing private keys, processing credit cards, secure multi-party computation...
- Process
  - Launch a compatible Nitro-based EC2 instance with the `EnclaveOptions` parameter set to `true`
  - Use the Nitro CLI to convert your app to an Enclave Image File (EIF)
  - Using the EIF file as an input, use the Nitro CLI to create an Enclave
  - The Enclave is a separate virtual machine with its own kernel, memory and CPU
  - The instance can only connect with Secure local channel leveraging Nitro Hypervisor
  - Isolation between EC2 instances on the same host