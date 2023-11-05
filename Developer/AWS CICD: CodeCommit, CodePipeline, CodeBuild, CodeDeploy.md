## AWS CICD

### Introduction to CICD in AWS

- CICD - Introduction
    - We have learned how to:
        - Create AWS resources, manually (fundamentals)
        - Interact with AWS programmatically (AWS CLI)
        - Deploy code to AWS using Elastic Beanstalk
    - All there manual steps make it very likely for us to do mistakes
    - We would like our code "in a repository" and have it deployed onto AWS
        - Automatically
        - The right way
        - Making sure it's tested before being deployed
        - With possibility to go into different stages (dev, test, staging, prod)
        - With manual approval where needed
    - To be a proper AWS developer... we need to learn AWS CICD
    - This section is all about automating the development we've done so fat while adding increased safety
    - We'll learn about:
        - AWS CodeCommit - storing our code
        - AWS CodePipeline - automating our pipeline from code to Elastic Beanstalk
        - AWS CodeBuild - building and testing our code
        - AWS CodeDeploy - deploying the code to EC2 instances (not Elastic Beanstalk)
        - AWS CodeStar - manage software development activities in one place
        - AWS CodeArtifact - store, publish, and share software packages
        - AWS CodeGuru - automated code reviews using Machine Learning

### Continuous Intergration (CI)

- Developers push the code to a code repository often (e.g, Github, CodeCommit, Bitbucket...)
- A testing/build server checks the code as sonn as it's published (CodeBuild, Jenkins CI,...)
- The developer gets feedback about the tests and checks that have passed / failed
- Find bugs early, then fix bugs
- Deliver faster as the code is tested
- Deploy often
- Happier developers, as they're unblocked

### Continuous Delivery (CD)

- Ensures that the software can be released reliably whenever needed
- Ensures deployments happen often and are quick
- Shift away from "one release every 3 months" to "5 releases a day"
- That usually menas automated deployment (e.g, CodeDeploy, Jenkins CD, Spinnaker)

### AWS CodeCommit

- Version control is the ability to understand the various changes that happened to the code over time (and possibly
  roll back)
- All these are enabled by using a version control system such as Git
- A Git repository can be synchronized on your computer, but it usually is uploaded on a central online repository
- Benefits are:
    - Collaborate with other developers
    - Make sure the code is backed-up somewhere
    - Make sure it's fully viewable and auditable
- Git repositories can be expensive
- The industry includes Github, GitLab, Bitbucket
- And AWS CodeCommit:
    - Private Git repositories
    - No size limit on repositories (scale seamlessly)
    - Fully managed, highly available
    - Code only in AWS Cloud account => increased security and compliance
    - Security (encrypted, access control)
    - Integrated with Jenkins, AWS CodeBuild and other CI tools
- CodeCommit - Security
    - Interactions are done using Git (standard)
    - Authentication
        - SSH Keys - AWS Users can configure SSH keys in their IAM Console
        - HTTPS - with AWS CLI Credential helpers or Git Credentials for IAM user
    - Authorization
        - IAM policies to manage users/roles permissions to repositories
    - Encryption
        - Repositories are automatically encrypted at rest using AWS KMS
        - Encrypted in transit (can only use HTTPS or SSH - both secure)
    - Cross-account Access
        - Do NOT share your SSH keys or AWS credentials
        - Use an IAM Role in your AWS account and use AWS STS (AssumeRole API)
- Notification
    - Events
        - Comments
        - Approvals
        - Pull requests
        - Branches and tags
        - Target:
            - SNS Topic
            - Chat bot (Slack)
- Trigger
    - Events
        - Push to existing branch
        - Create branch or tag
        - Delete branch or tag
        - All
    - Target service
        - Amazon SNS
        - AWS Lambda

### AWS CodePipeline

- Visual Workflow to orchestrate your CICD
- Source - CodeCommit, ECR, S3, Bitbucket, Github
- Build - CodeBuild, Jenkins, CloudBees, TeamCity
- Test - CodeBuild, AWS Device Farm, 3rd party tools
- Deploy - CodeDeploy, Elastic Beanstalk, CloudFormation, ECS, S3,...
- Invoke - Lambda, Step Functions
- Consists of stages:
    - Each stage can have sequential actions and/ or parallel actions
    - Example: Build -> Test -> Deploy -> Load Testing ->...
    - Manual approval can be defined at any stage
- CodePipeline - Artifacts
    - Each pipeline stage can create artifacts
    - Artifacts stored in an S3 bucket and passed on to the next stage
- CodePipeline - Troubleshooting
    - For CodePipeline Pipeline/Action/Stage Execution State Changes
    - Use CloudWatch Events (Amazon EventBridge). Example:
        - You can create events for failed pipelines
        - You can create events for canceled stages
    - If CodePipeline fails a stage, your pipeline stops, and you can get information in the console
    - If pipeline can't perform an action, make sure the "IAM Serivce Role" attached does have enough IAM permissions (
      IAM Policy)
    - AWS CloudTrail can be used to audit AWS API calls

### CodePipeline - Extras

- Events
    - CodeCommit -> event -> EventBridge -> trigger -> CodePipeline
    - Github -> CodeStar Source Connection (GutHub App) -> trigger -> CodePipeline
- Webhooks
    - Script -> HTTP Webhook -> CodePipeline
- Polling
    - CodePipeline -> regular checks -> GitHub
- Action Types Constraints for Artifacts
    - Owner
        - AWS - for AWS services
        - 3rd Party - GitHub or Alexa Skills Kit
        - Custom - Jenkins
    - Action Type
        - Source - S3, ECR, Github,...
        - Build - CodeBuild, Jenkins
        - Test - CodeBuild, Device Farm, Jenkins
        - Approval - Manual
        - Invoke - Lambda, Step Functions
        - Deploy - S3, CloudFormation, CodeDeploy, Elastic Beanstalk, OpsWorks, ECS, Service Catalog,...
- Manual Approval Stage
    - CodeCommit -> new commit -> CodeBuild -> trigger -> Manual Approval (Important: Owner is "AWS", Action is "
      Manual") -> deploy -> CodeDeploy
    - Manual Approval can trigger SNS to email to IAM User, need proper IAM User
      Permissions (`GetPipeline`,`PutApprovalResult`)

### AWS CodeBuild

- Source - CodeCommit, S3, Bitbucket, GitHub
- Build instructions: Code file `buildspec.yml` or insert manually in console
- Output logs can be stored in Amazon S3 & CloudWatch Logs
- Use CloudWatch Metrics to monitor build statistics
- Use EventBridge to detect failed builds and trigger notifications
- Use CloudWatch Alarms to notify if you need "thresholds" for failures
- Build Projects can be defined within CodePipeline or CodeBuild
- Supported Environments
    - Java
    - Ruby
    - Python
    - Go
    - Node.js
    - Android
    - .NET Core
    - PHP
    - Docker - extend any environment you like
- How it Works
    - CodeCommit + buildspec.yml -> CodeBuild (running instructions from buildspec.yml) -> Prepackaged or Custom Image
      from Docker -> retrieve/store reusable pieces to S3 Bucket (Cache) (optional) -> save artifacts in S3 Bucket ->
      store logs in Amazon S3 / CloudWatch Logs
- CodeBuild - buildspec.yml
    - buildspec.yml file must be at the root of your code
    - env - define environment variables
        - variables - plaintext variables
        - parameter-store - variables stored in SSM Parameter Store
        - secrets-manager - variables stored in AWS Secrets Manager
    - phases - specify commands to run:
        - install - install dependencies you may need for your build
        - pre_build - final commands to execute before build
        - Build - actual build commands
        - post_build - finishing touches (e.g, zip output)
    - artifacts - what to upload to S3 (encrypted with KMS)
    - cache - files to cache (usually dependencies) to S3 for future build speedup
- CodeBuild - Local Build
    - In case of need of deep troubleshooting beyond logs...
    - you can run CodeBuild locally on your desktop (after installing Docker)
    - For this, leverage the CodeBuild Agent
- CodeBuild - Inside VPC
    - By default, your CodeBuild containers are launched outside your VPC
        - It cannot access resources in a VPC
    - You can specify a VPC configuration:
        - VPC ID
        - Subnet IDs
        - Security Groups IDs
    - Then your build can access resources in your VPC (e.g, RDS, ElastiCache, EC2, ALB,...)
    - Use cases: integration tests, data query, internal load balancers,...

### CodePipeline - CloudFormation Integration

- CloudFormation is used to deploy complex infrastructure using an API
    - CREATE_UPDATE - create or update an existing stack
    - DELETE_ONLY - delete a stack if exists
- CodeBuild (Build app) -> CloudFormation (Deploy Infra & app) -> CREATE_UPDATE -> CodeBuild (Test app) -> HTTP Test
  Suite -> CloudFormation (Delete Test Infra) -> DELETE_ONLY -> CloudFormation (Deploy Prod Infra) -> deploy to
  Production (CREATE_UPATE)

### AWS CodeDeploy

- Deployment service that automates application deployment
- Deploy new applications versions to EC2 Instances, On-premises servers, Lambda functions, ECS Services
- Automated Rollback capability in case of failed deployments, or trigger CloudWatch Alarm
- Gradual deployment control
- A file named `appsepc.yml` defines how the deployment happens

### CodeDeploy - EC2/On-premises Platform

- Can deploy to EC2 Instances & on-premises servers
- Perform in-place deployments or blue/green deployments
- Must tun the CodeDeploy Agent on the target instances
- Define deployment speed
    - AllAtOnce: most downtime
    - HalfAtTime: reduced capacity by 50%
    - OneAtATime: slowest, lowest availability impact
    - Custom: define your %
- CodeDeploy Agent
    - The CodeDeploy Agent must be running on the EC2 instgances as a pre-requisites
    - It can be installed and updated automatically if you're using Systems Manager
    - The EC2 Instances must have sufficient permissions to access Amazon S3 to get deployment bundles

### CodeDeploy - Lambda Platform

- CodeDeploy can help you automate traffic shift or Lambda aliases
- Feature is integrated within the SAM framework
- Linear: grow traffic every N minutes until 100%
    - LambdaLinear10PercentEvery3Minutes
    - LambdaLinear10PercentEvery10Minutes
- Canary: try X percent then 100%
    - LambdaCanary10Percent5Minutes
    - LambdaCanary10Percent30Minutes
- AllAtOnce: immediate

### CodeDeploy - ECS Platform

- CodeDeploy can help you automate the deployment of a new ECS Task Definition
- Only Blue/Green Deployments
- Linear: grow traffic every N minutes until 100%
    - LambdaLinear10PercentEvery3Minutes
    - LambdaLinear10PercentEvery10Minutes
- Canary: try X percent then 100%
    - LambdaCanary10Percent5Minutes
    - LambdaCanary10Percent30Minutes
- AllAtOnce: immediate

### CodeDeploy for EC2 and ASG

- Deployment to EC2
    - Define how to deploy application using appspec.yml + Deployment Strategy
    - Will do In-place update to your fleet of EC2 instances
    - Can use hooks to verify the deployment after each deployment phase
- Deploy to an ASG
    - In-place Deployment
        - Updates existing EC2 instances
        - Newly created EC2 instances by an ASG will also get automated deployments
    - Blue/Green Deployment
        - A new Auto-Scaling Group is created (settings are copied)
        - Choose how long to keep the old EC2 instances (old ASG)
        - Must be using an ELB
- Redeploy & Rollbacks
    - Rollback = redeploy a previously deployed version of your application
    - Deployments can be rolled back:
        - Automatically - rollback when a deployment fails or rollback when a CloudWatch Alarm thresholds are met
        - Manually
    - Disable Rollbacks - do not perform rollbacks for this deployment
    - If a roll back happens, CodeDeploy redeploys the last known good revision as a new deployment (not a restored
      version)

### CodeDeploy - Troubleshooting

- Deployment Error: "InvalidSignatureException - Signature expired : [time] is now earlier than [time]"
    - For CodeDeploy to perform its operations, it requires accurate time references
    - If the data and time on your EC2 instance are not set correctly, they might not match the signature date of your
      deployment request, which CodeDeploy rejects
- Check log files to understand deployment issues
    - For Amazon Linux, Ubuntu, and RHEL log files stored
      at `/opt/cdedeploy-agent/deployment-root/deployment-logs/codedeploy-agent-deployments.log`

### Amazon CodeStar

- An integrated solution that groups: GitHub, CodeCommit, CodeBuild, CodeDeploy, CloudFormation, CodePipeline,
  CloudWatch...
- Quickly create "CICD-ready" projects for EC2, Lambda, Elastic Beanstalk
- Supported languages: C#, Go, HTML 5, Java, Node.js, PHP, Python, Ruby
- Issue tracking integration with JIRA / GitHub Issues
- Ability to integrate with Cloud9 to obtain a web IDE (not all regions)
- One dashboard to view all your components
- Free service, pay only for the underlying usage of other services
- Limited Customization

### AWS CodeArtifact

- Software packages depned on each otehr to be build (also called code dependencies), and new ones are created
- Sotring and retrieving these dependencies is called artifact managment
- Traditionally you need to setup your own artifact management system
- CodeArtifact is a secure, scalable, and cost-effectivd artifact managment for software development
- Works with common dependency management tools such as Maven, Gradle, npm, yarn, twine, pip and NuGet
- Developers and CodeBuild can then retrieve dependencies straight from CodeArtifact
- CodeArtifact - EventBridge Intergration
    - Event is created when a Package version is created, modified, or deleted
    - Inovke Lambda Function, active Step Functions State Machine
    - Message to SNS, SQS
    - Start CodePipeline, Rebuild & redeploy an Application with the latest security fixes
- CodeArtifact - Resource Policy
    - Can be used to authorize another account to access CodeArtifact
    - A given principal can either read all the packages in a repository or none of them

### CodeArtifact - Upstream Repositories & Domains

- Upstream Repositories
    - A CodeArtifact repository can have other CodeArtifact repositories as Upstream Repositories
    - ALlows a package manager client to access the packages that are contained in more than one repository using a
      single repository endpoint
    - Up to 10 Upstream Repositories
    - Only one external connection
- External Connection
  - An External Connection is a connection between a CodeArtifact Repository and an external/public repository (e.g, Maven, npm, PyPI, NuGet...)
  - Allows you to fetch packages that are not already present in your CodeArtifact Repository
  - A repository has a maximum of 1 external connection
  - Create many repositories for many external connections
  - Example - Connect to npmjs.com
    - Configure one CodeArtifact Repository in your domain wiht an external connection to npmjs.com
    - Configure all the other repositories with an upstream to it
    - Packages fetched from npmjs.com are cached in the Upstream Repository, rather than fetching and storing them in each Repository
- CodeArtfiact - Retention
  - If a requested package version is found in an Upstream Repository, a reference to it is retained and is always availbale from the Downstream Repository
  - The retained package version is not affected by changes to the Upstream Repository (deleting it, update the package,...)
  - Intermdediate repositories do not keep the package
  - Example - Fetching Package from npmjs.com
    - Package Manager connected to Repository A requestes the package Lodash v4.17.20
    - The pakcage version is not present in any of the three repositories
    - THe pacakge version will be fetched from npmjs.com
      - Repository A - the most-downstream repository
      - Repository C - has the external connection to npmjs.com
      - THa Package version will not be retained in Repository B as that is an intermediate Repository
- CodeArtifact - Domains
  - Dedupliacte Storage - asset only nees to be stored once in a domain, even if it's available in many repositories (only pay once for storage)
  - Fast Copying - only metadata record are upadted when you pull packages from an Upstream CodeArtifact Repository into a Downstream
  - Easy Sharing Across Repositores and Teams - all the assets and metadata in a domain are encrypted with a single AWS KMS Key
  - Apply Policy Across Multiple Repositories - domain administrator can apply policy across the domain such as:
    - Restricting which accoutns have access to repositories in the domain
    - Who can configure connections to publci repositories to use as source of packages

### Amazon CodeGuru
- An ML-pwered service for automated code reviews and application performance recommendations
- Provides two funciontnalities:
  - CodeGuru Reviewer: automated code reviews for static code analysis (development)
  - CodeGuru Profiler: visibility/recommendations about application performance during runtime (production)
- Amazon CodeGuru Reviewer
  - Identify critical issues, security vulnerabilities, and hard-to-find bugs
  - Example: common coding best practices, reosurce leaks, security detection, input validation
  - Uses Machine Learning and automated reasoning
  - Hard-learned lessons across millions of code reviews on 1000s of open-source and Amazon repositories
  - Supports Java and Python
  - Integrates with GitHub, Bitbucket, and AWS CodeCommit
- Amazon CodeGuru Profiler
  - Helps understand the runtime behavior of your application
  - Example: identify if your application is consuming excessive CPU capacity on a logging routine
  - Features:
    - Identify and remove code inefficiencies
    - Improve application performance (e.g, reduce CPU utilization)
    - Decrease compute costs
    - Provides head summary (identify which objects using up memory)
    - Anomaly Detection
  - Support applications running on AWS or on-premise
  - Minimal overhead on application

### Amazon CodeGuru - Agent Configuration
- MaxStackDepth - the maximum depth of the stacks in the code that is represented in the profile
  - Example: if CodeGuru Profiler finds a method A, which calls method B, which calls method C, which calls method D, the nthe depth is 4
  - If the MaxStackDepth is set to 2, then the profiler evaluates A and B
- MemoryUsageLimit Percent - the  memory percentage used by the profiler
- MinimumTimeForReportingInMilliseconds - the minimum time between sending reports (milliseconds)
- ReportingIntervalInMilliseconds - the reporting interval used to report profiles (milliseconds)
- SamplingIntervalInMilliseconds - the sampling interval that is used to profile samples (milliseconds)
  - Reduce to have a higher sampling rate

### AWS Cloud9
- Cloud-based Integrated Development Environment (IDE)
- Code editor, debugger, terminal in a broswer
- Work on your projects from anywhere with an Internet connection
- Prepackaged with essential tools for popular programming languages (JavaScript, Python, PHP,...)
- Share your development environmetn with your team (pair programming)
- Fully integrated with AWS SAM & Lambda to easily build serverless applications