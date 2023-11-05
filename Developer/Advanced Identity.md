## Advanced Identity

### AWS STS - Security Token Service

- Enable you to create temporary, limited-privileges credential to access your AWS resources
- Short-term credentials, you configure expiration period
- Use cases
    - Identity federation
    - IAM Roles for cross/same account access
    - IAM Roles for Amazon EC2
- Allows to grant limited and temporary access to AWS resources (up to 1 hour)
- AssumeRole: Assume roles within your account or cross account
- AssumeRoleWithSAML: return credentials for user logged with SAML
- AssumeRoleWithWebIdentity:
    - return creds for users logged with an IdP (Facebook Login, Google Login, OICD compatible...)
    - AWS recommends against using this, and using Cognito Identity Pools instead
- GetSessionToken: for MFA, from a user or AWS account root user
- GetFederationToken: obtain temporary creds for a federated user
- GetCallerIdentity: return details about the IAM user or role used in the API call
- DecodeAuthorizationMessage: decode error message when an AWS API is denied

### Using STS to Assume a Role

- Define an IAM Role within your account or cross-account
- Define which principals can access this IAM Role
- Use AWS STS (Security Token Service) to retrieve credentials and impersonate the IAM Role you have access to (
  AssumeRole API)
- Temporary credentials can be valide between 15 minutes to 1 hour

### STS with MFA

- Use GetSessionToken from STS
- Appropriate IAM policy using IAM Conditions
- `aws:MutliFactorAuthPresent:true`
- Reminder, GetSessionToken returns:
    - Access ID
    - Secret Key
    - Session Token
    - Expiration date

### Authorization Model Evaluation of Policies, simplified

- If there's an explicit DENY, end decision and DENY
- If there's an ALLOW, end decision with ALLOW
- Else DENY

### IAM Policies & S3 Bucket Policies

- IAM Policies are attached to users, roles, groups
- S3 Bucket Policies are attached to buckets
- When evaluating if an IAM Principal can perform an operation X on a bucket,
  the union of its assigned IAM Policies and S3 will be evaluated

### Dynamic Policies with IAM

- How do you assign each user a /home/<user> folder in an S3 bucket
- Option 1:
    - Create an IAM policy allowing georges to have access to /home/georges
    - Create an IAM policy allowing georges to have access to /home/sarah
    - Create an IAM policy allowing georges to have access to /home/matt
    - ...One policy per user!
    - This doesn't scale
- Option 2:
    - Create one dynamic policy with IAM
    - Leverage the special policy variable ${aws:username}

### Inline vs Managed Policies

- AWS Managed Policy
    - Maintained by AWS
    - Good for power users and administrators
    - Update in case of new services / new APIs
- Customer Managed Policy
    - Best Practice, re-usable, can be applied to many principals
    - Version Controlled + rollback, central change management
- Inline
    - Strict on-to-one relationship between policy and principal
    - Policy is deleted if you delete the IAM principal

### Granting a USer Permissions to Pass a Roles to an AWS Service

- To configure many AWS services, you must pass an IAM role to the service (this happens only once during setup)
- The service will later assume the role and perform actions
- Example of passing a role:
    - To an EC2 instance
    - To an Lambda function
    - To an ECS task
    - To CodePipeline to allow it to invoke other services
- For this, you need the IAM permission `iam:PassRole`
- It often comes with `iam:GetRole` to view the role being passed

### Can a role be passed to any service?

- No: Roles can only be passed to what their trust allows
- A trust policy for the role that allows the service to assume the role


### AWS Directory Services

- What is Microsoft Active Directory (AD)?
  - Found on any Windows Server with AD Domain Services
  - Database of objects: User Accounts, Computers, Printers, File Shares, Security Groups
  - Centralized security managements, create account, assign permissions
  - Objects are organized in trees
  - A group of tress is a forest
- AWS Directory Services
  - AWS Managed Microsoft AD
    - Create your own AD in AWS, manage users locally, supports MFA
    - Establish "trust" connections with your on-premise AD
  - AD Connector
    - Directory, Gateway (proxy) to redirect to on-premise AD, supports MFA
    - Users are managed on the on-premise AD
  - Simple AD
    - AD-compatible managed directory on AWS
    - Cannot be joined with on-premise AD
- IAM Identity Center - Active Directory Setup
  - Connect to an AWS Managed Microsoft AD (Directory Service)
    - Integration is out of the box
  - Connect to a Self-Managed Directory
    - Create Two-way Trust Relationship using AWS Managed Microsoft AD
    - Create an AD Connector