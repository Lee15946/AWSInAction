## AWS Directory Services

### What is Microsoft Active Directory (AD)

- Found on any Windows Server with AD Domain Services
- Database of objects: User Accounts, Computers. Printers. File Shares, Security Groups
- Centralized security management, create account, assign permissions
- Objects are organized in trees
- A groups of trees is a forest

### What is ADFS (AD Federation Services)

- ADFS provides Single Sign-On across applications
- SAML across 3rd party: AWS Console, Dropbox, Office365, etc...

### AWS Directory Services

- AWS Managed Microsoft AD
    - Create you own AD in AWS, manage users locally, supports MFA
    - Establish "trust" connections with your on-premises AD
- AD Connector
    - Directory Gateway (proxy) to redirect to on-premises AD, supports MFA
    - Users are managed on the on-premises AD
- Simple AD
    - AD-compatible managed directory on AWS
    - Cannot be joined with on-premises AD

### AWS Directory Services - AWS Managed Microsoft AD

- Managed Service: Microsoft AD in your AWS VPC
- EC2 Windows Instances:
    - EC2 Windows instances can join the domain and run traditional AD applications (sharepoint, etc)
    - Seamlessly Domain Join Amazon EC2 Instances from Multiple Accounts & VPCs
- Integrations:
    - RDS for SQL Server, AWS Workspaces, Quicksight
    - AWS SSO to provide access to 3rd party applications
- Standalone repository in AWS or joined to on-premises AD
- Multi AZ deployment of AD in 2 AZ, # of DC (Domain Controllers) can be increased for scaling
- Automated backups
- Automated Multi-Region replication of your directory

### AWS Microsoft Managed AD - Integrations

- AWS Managed Microsoft AD DC
    - AD two-way Forest trust with ON-premises AD
    - SAML through AWS SSO
    - AWS resources
        - RDS for SQL Server
        - Amazon WorkSpaces
        - Amazon Quicksight
        - Amazon Connect
        - Amazon WorkDocs
        - AWS Single-Sign On
    - Traditional AD Applications
        - .NET Apps
        - SharePoint
        - SQL Server

### Connect to on-premises AD

- Ability to connect your on-premises Active Directory to AWS Managed Microsoft AD
- Must establish a Direct Connect (DX) or VPN connection
- Can setup three kinds of forest trust:
    - One-way trust: AWS => On-premises
    - One-way trust: AWS => On-premises
    - Two-way forest trust: AWS <=> On-Premises
- Forest trust is different than synchronization (replication is not supported)

### Solution Architecture: Active Directory Replication

- You may want to create a replica of your AD on EC2 in the cloud to minimize latency of in case DX or VPN goes down
- Establish trust between the AWS Managed Microsoft AD and EC2
- On-premise Microsoft AD can replicate on Microsoft AD on EC2 as Self Managed Replica
- AWS Managed Microsoft AD DC can have two way trust on EC2 Microsoft AD

### AWS Directory Services - AD Connector

- AD Connector is a directory gateway to redirect directory requests to your on-premises Microsoft Active Directory
- No caching capability
- Manage users solely on-premises, no possibility of setting up a trust
- VPN or Direct Connect
- Doesn't work with SQL Server, doesn't work with seamless joining, can;t share directory

### AWS Directory Services - Simple AD

- SimpleAD is an inexpensive Active Directory - compatible service with the common directory features
- Supports joining EC2 instances, manage users and groups
- Does not support MFA, RDS SQL server, AWS SSO
- Small: 500 users, large: 5000 users
- Powered by Samba4, compatible with Microsoft AD
- lower cost, low scale, basic AD compatible, or LDAP compatibility
- No trust relationship









