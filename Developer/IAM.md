## IAM Section

- Identity and Access Management
- Global Service
- Root account created by default, shouldn't be used or shared

### Users & Groups

- Users are people within your organization, and can be grouped
- Groups only contain users, not other groups
- Users don't have to belong to a group, and can belong to multiple groups

### IAM Policies

- Policies: JSON documents
- Define permissions of the users
- Least privilege principle
- IAM policy structure:
    - Version
    - Id: (optional)
    - Statement: one or more individual statements (required)
        - Sid: (optional)
        - Effect: Allow/Deny
        - Action: lists of actions
        - Resource: lists of resources to which action applied to
        - Condition: (optional)

### Password Policy

- Minimum password length
- Require specific character types
- Password expiration
- Prevent password re-use

### Multi Factor Authentication - MFA

- Virtual MFA device
- Universal 2nd Factor (U2F) Security Key
- Hardware Key Fob MFA device
- Hardware Key Fob MFA device for AWS GovCloud (US)

### Access AWS

- AWS Management console
- AWS Command Line interface (CLI)
    - AWS Cloud Shell
- AWS Software Developer Kit (SDK)
- Access Key and Secret Access Key

### IAM Roles for Services

- Some AWS service will need to perform actions on your behalf
- We will assign permissions to AWS services with IAM Role

### IAM Security Tools

- IAM Credential Report (account-level)
    - A report that lists all your account's users and the status of their various credentials
- IAM Access Advisor (user-level)
    - Show the service permissions granted to a user and when those services are last accessed
    - To revise your polices

### IAM Section - Summary

- Users: mapped to a physical user, has a password for AWS console
- Groups: contains users only
- Policies: JSON document that outlines permissions for users or groups
- Roles: for EC2 instances or AWS services
- Security: MFA +
- AWS CLI: Manage AWS services using command-line
- AWS SDK: Manage AWS services using a programming language
- Access keys: Access AWS using CLI or SDK
- Audit: IAM credential Reports & IAM Access Advisor

### IAM: Permissions

- Users of Groups can be assigned JSON documents called policies
- These policies define the permissions of the users
- In AWS you apply the least privilege principle: don't give more permissions than a user needs

### IAM Policies Structure

- Consists of
    - Version: policy language version, always include "2012-10-17"
    - Id: an identifier for the policy (optional)
    - Statement: one or more individual statements (required)
        - Sid: an identifier for the statement (optional)
        - Effect: whether the statement allows or denies access (Allow, Deny)
        - Principal: account/user/role to which the policy applied to
        - Action: list of actions this policy allows or denies
        - Resource: list of resources to which the actions applied to
        - Condition: conditions for when this policy is in effect (optional)

### IAM - Password Policy

- Strong passwords = higher security for your account
- In AWS, you can setup a password policy:
    - Set a minimum password length
    - Require specific character types
        - including uppercase letters
        - lowercase letters
        - numbers
        - non-alphanumeric characters
    - Allow all IAM users to change their own passwords
    - Required users to change their password after some time (password expiration)
    - Prevent password re-use

### Multi Factor Authentication - MFA

- Users have access to your account and can possibly change configurations or delete resources in your AWS account
- You want to protect your Root Accounts and IAM users
- MFA = password you know + security device you onw
- Main benefit of MFA:
    - If a password is stolen or hacked, the account is not compromised

### How can users access AWS?

- TO access AWS, you have three options:
    - AWS Management Console (protected by password + MFA)
    - AWS Command Line Interface (CLI) protected by access keys
    - AWS Software Developer Kit (SDK) - for code: protected by access keys
- Access Keys are generated through the AWS Console
- Users manage their own access keys
- Access Keys are secret, just like a password. Don't share them
- Access Key ID ~= username
- Secret Access Key ~= password

### What's the AWS CLI

- A tool that enables you to interact with AWS services using commands in your command-line shell
- Direct access to the public APIs of AWS services
- You can develop scripts to manage your resources
- open-source
- Alternative to using AWS Management Console

### What's the AWS SDK

- AWS Software Development Kit (AWS SDK)
- Language-specific APIs (set of libraries)
- Enables you to access and manage AWS services programmatically
- Embedded within your application
- Supports
    - SDKs (JavaScript, Python, PHP, .NET, Ruby, Java, Go, Node.js, C++)
    - Mobile SDKs (Android, iOS)
    - IoT Device SDKs (Embedded C, Arduino)
- Example: AWS CLI is built on AWS SDK for Python

### IAM Roles for Services

- Some AWS service will need to perform actions on your behalf
- To do so, we will assign permissions to AWS services with IAM Roles
- Common roles:
    - EC2 instances roles
    - Lambda function roles
    - Roles for CloudFormation

### IAM Guidelines & Best Practices

- Don't use the root account except for AWS account setup
- One physical user = One AWS user
- Assign users to groups and assign permissions to groups
- Create a strong password policy
- Use and enforce the use of Multi-Factor Authentication (MFA)
- Create and use Roles for giving permissions to AWS services
- Use Access Keys for Programmatic Access (CLI/SDK)
- Audit permissions of your account using IAM Credentials Report & IAM Access Advisor
- Never share IAM users & Access Keys

### Shared Responsibility Model for IAM

- AWS
    - Infrastructure (global network security)
    - Configuration and vulnerability analysis
    - Compliance validation
- You
    - Users, Groups, Roles, Policies management and monitoring
    - Enable MFA on all accounts
    - Rotate all your keys often
    - Use IAM tools to apply appropriate permissions
    - Analyze access patterns & review permissions