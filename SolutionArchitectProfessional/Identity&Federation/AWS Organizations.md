## AWS Organizations

- Root Organization Unit (OU)
- Management Account
    - OU (Dev)
    - Memeber Accounts
- OU (Prod)
    - OU (HR)
    - OU (Finance)

### AWS Organizations - OrganizationAccountAccessRole

- IAM Role which grants full administrator permissions in the Member account to the Management account
- Used to perform admin tasks in the Member accounts (e.g, creating IAM users)
- Could be assumed by IAM users in the Management account
- Automatically added to all new Memer accounts created with AWS organizations
- Must be created manually if you invite an existing Member account

### Multi Account Strategies

- Create accounts per department, per cost center, per dev / test/ prod, based on regulatory restrictions (using SCP),
  for better resource isolation (ex: VPC) to have seperate per-account service limits, isolated account for logging
- Multi Account vs. One Account Multi VPC
- Use tagging standards for billing purposes
- Enable CloudTrail on all accounts, send logs to central S3 account
- Send CloudWatch Logs to central logging account
- Strategy to create an account for security

### Organizational Units (OU) - Examples

- Business Unit
- Environmental Lifecycle
- Project-Based

### AWS Organization - Feature Modes

- Consolidated billing features:
    - Consolidate Billing across all accounts - single payment method
    - Pricing benefits from aggregated usage (volume discount for EC2, S3...)
- All Features (Default)
    - Includes consolidate billing features, SCP
    - Invited accounts must approve enabling all features
    - Ability to apply an SCP to prevent member accounts from leaving the org
    - Can't switch back to Consolidated Billing Features only

### AWS Organizations - Reserved Instances

- For billing purposes, the consolidate billing feature of AWS Organizations treats all the accounts in the organization
  as one account
- This means that all accounts in the organization can receive the hourly cost benefit of Reserved Instances that are
  purchased by any other account
- The payer account (Management account) of an organization can turn off Reserved Instance (RI) discount and Saving
  Plans discount sharing for any accounts in that organization, including the payer account
- This means that RIs and Saving Plans discounts aren't shared between any accounts that have sharing turned off
- To share an RI or Saving Plans discount with an account, both accounts must have sharing turned on

### AWS Organizations - Moving Accounts

- Remove the member account from the AWS Organization
- Send an invite to the member account from the AWS Organization
- Accept the invite to the new Organization from the member account














