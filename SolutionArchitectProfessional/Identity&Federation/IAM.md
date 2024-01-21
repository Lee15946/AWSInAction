## IAM

### IAM - What should you know by now

- Users: long term credentials
- Groups
- Roles: short-term credentials, uses STS
    - EC2 Instance Roles: uses the EC2 metadata service. One role at a time per instance
    - Service Roles: API Gateway, CodeDeploy, etc...
    - Cross Account roles
- Policies
    - AWS Managed
    - Customer Managed
    - Inline Policies
- Resource Based Policies (S3 bucket, SQS queue, etc...)

### IAM Policies Deep Dive

- Anatomy of a policy: JSON doc with Effect, Action, Resource, Conditions, Policy Variables
- Explicit DENY has precedence over ALLOW
- Best practice: use least privilege for maximum security
    - Access Advisor: See permissions granted and when last accessed
    - Access Analyzer: Analyze resources that are shared with external entity

### IAM AWS Managed Policies

- AdministratorAccess
    - `Allow *`
- PowerUserAccess
    - `Allow NotAction iam:*`
    - `Allow Action iam:CreateServiceLinkedRole`
    - Don't deny everything and explicitly allow a few things are there

### IAM Policies Conditions

- Operators
    - String (StringEquals, StringNotEquals, StringLike...)
    - Numeric (NumericEquals, NumericNotEquals, NumericLessThan)
    - Date (DateEquals, DateNotEquals, DateLessThan...)
    - Boolean (Bool)
        - `"Condition":{"Bool":{"aws:SecureTransport:":"true"}}`
        - `"Condition":{"Bool":{"aws:MultiFactorAuthPresent:":"true"}}`
    - (Not)IpAddress
        - `"Condition":{"IpAddress":{"aws:SourceIp:":"203.0.113.0/24"}}`
    - ArnEquals, ArnLike
    - Null
        - `"Condition":{"Null":{"aws:TokenIssueTime:":"true"}}`

### IAM Policies Variables and Tags

- Example: ${aws:username}
- AWS Specific:
    - aws:CurrentTime, aws:TokenIssueTime, aws:principaltype, aws:SecureTransport, aws:SourceIp, aws:userid, ec2:
      SourceInstanceARN
- Service Specific:
    - s3:prefix, s3:max-keys, s3:x-amz-acl, sns:Endpoint, sns:Protocol
- Tag Based:
    - iam:ResourceTag/key-name, aws:PrincipalTag/key-name

### IAM Roles vs Resource Based Policies

- Attach a policy to a resource (example: S3 bucket policy) versus attaching of a using of a role as a proxy
- When you assume a role (user, application or service), you give up your original permissions and take the permissions
  assigned to the role
- When using a resource-based policy, the principal doesn't have to give up any permissions
- Example: User in account A needs to scan a DynamoDB table in Account A and dump it and S3 bucket in Account B
- Supported by: Amazon S3 buckets, SNS topics, SQS queues, Lambda functions, ECR, Backup, EFS, Glacier, Cloud9. AWS
  Artifact, Secrets Manager, ACM, KMS, CloudWatch Logs, API Gateway, EventBridge etc...

### IAM Permission Boundaries

- IAM Permission Boundaries are supported for users and roles (not groups)
- Advanced feature to use a managed policy to set the maximum permissions and IAM entity can get
- Can be used in combinations of AWS Organizations SCP
- Use cases
    - Delegate responsibilities to non administrators within their permission boundaries, for example create new IAM
      users
    - Allow developers to self-assign policies and manage their own permissions, while making sure they can't "escalate"
      their privileges (= make themselves admin)
    - Useful to restrict one specific user (instead of a whole account using Organization & SCP)

### IAM Access Analyzer

- Find out which resources are shared externally
    - S3 Buckets
    - IAM Roles
    - KMS Keys
    - Lambda Functions and Layers
    - SQS queues
    - Secrets Manager Secrets
- Define Zone of Trust = AWS Account or AWS Organization
- Access outside zone of trusts => findings
- IAM Access Analyzer Policy validation
    - Validates your policy against IAM policy grammar and best practices
    - General warnings, security warnings, errors, suggestions
    - Provides actionable recommendations
- IAM Access Analyzer Policy Generation
    - Generates IAM policy based on access activity
    - CloudTrail logs is reviewed to generate the policy with the fine-grained permissions and the appropriate Actions
      and Services
    - Review CloudTrails logs for up to 90 days

### STS