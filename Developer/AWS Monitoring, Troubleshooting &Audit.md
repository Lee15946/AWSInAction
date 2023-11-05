## AWS Monitoring & Audit

### Monitoring in AWS

- AWS CloudWatch:
    - Metrics: Collect and track key metrics
    - Logs: Collect, monitor, analyze and store log files
    - Events: Send notifications when certain events happen in your AWS
    - Alarms: React in real-time to metrics / events
- AWS X-Ray:
    - Troubleshooting application performance and errors
    - Distributed tracing of microservices
- AWS CloudTrail:
    - Internal monitoring of API calls being made
    - Audit changes to AWS Resources by your users

### Amazon CloudWatch Metrics

- CloudWatch provides metrics for every service in AWS
- Metric is a variable to monitor (CPUUtilization, NetworkIn)
- Metrics belong to namespaces
- Dimension is an attribute of a metric (instance id, environment, etc...)
- Up to 30 dimensions per metric
- Metrics have timestamps
- Can create CloudWatch dashboards of metrics
- Can create CloudWatch Custom Metrics (For the RAM for example)
- CloudWatch Metric Streams
    - Continually stream CloudWatch metrics to a destination of your choice, with near-real-time delivery and low
      latency
        - Amazon Kinesis Data Firehose (and then its destinations)
        - 3rd party service provider: Datadog, Dynatrace, New Relic, Sumo Logic
    - Option to filter metrics to only stream a subset of them

### EC2 Detailed monitoring

- EC2 instance metrics have metrics "every 5 minutes"
- With detailed monitoring (for a cost), you get data "every 1 minute"
- Use detailed monitoring if you want to scale faster for your ASG
- The AWS Free Tier allows us to have 10 detailed monitoring metrics
- Note: EC2 Memory usage is by default not pushed (must be pushed from inside the instance as a custom metric)

### CloudWatch Custom Metrics

- Possiblity to define and send your own custom metrics to CloudWatch
- Example: memory (RAM) usage, disk space, number of logged in users...
- Use API call `PutMetricData`
- Ability to use dimensions (attributes) to segment metrics
    - Instance.id
    - Environment.name
- Metric resolution (StorageResolution API parameter - two possible value):
    - Standard: 1 minutes (60 seconds)
    - High Resolution: 1/5/10/30 second(s) - Higher cost
- Important: Accept metric data points two weeks in the past and two hours in the future (make sure to configure your
  EC2 instance time correctly)

### CloudWatch Logs

- Log groups: arbitrary name, usually representing an application
- Log stream: instances within application / log files / containers
- Can define log expiration polices (never expire, 1 day to 10 years)
- CloudWatch Logs can send logs to:
    - Amazon S3 (exports)
    - Kinesis Data Streams
    - Kinesis Data Firehose
    - AWS Lambda
    - OpenSearch
- Logs are encrypted by default
- Can setup KMS-based encryption with your own keys
- CloudWatch Logs - Sources
    - SDK, CloudWatch Logs Agent, CloudWatch Unified Agent
    - Elastic Beanstalk: collection of logs from application
    - ECS: collection from containers
    - AWS Lambda: collection from function logs
    - VPC Flow Logs: VPC specific logs
    - API Gateway
    - CloudTrail: based on filter
    - Route53: Log DNS queries
- CloudWatch Logs Insights
    - Search and analyze log data stored in CloudWatch Logs
    - Example: find a specific IP inside a log, count occurrences of "ERROR" in your logs...
    - Provides a purpose-built query language
        - Automatically discovers fields from AWS services and JSON log events
        - Fetch desired event fields, filter based on conditions, calculate aggregate statistics, sort events, limit
          number of events
        - Can save queries and add them to CloudWatch Dashboards
    - Can query multiple Log Groups in difference AWS accounts
    - It's a query engine, not a real-time engine
- CloudWatch Logs - S3 Export
    - Log data can take up to 12 hours to become available for export
    - The API call is `CreateExportTask`
    - Not near-real time or real-time...use Logs Subscriptions instead
- CloudWatch Logs Subscriptions
    - Get a real-time log events from CloudWatch Logs for processing and analysis
    - Send to Kinesis Data Streams, Kinesis Data Firehose, or Lambda
    - Subscription Filter - filter which logs are events delivered to your destination
- CloudWatch Logs Aggregation: Multi-Account & MutliRegion
    - Cross-Account Subscription - send log events to resources in a difference AWS account (KDS,KDF)

### CloudWatch Logs for EC2 / CloudWatch Agent

- By default, no logs from your EC2 machine will go to CloudWatch
- You need to run a CloudWatch agent on EC2 to push the log files you want
- Make sure IAM permissions are correct
- The CloudWatch log agent can be setup on-premises too
- CloudWatch Logs Agent & Unified Agent
    - For virtual servers (EC2 instances, on-premises servers...)
    - CloudWatch Logs Agent
        - Old version of the agent
        - Can only send to CloudWatch Logs
    - CloudWatch Unified Agent
        - Collect additional system-level metrics such as RAM, processes, etc..
        - Collect logs to send to CloudWatch Logs
        - Centralized configuration using SSM Parameter Store
- CloudWatch Unified Agent - Metrics
    - Collected directly on your Linux server / EC2 instance
    - CPU (active, guest, idle, system, user, steal)
    - Disk metrics (free, used, total), DiskIO (writes, reads, bytes, iops)
    - RAM (free, inactive, used, total, cached)
    - Netstat (number of TCP and UDP connections, net packets, bytes)
    - Processes (total, dead, bloqued, idle, running, sleep)
    - Swap Space (free, used, used %)
    - Reminder: out-of-the box metrics for EC2 - disk, CPU, network (high level)

### CloudWatch Logs Metric Filter

- CloudWatch Logs can use filter expressions
    - For example, find a specific IP inside of a log
    - Or count occurrences of "ERROR" in your logs
    - Metric filters can be used to trigger alarms
- Filter do not retroactively filter data. Filters only publish the metric data points for events that happen after the
  filter was created
- Abiltiy to specify up to 3 Dimensions for the Metric Filter (optional)
- EC2 CloudWatch Logs Agent -> CloudWatch Logs -> Metric Filters -> CloudWatch Alarm -> SNS

### CloudWatch Alarms

- Alarms are used to trigger notifications for any metric
- Various options (sampling, %, max, min, etc...)
- Alarm States:
    - OK
    - INSUFFICIENT_DATA
    - ALARM
- Period:
    - Length of time in seconds to evaluate the metric
    - High resolution custom metrics: 10sec, 30sec or multiple of 60 sec
- CloudWatch Alarm Targets
    - Stop, Terminate, Reboot or Recover an EC2 instance
    - Trigger Auto Scaling Action
    - Send notification to SNS (from which you can do much anything)
- CloudWatch Alarms - Composite Alarms
    - CloudWatch Alarms are on a single metric
    - Composite Alarms are monitoring the states of mutliple other alarms
    - AND and OR conditions
    - Helpful to reduce "alarm noise" by creating complex composite alarms
- EC2 Instance Recovery
    - Status Check:
        - Instance status = check the EC2 VM
        - System status = check the underlying hardware
    - Recovery: Same Private, Public, ElasticIP, metadata, placement group
- CloudWatch Alarm: good to know
    - Alarms can be created on CloudWatch Logs Metrics Filters
    - To test alarms and notifications, set the alarm state to Alarm using CLI

### CloudWatch Synthetics Canary

- Configurable script that monitor your APIs, URLs, Websites...
- Reproduce what your customers do programmatically to find issues before customers are impacted
- Checks the availability and latency of your endpoints and can store load time data and screenshots of the UI
- Integration with CloudWatch Alarms
- Scripts written in Node.js or Python
- Programmatic access to a headless Google Chrome browser
- Can run once or on a regular schedule

### CloudWatch Synthetics Canary Blueprints

- Heartbeat Monitor - load URL, store screenshot and an HTTP archive file
- API Canary - test basic read and write functions of REST APIs
- Broken Link Checker - check all links inside the URL that you are testing
- Visual Monitoring - compare a screenshot taken during a canary run with a baseline screenshot
- Canary Recorder - used with CloudWatch Synthetics Recorder (record your actions on a website and automatically
  generate a script for that)
- GUI Workflow Builder - verifies that actions can be taken on your webpage (e.g, test a webpage with a login form)

### Amazon EventBridge (formerly CloudWatch Events)

- Schedule: Cron jobs (scheduled scripts)
- Event Pattern: Event rules to react to a service doing something
- Trigger Lambda functions, send SQS/SNS messages...
- Amazon EventBridge
    - Default Event Bus
    - Partner Event Bus
    - Custom Event Bus
    - Event buses can be accessed by other AWS accounts using Resource-based Policies
    - You can archive events (all/filter) sent to an event bus (indefinitely or set period)
    - Ability to replay archived events
- Amazon EventBridge - Schema Registry
    - EventBridge can analyze the events in your bus and infer the schema
    - The Schema Registry allows you to generate code for your application, that will know in advance how data is
      structured in the event bus
    - Schema can be versioned
- Amazon EventBridge - Resource-based Policy
    - Manage permissions for a specific Event Bus
    - Example: allow/deny events from another AWS account or AWS region
    - Use case: aggregate all events from your AWS Organization in a single AWS account or AWS region

### CloudWatch Insights and Operational Visibility

- CloudWatch Container Insights
    - Collect, aggregate, summarize metrics and logs from containers
    - Available for containers on..
        - Amazon Elastic Container Service (Amazon ECS)
        - Amazon Elastic Kubernetes Services (Amazon EKS)
        - Kubernetes platforms on EC2
        - Fargate (both for ECS and EKS)
    - In Amazon EKS and Kubernetes, CloudWatch Insights is using a containerized version of the CloudWatch Agent to
      discover containers
- CloudWatch Lambda Insights
    - Monitoring and troubleshooting solution for serverless applications running on AWS Lambda
    - Collects, aggregates, and summarizes systme-level metrics including CPU time, memory, disk and network
    - Collects, aggregates and summarizes diagnostic information such as cold starts and Lambda worker shutdowns
    - Lambda Insights is provided as a Lambda layer
- CloudWatch Contributor Insights
    - Analyze log data and create time series that display contributor data
        - See metircs about the top-N contributors
        - The total number of unique contributors, and their usage
    - This helps you find top talkers and understand who or what is impacting system performance
    - Works for any AWS-generated logs (VPC, DNS, etc...)
    - For example, you can find bad hosts, identify the heaviest network users, or find the URLs that generate the most
      errors
    - You can build your rules from scratch, or you can also use sample rules that AWS has created - leverages your
      CloudWatch Logs
    - CloudWatch also provides built-in rules that you can use to analyze metrics from other AWS services
- CloudWatch Application Insights
    - Provides automated dashboards that show potential problems with monitored applications, to help isolate ongoing
      issues
    - Your application run on Amazon EC2 instances with select technologies only (JAVA, .NET, Microsoft IIS Web Server,
      databases)
    - And you can use other AWS resources such as Amazon EBS, RDS, ELB, ASG, Lambda, SQS, DynamoDB, S3 bucket, ECS, EKS,
      SNS, API Gateway...
    - Powered by SageMaker
    - Enhanced visibility into your application health to reduce the time it will take you to troubleshoot and repair
      your applications
    - Findings and alerts are sent to Amazon EventBridge and SSM OpsCenter
- CloudWatch Insights and Operational Visibility
    - CloudWatch Container Insights
        - ECS, EKS, Kubernetes on EC2, Fargate, needs agent for Kubernetes
        - Metrics and logs
    - CloudWatch Lambda Insights
        - Detailed metrics to troubleshoot serverless applications
    - CloudWatch Contributors Insights
        - Find "Top-N" Contributors through CloudWatch Logs
    - CloudWatch Application Insights
        - Automatic dashboard to troubleshoot your application and related AWS services

### AWS X-Ray

- Debugging in Production, the good old way:
    - Test locally
    - Add log statements everywhere
    - Re-deploy in production
- Log formats differ across applications using CloudWatch and analytics is hard
- Debugging: monolith "easy", distributed services "hard"
- No common views of your entire architecture
- Enter... AWS X-Ray!
- AWS X-Ray advantages
    - Troubleshooting performance (bottlenecks)
    - Understand dependencies in a microservice architecture
    - Pinpoint service issues
    - Find errors and exceptions
    - Are we meeting time SLA?
    - Where I am throttled
    - Identify users that are impacted
- X-Ray compatibility
    - AWS Lambda
    - Elastic Beanstalk
    - ECS
    - ELB
    - API Gateway
    - EC2 Instances or any application server (even on premise)
- AWS X-Ray Leverages Tracing
    - Tracing is an end to end way to follow a "request"
    - Each componenet dealing with the request adds its own "trace"
    - Tracing is made of segments (+ sub segments)
    - Annotations can be added to traces to provide extra-information
    - Ability to trace
        - Every request
        - Sample request (as a % for example or a rate per minute)
    - X-Ray Security:
        - IAM for authorization
        - KMS for encryption at rest
- AWS X-Ray How to enable it?
    1. Your code (Java, Python, Go, Node.js, .NET) must import the AWS X-Ray SDK
        - Very little code modification needed
        - The application SDK will then capture
            - Calls to AWS services
            - HTTP / HTTPS requests
            - Database Calls (MySQL, PostgreSQL, DynamoDB)
            - Queue calls (SQS)
    2. Install the X-Ray daemon or enable X-Ray AWS Integration
        - X-Ray daemon works as a low leve UPD packer interceptor (Linux / Windows / Mac...)
        - AWS Lambda / other AWS services already run the X-Ray daemon for you
        - Each application must have the IAM rights to write data to X-Ray
- The X-Ray magic
    - X-Ray service collects data from all the different services
    - Service map is computed from all the segments and traces
    - X-Ray is graphical, so even non technical people can help troubleshoot
- AWS X-Ray Troubleshooting
    - If X-Ray is not working on EC2
        - Ensure the EC2 IAM Role has the proper permissions
        - Ensure the EC2 instance is running the X-Ray Daemon
    - To enable on AWS Lambda:
        - Ensure is has an IAM execution role with proper policy (AWSX-RayWriteOnlyAccess)
        - Ensure that X-Ray is imported in the code
        - Enable Lambda X-Ray Active Tracing

### X-Ray: Instrumentation and Concepts

- X-Ray Instrumentation in your code
    - Instrumentation means the measure of product's performance, diagnose errors, and to write trace information
    - To instrument your application code, you use the X-Ray SDK
    - Many SDK require only configuration changes
    - You can modify your application code to customize and annotation the data that the SDK sends to X-Ray, using
      interceptors, filters, handlers, middleware
- X-Ray Concepts
    - Segments: each application / service will send them
    - Subsegments: if you need more details in your segment
    - Trace: segments collected together to form an end-to-end trace
    - Sampling: decrease the amount of requests sent to X-Ray, reduce cost
    - Annotations: Key Value pairs used to index traces and use with filters
    - Metadata: Key Value pairs, not indexed, not used for tracing
    - The X-Ray daemon / agent has a config to send traces cross account:
        - Make sure the IAM permissions are correct - the agent will assume the role
        - This allows to have a central account for all your application tracing
- X-Ray Sampling Rules
    - With sampling rules, you control the amount of data that you record
    - You can modify sampling rules withour changing your code
    - By default, the X-Ray SDK records the first request each second, and five percent of any addtionnal requests
    - One request per second is the reservoir,which ensures that at least one trace is recorded each second as long the
      service is serving requests
    - Five percent is the rate at which additional requests beyond the reservoir size are samples
- X-Ray Custom Sampling Rules
    - You can create your own rules with the reservoir and rate

### X-Ray APIs

- X-ray Write APIs (used by the X-Ray daemon)
    - PutTraceSegments: Uploads segment documents to AWS X-Ray
    - PutTelemetryRecords: Used by the AWS X-Ray daemon to upload telemetry
        - SegmentsReceivedCount
        - SegmentsRejectedCounts
        - BackendConnectionErrors
    - GetSamplingRules: Retrieve all sampling rules (to know what/when to send)
    - GetSamplingTargets & GetSamplingStatisticSummaries: advanced
- X-Ray Read APIs - continued
    - GetServiceGraph: main graph
    - BatchGetTraces: Retrieves a list of traces specified by ID. Each trace is a collection of segment documents that
      originates from a single request
    - GetTraceSummaries: Retrieves IDs and annotations for traces available for a specified time frame using an optional
      filter. To get the full traces pass the trace IDs to BatchGetTraces
    - GetTraceGraph: Retrieves a service graph for one or more specific trace IDs

### X-Ray with Elastic Beanstalk

- AWS Elastic Beanstalk platforms include the X-Ray daemon
- You can run the daemon by setting an option in the Elastic Beanstalk console or with a configuration file (in
  .ebextensions/xray-daemon.config)

```yaml
options_settings:
  aws:elasticbeanstalk:xray:
    XRayEnabled: true
```

- Make sure to give your instance profile the correct IAM permissions so that the X-Ray daemon can function correctly
- Then make sure your application code is instrumented with X-Ray SDK
- Note: The X-Ray daemon is not provided for Multicontainer Docker

### X-RAy & ECS

- ECS + X-Ray integration options
    - ECS Cluster
        - X-Ray Container as a Daemon
        - X-Ray Container as a "Side Car"
    - Fargate Cluster
        - X-Ray Container as a "Side Car"
- ECS + X-Ray: Example Task Definition
    - portMappings
    - environment: AWS_XRAY_DAEMON_ADDRESS
    - links: xray-daemon

### AWS Distro for OpenTelemetry

- Secure, production-ready AWS-supported distribution of the open-source project OpenTelemetry project
- Provides a single set of APIs, libraries, agents, and collector services
- Collects distributed traces and metrics from your apps
- Collect metadata from your AWS resources and services
- Auto-instrumentation Agents to collect traces without changing your code
- Send traces and metrics to multiple AWS services and partner solutions
    - X-Ray, CloudWatch, Prometheus
- Instrument your apps running on AWS (e.g, EC2, ECS, EKS, Fargate, Lambda) as well as on-premises
- Migrate from X-Ray to AWS Distro for Telemetry if you want to standardize with open-soruce APIs from Telemetry or send
  traces to multiple destinations simultaneously

### AWS CloudTrail

- Provides governance, compliance and audit for your AWS Account
- CloudTrail is enabled by default
- Get an history of events/API calls made within your AWS Account by:
    - Console
    - SDK
    - CLI
    - AWS Services
- Can put logs from CloudTrail into CloudWatch Logs or S3
- A trail can be applied to All Regions(default) or single Region
- If a resource is deleted in AWS, investigate CloudTrail first!
- CloudTrail Events
    - Management Events
        - Operations that are performed on resources in your AWS account
        - Examples:
            - Configuring security (IAM AttachRolePolicy)
            - Configuring rules for routing data (Amazon EC2 CreateSubnet)
            - Setting up logging (AWS CloudTrail CreateTrail)
        - By default, trails are configured to log management events
        - Can seperate Read Events (that don't modify resources) from Write Events (that may modify resources)
    - Data Events
        - By default, data events are not logged (because high volume operations)
        - Amazon S3 object-level activity (ex: GetObject, DeleteObject, PutObject): can seperate Read and Write Events
        - AWS Lambda function execution activity (the Invoke API)
    - CloudTrail Insights Events
        - Enable CloudTrail Insights to detect unusual activity in your account
            - inaccurate resource provisioning
            - hitting service limits
            - Bursts of AWS IAM actions
            - Gaps in periodic maintenance activity
        - CloudTrail Insights analyzes normal mangement events to create a baseline
        - And then continuously analyzes write events to detect unusual patterns
            - Anomalies appear in the CloudTrail console
            - Event is sent to Amazon S3
            - An EventBridge event is generated (for automation needs)
    - CloudTrail Events Retention
        - Events are stored for 90 days in CloudTrail
        - To keep events beyon this period, log them to S3 and use Athena

### Amazon EventBridge- Intercept API calls

- User -> AssumeRole -> API Call logs -> CloudTrail -> EventBridge -> SNS
- USer -> edit SG Inbound Rules -> API Call logs -> CloudTrail -> EventBridge -> SNS
- USer -> DeleteTable API Call -> Log API call -> CloudTrail -> Amazon EventBridge -> SNS

### CloudTrail vs CloudWatch vs X-Ray

- CloudTrail:
    - Audit API calls made by users /services / AWS console
    - Useful to detect unauthorized calls or root cause of changes
- CloudWatch
    - CloudWatch Metrics over time for monitoring
    - CloudWatch Logs for storing application log
    - CloudWatch Alarms to send notifications in case of unexpected metrics
- X-Ray:
    - Automated Trace Analysis & Central Service Map Visualization
    - Latency, Errors and Fault analysis
    - Request tracking across distributed systems

### AWS Config

- Helps with auditing and recording compliance of your AWS resources
- Helps record configuration and changes over time
- Questions that can be solved by AWS Config
    - Is there unrestricted SSH access to my security groups?
    - Do my buckets have public access
    - How has my ALB configuration changed over time?
- You can receive alerts (SNS notifications) for any changes
- AWS Config is a per-region service
- Can be aggregated across regions and accounts
- Possiblity of storing the configuration data into S3 (analyzed by Athena)
- Config Rules
    - Can use AWS managed config rules (over 75)
    - Can make custom config rules (must be defined in AWS Lambda)
        - Ex: evaluate if each EBS dish is of type gp2
        - Ex: evaluate if each EC2 instance is t2.micro
    - Rules can be evaluated/triggered
        - For each config change
        - And/Or: at regualr time intervals
    - AWS Config Rules does not prevent actions from happening (no deny)
    - Pricing: no free tier, $0.003 per configuration item recorded per region, $0.001 per config rule evaluation per
      region
- AWS Config Resource
    - View compliance of a resource over time
    - View configuration of a resource over time
    - View CloudTrail API calls of a resource over time
- Config Rules - Remediation
    - Automate remediation of non-compliant resources using SSM Automation Documents
    - Use AWS-Managed Automation Documents or create custom Automation Documents
        - You can create Automation Documents that invokes Lambda function
    - You can set Remediation Retries if the resource is still non-compliant after auto-remediation
- Config Rules - Notification
    - Use EventBridge to trigger notifications when AWS resources are non-compliant
    - Ability to send configuration changes and compliance state notifications to SNS (all events - use SNS Filtering or
      filter at client-side)

### CloudWatch vs CloudTrail vs Config

- CloudWatch
    - Performance monitoring (metrics, CPU, network, etc...) & dashboards
    - Events & Alerting
    - Log Aggregation & Analysis
- CloudTrail
    - Record API calls made within your Account by everyone
    - Can define trails for specific resources
    - Global Service
- Config
    - Record configuration changes
    - Evaluate resources against compliance rules
    - Get timeline of changes and compliance
