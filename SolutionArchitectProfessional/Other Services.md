# Other Services

## CICD

### Continous Integration

- Developers push the code to a code repository often (Github / CodeCommit / Bitbucket / etc...)
- A testing/ build server checks the code as soon as it's ushed (CodeBuild / Jenkins CI / etc...)
- The developer gets feedback about the tests and checks that have passed / failed
- Find bugs early, fix bugs
- Deliver faster as the code is tested
- Deploy often
- Happier developers, as they're unblocked
- ### Continuous Delivery
- Ensure that the software can be released reliably whenever needed
- Ensures deployments happen often and are quick
- Shift away from "one release every 3 months" to "5 releases a day"
- That usually means automated deployment
    - CodeDeploy
    - Jenkins CD
    - Spinnaker
    - Etc..

### Technology Stack for CICD

- Code -> Build -> Test -> Deploy -> Provision
- AWS CodeCommit / GitHub or 3rd party code repository
- AWS CodeBuild (no time limit) / Jenkins CI Or 3rd party CI servers
- AWS Elastic Beanstalk / AWS CodeDeploy / User Managed EC2 Instance Fleet (CloudFormation)
- Orchestrate: AWS CodePipeline

### CodeCommit Trigger for AWS Lambda

- Every push to CodeCommit can trigger a Lambda function
- The Lambda function can scan for leaked AWS credentials on every code push, and disable them automatically to remedy the issue

### Good to know - CICD

- You can use a manual approval stage in CodePipeline
- Running units tests CodeCommit + CodeBuild + CodePipeline
- Build and Store Docker Images: CodeBuild + ECR
- Automated CloudFormation deployment: CodePipeline

### CodePipeline - GitHub integration

- CodePipeline -> Regular checks -> Github
- GitHub -> HTTP Webhooks -> CodePipeline
- Github -> CodeStar Source Connection (GitHub App) -> CodePipeline

## Amazon CodeGuru

- An ML-powered service for automated code review and application performacne recommendations
- Provides two functionalities
    - CodeGurn Reviewer: automated code reviews for static code analysis (development)
        - Built-in code reviews with actionable recommendations
    - CodeGuru Profiler: visibility/recemmendations about application performance during runtime (production)
        - Detect and optimizde the expensive lines of code pre-prod
        - Identiyf performance and cost imporovements in production

### Amazon CodeGuru Reviewer

- Identify critical issues, security vulnerabilities, and hard-to-find bugs
- Example: common coding best practices, resource leaks, security detection, input validation
- Use Machine Learning and automated reasoning
- Hard-learned lessons across millions of code reviews on 1000s of open-sources and Amazon repositories
- Supports Java and Python
- Integrates with GitHub, Bitbucket, and AWS CodeCommit

### Amazon CodeGuru Profiler

- Helps understand the runtime behavior of your application
- Example: identify if your application is consuming excessive CPU capacity on a logging routine
- Features:
    - Identify and remove code inefficiencies
    - Improve application performance (e.g, reduce CPU utilization)
    - Decrease compute costs
    - Provides heap summary (identify which objects using up memory)
    - Anomaly Detection
- Support applications running on AWS or on-premise
- Minimal overhead on application

## Alexa for Business, Lex & Connect

- Alexa for Business
    - Use Alexa to help employees be more productive in meeting rooms and their desk
    - Measure and increase the utilization of meeting rooms in their workplace
- Amazon Lex (same technology that powers Alexa)
    - Automatice Speech Recognition (ASR) to convert speech to text
    - Natural Language Understanding to recoginze the intent of text, callers
    - Helps build chatbots, call center bots
- Amazon Connect
    - Receive calls, create contact flows, cloud-based virtual contact center
    - Can integrate with other CRM systems or AWS

## Kinesis Video Streams

- One video stream per streaming device (producers)
    - Seucirty cameras, body worn camera, smartphone
    - Can use a Kinesis Video Streams Producer library
- Underlying data is stored in S3 (but we don't have access to it)
- Cannot output the stream data to S3 (must build custom solution)
- Consumers
    - Consumed by EC2 instances for real time analysis, or in batch
    - Can leverage the Kinesis Video Stream Parser Library
    - Integration with AWS Rekognition for facial detection

## AWS WorkSpaces

- Managed, Secure Cloud Desktop
- Great to eliminate management of on-premise VDI (Virtual Desktop Infrastructure)
- Pricing is either on-demand (pay per hour) or monthly subscription
- Secure, Encrypted, Network Isolation
- Integrated with Microsoft Active Directory
- WorkSpaces Application Manager (WAM)
    - Deploy and Manage applications as virtualized application containers
    - Provision at scale, and keep the applications updated using WAM
- Windows Updates
    - By default, Amazon Workspaces are configured to install software updates
    - Amazon WorkSpaces with Windows will have Windows Update turned on
    - You have full control over the Windows Update frequency
- Maintenance Windows
    - Updates are installed during maintenance windows (you define them)
    - Always On WorkSpaces: default in from 00h00 to 04h00 on Sunday morning
    - AutoStop WorkSpaces: automatically starts once a month to install updates
    - Manual maintenance: you define your windows and perform maintenance

### Amazon WorkSpaces - Cross Region Redirection

- Route 53 (TXT Record, Failover Type)
- Primary Region
    - WorkSpaces Directory
    - AWS Managed Microsoft AD
- Failover Region
    - WorkSpaces Directory
    - AD Connector
    - Must use AD Connector, can't use multi-region AWS Managed Microsoft AD
- Amazon WorkDocs (persistence for user data)

### Amazon WorkSpaces - IP Access Control Groups

- Similar to security groups for Amazon WorkSpaces
- List of IP addresses / CIDR address ranges that users are authorized to connect from
- If users access WorkSpaces through VPN or NAT, the IP Access Control Group must authorize the public IP of these

## Amazon AppStream 2.0

- Desktop Application Streaming Service
- Deliver to any computer, without acquiring, provisioning infrastructure
- The application is delivered from witnin a web browser

### Amazon AppStream 2.0 vs WorkSpaces

- Workspaces
    - Fully managed VDI and desktop available
    - The users connect to the VDI and open native or WAM applications
    - Workspaces are on-demand or always on
- AppStream 2.0
    - Stream a desktop application to web browsers (no need to connect to a VDI)
    - Works with any device (that has a web browser)
    - Allow to configure an instance type per application type (CPU, RAM, GPu)

## AWS Device Farm

- Application testing service for your mobile and web applications
- Test across real browsers and real mobiles devices
- Fully automated using framework
- Improve the quality of web and mobile apps
- Generates videos and logs to document the issues encountered
- Can remotely log-in to devices for debugging

## Amazon Macie

- Amazon Macie is a fully managed data security and data privacy service that uses machine learning and pattern matching to discover and protect your sensitive data in AWS
- Macie helps identify and alert you to sensitive data, such as personally identifiable information (PII)
- S3 buckets -> analyez -> Macie (Discover Sensitive Data (PII)) -> notify -> Amazon EventBridge -> integrations

## Amazon SES

### Amazon Simple Emails Service (Amazon SES)

- Fully managed service to send emails securely, globally and at scale
- Allows inbound/outbound emails
- Reputation dashboard, performance insights, anti-spam feedback
- Provides statistics such as email deliveries, bounces, feedback loop results, email open
- Support DomainKeys Identified Mail (DKIM) and Sender Policy Framework (SPF)
- Flexible IP deployment: shared, dedicated, and customer-owned IPs
- Send emails using your application using AWS Console, APIs, or SMTP
- Use cases: transactional, marketing and bulk email communications

### Amazon SES - Configuration Sets

- Configuration sets help you to customize and analyze your email send events
- Event destinations
    - Kinesis Data Firehose: receives metrics (numbers of sends, deliveries, opens, clicks, bounces, and complaints) for each email
    - SNS: for immefiate feedback on bounce and complaint information
- IP pool management: use IP pools to send particular types of emails

## EC2 Image Builder

- Used to automate the creation of Virtual Machines or container images
- => Automated the creation, maintain, validate and test EC2 AMIs
- Can be run on a schedule (weekly, whenever packages are updated, etc...)
- Free service (only pay for the underlying resources)
- Can publish AMI to multiple regions and mutliple accounts
- EC2 Image Builder -> create -> Builder EC2 Instance (Build Components applied (customize software on instance)) -> create -> New AMI -> Test EC2 Instance (Test suite is run (is the AMI working, secure?)) -> AMI is distributed (can be multiple regions, multiple accounts)

### EC2 Image Builder - CICD Architecture

- AWS CodePipeline
- -> AWS CodeCommit -> AWS CodeBuild
- -> AWS CloudFormation -> EC2 Image Builder -> AMI
- -> AWS CloudFormation -> Rolling update -> Auto Scaling -> Instances

## AWS IoT Core

- IoT stands for "Internet of Things" - the network of internet-connected devices that are able to collect and transfer data
- AWS IoT Core allows you to easily connect IoT devices to the AWS Cloud
- Serverless, secure & scalable to billions of devices and trillions of messages
- Integrates with a lot of AWS services (Lambda, S3, SageMaker, etc..)
- Build IoT applications that gather, process, analyze, and act on data

### IoT Core - Integrations

- MQTT -> IoT Topic -> IoT Rules -> IoT Rules Actions -> AWS

### IoT Core - Kinesis Data Firehose

- MQTT -> IoT Core rule actions -> Amazon Kinesis Data Firehose -> Amazon S3, Redshift, OpenSearch

## Other Service Summary

- CodeCommit: store code in version-controlled repositories, Coe can live on multiple branches
- CodeBuild: build & test code on-demand in your CICD pipeline
- CodeDeploy: deploy code on EC2, ASG, Lambda or ECS
- CodePipeline: orchestrate CICD pipelines. If using CodeCommit as a source, matches to only one branches
- CloudSearch: managed search solution to perform a full-text search, auto-completion in your applications
- Alexa for Buiness: use Alexa to help emplyees be more productive in meeting rooms and their desk
- Lex: Automatic Speech Recognition (ASR) to convert speech to text. Helpful to build chat-bots
- Connect: receive calls, create contact flows, cloud-based virtual contact center
- Rekognition: find objects, people, text, scenes in images and videos using Machine Learning
- Kinesis Video Stream: one stream per video device, analyze using EC2 instances or Rekognition
- WorkSpaces: on-demand Windows workstations, WAM is used to manage applications
- AppStream 2.0: stream desktop applications into web browsers
- Mechanical Turk: Crowd-souring marketplace to perform simple human tasks, integration with SWF
- Device Farm: Application testing service for your mobile and web applications across real devices



































