# Monitoring

## CloudWatch

- CloudWatch Metrics
    - Provided by many AWS services
    - EC2 standard: 5 minutes, detailed monitoring: 1 minute
    - EC2 RAM is not a built-in metric
    - Can create custom metrics: standard resolution: 1 minute, high resolution: 1 sec
- CloudWatch Alarms
    - Can trigger actions: EC2 action (reboot, stop, terminate, recover), Auto Scaling, SNS
    - Alarm events can be intercepted by Amazon EventBridge
- CloudWa`tch Dashboards
    - Display metrics and alarms
    - Can show metrics of multiole regions

### CloudWatch Synthetics Canary

- Configurable script that monitor your APIs, URLs, Websites,...
- Reproduce what your customers do programmatically to find issues before customers are impacted
- Check the availability and latency of your endpoints adn can store load time data anc screenshots of the UI
- Integration with CloudWatch Alarms
- Scripts written in Node.js or Python
- Programmatic access to a headless Google Chrome browser
- Can run once or on a regular schedule

### CloudWatch Synthetics Canary Blueprints

- Heartbeat Monitor - load URL, store screenshot and an HTTP archive file
- API Canary - test basic read and write functions of REST APIs
- Broken Link Checker - check all links inside the URL that you are testing
- Visual Monitoring - compare a screenshot taken during a canary run with a baseline screenshot
- Canary Recorder - used with CloudWatch Synthetics Recorder (record your actions on a website and automatically generates a script for that)
- GUI Workflow Builder - verifies that actions can be taken on your webpage (e.g, test a webpage with a login form)

## CloudWatch Logs

### AWS CloudWatch Logs - Sources

- SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
- Elastic Beanstalk: collection of logs from application
- ECS: collection from containers
- AWS Lambda: collection from function logs
- VPC FLow Logs: VPC specific logs
- API Gateway
- CloudTrail based on filter
- CloudWatch log agents: for example on EC2 machines
- Route53: Log DNS queries

### CloudWatch Logs

- Log groups: arbitrary name, usualyy representing an application
- Log stream: instances within application / log files / containers
- Can define log expiration policies (never expire, 30 days, etc...)
- Optional KMS encryption
- CloudWatch Logs can send logs to:
    - Amazon S3 (exports)
    - Kinesis Data Streams
    - Kinesis Data Firehose
    - AWS Lambda
    - ElasticSearch

### CloudWatch Logs Metric Filter & Insights

- CloudWatch Logs can use filter expressions
    - For example, find a specific IP inside of a log
    - Or count occurrences of "ERROR" in your logs
    - Metrics filters can be used to trigger alarms
- CloudWatch Logs Insights can be used to query logs and add queries to CloudWatch Dashboards

### CloudWatch Logs - S3 Export

- S3 buckets must be encrypted with AES-256 (SSE-S3) or SSE-KMS
- Log data can take up to 12 hours to become available for export
- The API call is `CreateExportTask`
- Not near-real time or real-time...use Logs Subscriptions instead

### CloudWatch Logs Subscriptions

- CloudWatch Logs -> Subscription Filter
    - -> Lambda Function (managed by AWS) -> Real time -> Amazon ES
    - -> Kinesis Data Firehose -> Near Real Time -> Amazon S3
    - -> Kinesis Data Streams -> KDF,KDA,EC2,Lambda...
    - -> Lambda Function (custom)

### CloudWatch Logs Aggregation - Multi-Account & Multi Region

- Account A Region 1, CloudWatch Logs -> Subscription Filter -> Kinesis Data Streams -> Kinesis Data Firehose -> Near Real Time -> Amazon S3

### CloudWatch Agent - Integration with SSM

- Install CW Agent using SSM Run Command
    - Run Command -> SSM Document: `AWS-ConfigureAWSPackager`, Name: `AmazonCloudWatchAgent` -> EC2 Instance
- Install CW Agent using SSM State Manager
- Configure CW Agent by storing config. in SSM Parameter Store

## Amazon EventBridge

### Amazon EventBridge (formerly CloudWatch Events)

- Schedule: Cron jobs (scheduled scripts)
    - Schedule Every hour -> Trigger script on Lambda function
- Event Pattern: Event rules to react to a service doing something
    - IAM Root User Sign in Event -> SNS Topic Wiht Email Notification
- Trigger Lambda functions, send SQS/SNS messages,...

### Amazon EventBridge

- AWS Services -> Default Event Bus
- AWS SaaS Partners -> Partner Event Bus
- Custom Apps -> Custom Event Bus
- Event buses can be accessed by other AWS accounts using Resource-based policies
- You can archive events (all/filter) sent to an event bus (indefinitely or set period)
- Ability to replay archived events

### Amazon EventBridge - Schema Registry

- EventBridge can analyze the events in your bus and infer the schema
- The Schema Registry allows you to generate code for your application, that will know in advance how data is structured in the event bus
- Schema can be versioned

### Amazon EventBridge - Resource-based Policy

- Manage permissions for a speicfic Event Bus
- Example: allow/deny events from another AWS account or AWS region
- Use case: aggregate all events from your AWS Organization in a single AWS account or AWS region

## X-Ray

- Tracing requests across your microservices (distributed systems)
- Integrations with:
    - EC2 - install the X-Ray agent
    - ECS - install the X-Ray agent or Docker container
    - Lambda
    - Beanstalk - agent is automatically installed
    - API Gateway - helpful to debug errors (such as 504)
- The X-Ray agent or services need IAM permissions to X-Ray

## AWS Personal Health Dashboard

- Global service
- Show how AWS outages directly impact you
- Show the impact on your resources
- List issues and actions you can do to remediate them
- Will show maintenance events from AWS
- Programmatically accessible through the AWS Health API
- Aggregations across multiple accounts of an AWS Organization

### Health Event Notifications

- Use EventBridge (CloudWatch Events) to react to changes for AWS Health events in your AWS account
- Example: receive email notifications when EC2 instances in your AWS account are scheduled for updates
- Can't be used to return public events from the Service Health Dashboard
- Use cases: send notifications, capture event information, take corrective action...







