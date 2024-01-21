# Deployment and Instance Management

## Elastic Beanstalk

### AWS Elastic Beanstalk Overview

- Elastic Beanstalk is a developer centric view of deploying an application on AWS
- It uses all the component's we've seen before
    - EC2, Auto Scaling Group, Elastic Load Balancers, RDS, etc...
- But it's all in one view tha's easy to make sense of!
- We still have full control over the configuration of each component
- Beanstalk is free but you pay for the underlying instances
- Supoort for many platforms:
    - Go
    - Java SE
    - Java with Tomcat
    - .NET on Windows Server with IIS
    - Node.js
    - PHP
    - Python
    - Ruby
    - Packer Builder
    - Single Container Docker
    - Multi-container Docker
    - Preconfigured Docker
    - If not supported, you can write your custom platform (advanced)
    - Beanstalk is great to "Replatform" your application from on-premises to the cloud
- Managed service
    - Instance configuration / OS is handled by Beanstalk
    - Deployment strategy is configurable but perfomred by Elastic Beanstalk
- Just the application code is the responsibility of the developer
- Three architecture models
    - Single Instance deployment: good for dev
    - LB + ASG: great for production or pre-production web applications
    - ASG only: great for non-web apps in production (workers, etc...)

### Beanstalk Environments

- Single Instance (Great for dev)
    - Elastic IP -> EC2 Instance -> RDS Master
- High Availability with Load Balancer (Great for prod)
    - ALB -> ASG -> RDS
- Worker Tier
    - PUT -> SQS Queue -> Worker Tier = SQS + EC2, ASG

### Web Server vs Worker Environment

- If your application performs tasks that are long to complete, offload these tasks to a dedicated worker environment
- Decoupling your application into two tiers is common
- Example: processing a video, generating a zip file, etc
- You can define periodic tasks in a file `cron.yaml`

### Elastic Beanstalk Deployment - Blue / Green

- Not a "direct feature" of Elastic Beanstalk
- Zero downtime and release facility
- Create a new "stage" environemnt and deploy v2 there
- The new environment (green) can be validated independently and roll back if issues
- Route 53 can be setup using weighted policies to redirect a little bit of traffic to the stage environment
- Using Beanstalk, "swap URLs" (DNS swap) when done with the environment test

## CodeDeploy

### AWS CodeDeploy

- We want to deploy our application automatically to many EC2 instances
- These instances are not managed by Elastic Beanstalk
- There are several ways to handle deployments using open source tools (Ansible, Terraform, Chef, Puppet, etc...)
- We can use the managed service AWS CodeDeploy
- CodeDeploy can deploy to EC2, ASG, ECS & Lambda

### CodeDeploy to EC2

- Define how to deploy the application using `appspec.yml` + deployment strategy
- Will do in-place update to your fleet of EC2 instances
- Can use hooks to verify the deployment after each deployment phase

### CodeDeploy to ASG

- In place updates:
    - Updates current existing EC2 instances
    - Instances newly created by an ASG will alsog get automated deployments
- Blue / green deployment
    - A new auto-scaling group is created (settings are copied)
    - Choose how long to keep the old instances
    - Must be using an ELB

### CodeDeploy to AWS Lambda

- Traffic Shifting feature
- Pre and Post traffic hooks - features to validate deployment (before the traffic shift starts and after it ends)
- Easy & automated rollback using CloudWatch Alarms
- SAM framework natively uses CodeDeploy

### CodeDeploy to ECS

- Support for Blue/Green deployments for Amazon ECS and AWS Fargate
- Setup is done within the ECS service definition
- A new task set is created, and traffic is re-touted the new task test
- Then if everything is stable for X minutes, the old task set is terminated (so you have time to notice issues)
- Supports Canary deployments (Canary10Percent5Minutes)

## CloudFormation

### AWS CloudFormation

- Infrastructure as code (IaC) in AWS
- Portability of stacks across multiple accounts and regions
- Backbone of the Elastic Beanstalk service
- Backbone if the Service Catalog service
- Backbone of the SAM (Serverless Application Model) framework
- Must-know service as developer / sysops / devops

### Retaining Data on Deletes

- You can put a DeletionPolicy on any resource to control what happends when the CloudFormation template is deleted
- DeletionPolicy = Retain
    - Specify on resources to preserve / backup in case of CloudFormation deletes
    - To keep a resource, specify Retain (works for any resource / nested stack)
- DeletionPolicy = Snapshot
    - EBS Volume, ElastiCache Cluster, ElastiCache Replication Group
    - RDS DB Instance, RDS DB Cluster, Redshift Cluster
- DeletePolicy = Delete (default behavior)
    - Note: for `AWS::RDS::DBCluster` resources, the default policy is Snapshot
    - Note: to delete an S3 bucket, you need to first empty the bucket of its content

### CloudFormation Custom Resources (Lambda)

- You can define a Custom Resource in CloudFormation to address any of these use cases
    - An AWS resource is not yet supported (new service for example)
    - An On-Premise resource
    - Emptying an S3 bucket before being deleted
    - Fetch an AMI id
    - Anything you want...!

### CloudFormation StackSets

- Create, update or delete stacks across multiple accounts and regions with a single operation
- Administrator account to create StackSets
- Trusted accounts to create, update, delete stack instances from StackSets
- When you update a stack set, all associated stack instances are updated throughout all accounts and regions
- Enable Automatic Deployment feature to automatically deploy to accounts in AWS Organization or OUs

### CloudFormation Drift Overview

- CloudFormation allows you to create infrastructure
- But it doesn't protect you against manual configuration changes
- How do we know if our resources have drifted?
- We can use CloudFormation Drift!
- Detect drift on an entire stack or on individual resources within a stack

### CloudFormation - Integration with Secrets Manager

- Secret is generated
- reference secret in RDS DB instance
- link the secret to RDS DB instance

### CloudFormation - Resource Import

- Import existing resources into existing /new stacks
- You don't need to delete and re-create the resources as part of a CloudFormation stack
- During import operation, you'll need
    - A template that describes the entire stack (original stack resources & target resources to import)
    - A unique identifier for each target resource (ex. BucketName for S3 buckets)
- Each resource to import must have a `DeletionPolicy` attribute (any value) & Identifier
- Can't import the same resource into multiple stacks

## Service Catalog

### AWS Service Catalog

- Users that are new to AWS have too many options, and may create stacks that are not compliant / in line with the rest of the organization
- Some users just want to quick self-service portal to launch a set of authorized products pre-defined by admins
- Includes: virtual machines, databases, storage options, etc...
- Enter AWS Service Catalog!

### Service Catalog diagram

- Admin Tasks
    - Product (CloudFormation Templates) -> Portfolio (Collection of Products) -> Control (IAM Permissions ot Access Portfolios)
- User Tasks
    - Product List (Authorized by IAM) -> launch -> Provisioned Products (Ready to use Properly Configured, Properly Tagged)

### AWS Service Catalog

- Create and manage catalogs of IT services that are approved on AWS
- The "products" are CloudFormation templates
- Ex: Virtual machine images, Servers, Software, Databases, Regions, IP address ranges
- CloudFormation helps ensure consistency, and standardization by Admins
- They are assigned to Portfolios (teams)
- Teams are presented a self-service portal where they can launch the products
- All the deployed products are centrally managed deployed services
- Helps with governance, compliance, and consistency
- Van give user access to launch products without requiring deep AWS knowledge
- Integration with "self-service portals" such as ServiceNow

## SAM - Serverless Application Model

### AWS SAM - Serverless Application Model

- SAM - Serverless Application Model
- Framework for developing and deploying serverless applications
- All the configuration is YAML code. Examples:
    - Lambda Functions (`AWS::Serverless::Function``)
    - DynamoDB tables (`AWS::Serverless::SimpleTable`)
    - API Gateway (`AWS::Serverless::API`)
    - Step Function - State Machine (`AWS::Serverless::StateMachine`)
- SAM can help you to rum Lambda, API Gateway, DynamoDB locally
- SAM can use CodeDeploy to deploy Lambda functions (traffic shifting)
- Leverages CloudFormation in the backend

### CICD Architecture for SAM

- CodePipeline
    - CodeCommit -> CodeBuild (Build, test, package) -> CloudFormation + SAM
- CodeDeploy -> DynamoDB -> API Gateway

## AWS CDK - Cloud Development Kit

- Define your cloud infrastructure using a familiar language:
    - JavScript/TypeScript, Python, Java, and .NET
- The code is "compiled" into a CloudFormation template (JSON/YAML)
- You can therefore deploy infrastructure and application runtime code together
    - Great for Lambda functions
    - Greate for Docker containers in ECS/EKS
- CDK Application (Programming Languages) -> CDK CLI -> CloudFormation Template -> CloudFormation

## AWS System Manager - SSM

### AWS System Manager Overview

- Helps you manage your EC2 and on-premises systems at scale
- Get operational insights about the state of your infrastructure
- Easily detect problems
- Patching automation for enhanced compliance
- Works for both Windows and Linux OS
- Integrated with CloudWatch metrics / dashboards
- Integrated with AWS Config
- Free service

### How Systems Manager works

- We need to install the SSM agent onto the systems we control
- Installed by default on Amazon Linux AMI & some Ubuntu AMI
- If an instance can't be controlled with Systems Manager, it's probably an issue with the SSM agent
- Make sure the EC2 instances have a proper IAM role to allow Systems Manager actions

### AWS Systems Manager - Run Command

- Execute a document (=script) or just run a command
- Run command across multiple instances (using resource groups)
- Rate Control / Error Control
- Integrated with IAM & CloudTrail
- No need for SSH
- Results in the console

### AWS Systems Manager - Send Command before an ASG Instance is Terminated

- Perform any action before the ASG terminates an EC2 instance
- Create a ASG Lifecycle Hook that puts the instance in `Terminatin:Wait` state
- Monitor the `Terminating:Wait` state using EventBridge
- Trigger a SSM Automation Document to perform the actions on the instances before termination

### Systems Manager Patch Managers - Steps

- Define a patch baseline to use (or multiple if you have multiple environment)
- Define patch groups: define based on tags, example different environments (dev, test, prod) - use tag Patch Group
- Define Maintenance Windows (schedule, duration, registered targets/patch groups and registered tasks)
- Add the AWS-RunPatchBaseline Run Command as part of the registered tasks of the Maintenance Window (works across platform Linux & Windows)
- Define Rate Control (concurrency & error threshold) for the task
- Monitor Patch Compliance using SSM Inventory

### Systems Manager Session Manager

- Allows you to start a secure shell on your EC2 and on-premises servers
- Access through AWS Console, AWS CLI, or Session Manager SDK
- Does not need SSH access, bastion hosts, or SSH keys
- Supports Linux, macOS and Windows
- Log connections to your instances and executed commands
- Session log data can be sent to S3 or CloudWatch Logs
- CloudTrail can intercept `StartSession` events

### Systems Manager OpsCenter

- Resolve Operational Issues (OpsItems) related to AWS resources
- OpsItems - issues, events and alerts
- Aggregates information to resolve issues on each OpsItems such as:
    - AWS Config changes and relationships
    - CloudTrail Logs
    - CloudWatch Alarms
    - CloudFormation Stack information
- Provides Automation Runbooks that you can use to resolve issues
- EventBridge or CloudWatch Alarms can create OpsItems

## AWS Cloud Map

- A fully managed resource discovery service
- Creates a map of the backend services / resources that your applications depend on
- You register your application components, their locations, attributes, and health status with AWS Cloud Map
- Integrated health checking (stop sending traffic to unhealthy endpoints)
- Your applications can query AWS Cloud Map using AWS SDK, API, or DNS






















