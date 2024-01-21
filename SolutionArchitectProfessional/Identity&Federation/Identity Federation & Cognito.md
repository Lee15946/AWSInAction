## Identity Federation & Cognito

### Identity Federation in AWS

- Give users outside of AWS permissions to access AWS resources in your account
- You don't need to create IAM Users (user management is outside AWS)
- Use cases:
    - A corporate has its own identity system (e.g, Active Directory)
    - Web/Mobile application that needs access to AWS resources
- Identity Federation can have many flavors
    - SAML 2.0
    - Custom Identity Broker
    - Web Identity Federation With(out) Amazon Cognito
    - Single Sign-On (SSO)

### SAML 2.0 Federation

- Security Assertion Markup Language 2.0 (SAML 2.0)
- Open standard used by many identity providers (e.g, ADFS)
    - Supports integration with Microsoft Active Dierectoy Federations Service (ADFS)
    - Or any SAML 2.0-compatible IdPs with AWS
- Access to AWS Console, AWS CLI, or AWS API using temporary credentials
    - No need to create IAM Users for each of your employees
    - Need to setup a trust between AWS IAM and SAML 2.0 Identity Provider (both ways)
- under-the-hood: Uses the STS API `AssumeRoleWithSAMl`
- SAML 2.0 Federation is the "old way", Amazon Single Sign-On (AWS SSO) Federation is the new managed and simpler way

### SAML 2.0 Federation - AWS API Access

1. App makes Auth. Request to IdP
2. Idp authenticate to LDAP-based identity Store
3. Return SAML Assertion to User
4. User call STS with `AssumeRoleWithSAML` API
5. STS return Temporary Security Credentials
6. USer access AWS APIs with credentials

### SAML 2.0 Federation - AWS Console Access

1. Users Logs into Portal
2. IdP authenticate to LDAP-based Identity Store
3. Return SAML Assertion to User
4. USer Post to AWS Sign-in
5. AWS Sign-in Endpoint for SAML reuqest temporary security credentials to STS
6. Return Sign-in URL for AWS Console
7. Redirect to AWS Console

### SAML 2.0 Federation - Active Directory FS (ADFS)

1. Users Logs into Portal
2. ADFS authenticate to Microsoft Active Directory
3. Return SAML Assertion to User
4. USer Post to AWS Sign-in
5. AWS Sign-in Endpoint for SAML reuqest temporary security credentials to STS
6. Return Sign-in URL for AWS Console
7. Redirect to AWS Console

### Custom Identity Broker Application

- Use only if Identity Provider is NOT compatible with SAML 2.0
- The Identity Broker Authenticates users & requests temporary credentials from AWS
- The Identity Broker must determine the appropriate IAM Role
- Uses the STS API `AssumeRole` or `GetFedereationToken`

1. User browse to a URL
2. Custom Identity Broker authenticate to LDAP-based Identity Store
3. Broker Requests temporary security credentials from AWS STS
4. Broker return Token or URL to User
5. User can then access AWS APIs or redirect to AWS console

### Web Identity Federation - Without Cognito

- Not recommended by aWS - use Cognito instead

1. Client Login to 3rd Party Identity Provider
2. Get Web Identity oken
3. Call `AssumeRoleWithWebIdentity` API to STS
4. Get Temporary Security Credentials
5. Access AWS resources with credentials

### Web Identity Federation - With Cognito

- Preferred over for Web Identity Federation
    - Create IAM Roles using Cognito with the least privilege needed
    - Build trust between the OIDC IdP and AWS
- Cognito benefits:
    - Supports anonymous users
    - Supports MFA
    - Data Synchronization
- Cognito replaces a Token Vending Machine (TVM)

1. Client Login to 3rd Party Identity Provider
2. Get ID Token
3. Exchange ID Token to Amazon Cognito for Cognito Token
4. Use Cognito Token to get Temporary Security Credentials from STS
5. Access AWS resources with credentials

### Web Identity Federation - IAM Policy

- After being authenticated with Web Identity Federation, you can identity the user with an IAM policy variable
- Examples:
    - cognito-identity.amazonaws.com:sub
    - www.amazon.com:user_id
    - graph.facebook.com:d
    - accounts.google.com:sub







