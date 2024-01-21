## AWS Control Tower

- Easy way to set up and govern a secure and compliant multi-account AWS environment based on best practices
- Benefits:
    - Automate the set up of your environment in a few clicks
    - Automate ongoing policy management using guardrails
    - Detect policy violations and remediate them
    - Monitor compliance through an interactive dashboard
- AWS Control Tower runs on top of AWS Organizations:
    - It automatically sets up AWS Organizations to organize accounts and implement SCPs (Service Control Policies)

### AWS Control Tower - Account Factory

- Automated account provisioning and deployments
- Enables you to create pre-approved baselines and configuration options for AWS accounts in your organization (e.g, VPC
  default configuration, subnets, region,...)
- Uses AWS Service Catalog to provision new AWS accounts

### AWS Control Tower - Detect and Remediate Policy Violations

- Guardrail
    - Provides ongoing governance for your Control Tower environment (AWS Accounts)
    - Preventive - using SCPs (e.g, Disallow Creation of Access Keys for the Root User)
    - Detective - using AWS config (e.g, Detect Whether MFA for the Root User is enabled)
    - Example: identity non-compliant resources (e.g, untagged resources)

### AWS Control Tower - Guardrails Levels

- Mandatory
    - Automatically enabled and enforced by AWS Control Tower
    - Example: Disallow public Read access to the Log Archive account
- Strongly Recommended
    - Based on AWS best practices (optional)
    - Example: Enable encryption for EBS volumes attached to EC2 instances
- Elective
    - Commonly used by enterprises (optional)
    - Example: Disallow delete actions without MFA in S3 buckets












