## AWS Resource Access Manager

- Share AWS resources that you own with other AWS accounts
- Share with any account or within your Organization
- Avoid resource duplication!
- VPC Subnets
    - Allow to have all the resources launched in the same subnets
    - Must be from the same AWS Organizations
    - Cannot share security groups and default VPC
    - Participates can manage their own resources in there
    - Participants can't view, modify, delete resources that belong to other participants or the owner
- AWS Transit Gateway
- Route 53 (Resolver Rules, DNS Firewall Rule Groups)
- License Manager Configurations
- Aurora DB Clusters
- ACM Private Certificate Authority
- CodeBuild Project
- EC2 (Dedicated Hosts, Capacity Reservation)
- AWS Glue (Catalog, Database, Table)
- AWS Network Firewall Policies
- AWS Resource Groups
- System Manager Incident Manager (Contacts, Response Plans)
- AWS Outposts (Outpost, Site)

### Resource Access Manager - VPC example

- Each account...
    - is responsible for its own resources
    - cannot view, modify or delete other resources in other accounts
- Network is shared so...
    - Anything deployed in the VPC can talk to other resources in the VPC
    - Applications are accessed easily across accounts, using private IP!
    - Security groups from other accounts can be referenced for maximum security
- Use cases
    - Applications within the same trust boundaries
    - Applications with a high degree of interconnectivity

### Resource Access Manager - Managed Prefix List

- A set of one or more CIDR blocks
- Makes it easier to configure and maintain Security Groups and Route Tables
- Customer-Managed Prefix List
    - Set of CIDRs that you define and manage by you
    - Can be shared with other AWS accounts or AWS Organization
    - Modify to update many security groups at once
- AWS-Managed Prefix List
    - Set of CIDRs for AWS Services
    - you can't create, modify, share or delete them

### Resource Access Manager - Route 53 Outbound Resolver

- Helps you scale forwarding rules to your DNS in case you have multiple accounts and VPC

### Summary of Identity & Federation

- Users and Accounts all in AWS
- AWS Organizations
- AWS Control Tower to setup secure & complaint multi-account AWS environment (best practices)
- Federation with SAML
- Federation without SAML with a custom IdP (`GetFederationToken`)
- AWS Single Sign-On to connect to multiple AWS Accounts (Organization) and SAML apps
- Web Identity Federation (not recommended)
- Cognito for most web and mobile application (has anonymous mode, MFA)
- AWS Directory Service
    - Managed Microsoft AD - standalone or setup trust AD with on-premises, has MFA, seamless join, RDS integration
    - AD Connector - proxy request to on-premises
    - Simple AD - standalone $ cheap AD-compatible with no MFA, no advanced capabilities
- AWS RAM to share resources (example VPC subnets)



