## AWS Organizations Policies

### Service Control Policies (SCP)

- Define allowlist or blocklist IAM actions
- Applied at the OU or Account level
- Does not apply to the Management Account
- SCP is applied to all the Users and Roles in the account, including Root user
- The SCP does not affect Service-linked roles
    - Service-linked roles enable other AWS services to integrate with AWS Organizations and can't be restricted by SCPs
- SCP must have an explicit Allow (does not allow anything by default)
- Use cases:
    - Restric access to certain services (for example: can't use EMR)
    - Enforce PCI compliance by explicitly disabling services

### Restricting Tags with IAM Policies

- You can restrict specific Tags on AWS resources
- Using the `aws:TagKeys` Condition Key
    - Validate the Tag Keys attached to a resource againt the Tag Keys in the IAM Policy
- Example: allow IAM users to create EBS Volumes only if it has the "Env" and "CostCenter" Tags
- Use either `ForAllValues` (must have all keys) or `ForAnyValue` (must have any of these keys at a minimum)

### Using SCP to Restrict Creaing Resources without appropriate Tags

- Prevent IAM Users/Roles in the affected Member accounts from creating resources if they don't have a specific Tags
- Example: restrict launching an EC2 instance if it doesn't have the "Project" and "CostCenter" Tags

### AWS Organizations - Tag Polices

- Helps you standardize tags across resources in an AWS Organization
- Ensure consistent tags, audit tagged resources, maintain proper resources categorization
- You define Tag keys and their allowed values
- Helps with AWS Cost Allocation Tags and Attribute-based Access Control
- Prevent any non-compliant tagging operations on specified services and resources
- Generate a report that lists all tagged/non-compliant resources
- Use Amazon EventBridge to monitor non-compliant tags

### AWS Organizations - AI Services Opt-out Policies

- Certain AWS AI services may use your content for continous improvement of Amazon AI/ML services
- Example: Amazon Lex, Amazon Comprehend, Amazon Polly,...
- You can opt-out of having your content stored or used by AWS AI services
- Create an Opt-out Policy that enforces this setting across all Member accounts and AWS Regions
- You can opt-out all AI services or selected services
- Can be attached to Organization Root, speific OU. or individual Memeber account

### AWS Organizations - Backup Policies

- AWS Backup enables you to crete Backup Plans that define how to backup your AWS resources
- JSON documents that define Backup Plan across an AWS Organization
- Gives you granular control over backing up your resources (e.g, backup frequency, time window, backup region,...)
- Can be attached to Organization Root, specific OU, or individual Member account
- Immutable Backup Plans appear in Member accounts (view ONLY)












