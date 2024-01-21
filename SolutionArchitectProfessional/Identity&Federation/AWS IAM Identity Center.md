## AWS IAM Identity Center

### AWS IAM Identity Center (successor to aWS Single Sign-On)

- One login (single sign-on) for all your
    - AWS accounts in AWS Organizations
    - Business cloud applications (e.gm Salesforce, Box, Microsoft 365,...)
    - SAML2.0-enabled applications
    - EC2 Windows Instances
- Identity providers
    - Built-in identity store in IAM Identity Center
    - 3rd party: Active Directory (AD), OneLogin, Okta...

### AWS IAM Identity Center - Fine-grained Permissions and Assignments

- Multi-Account Permissions
    - Manage access across AWS accounts in your AWS Organization
    - Permission Sets - a collection of one or more IAM Policies assigned to users and groups to define AWS access
- Application Assignments
    - SSO access to many SAML 2.0 business applications (Salesforce, Box, Microsoft 365,..)
    - Provide required URLs, certificates and metadata
- Attribute-Based Access Control (ABAC)
    - Fine-grained permissions based on users' attributes stored in IAM Identity Center Identity Store
    - Example: cost center, title, locale,...
    - Use case: Define permissions once, then modify AWS access by changing the attributes