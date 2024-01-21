# Service Communication

## AWS Step Functions

- Build serverless visual workload to orchestrate your Lambda functions
- Represent flow as a JSON state machine
- Features: sequence, parallel, conditions, timeouts, error handling...
- Maximum execution time of 1 year
- Possibility to implement human approval feature
- If you chain Lambda functions using Step Functions, be mindful od the added latency to pass the calls

### Step Function Integrations

- Optimized Integrations
    - Can invoke a Lambda function
    - Run as AWS Batch job
    - Run an ECS task and wait for it to complete
    - Insert an item from DynamoDB
    - Publish message to SNS, SQS
    - Launch an EMR, Glue or SageMaker jobs
    - Launch another Step Function workflow
- AWS SDK Integrations
    - Access 200+ AWS services from your State Machine
    - Works like standard AWS SDK API call

### Step Functions Workflow Triggers

- You can invoke a Step Function Workflow (State Machine) using:
    - AWS Management Console
    - AWS SDK (`StartExectuin` API call)
    - AWS CLI (`start-exectuion`)
    - AWS Lambda (`StartExectuion` API call )
    - API Gateway
    - EventBridge
    - CodePipeline
    - Step Functions

### Step Functions - Tasks

- Lambda Tasks
    - Invoke a Lambda function
- Activitiy Tasks
    - Activity worker (HTTP), EC2 Instances, mobile devices, on premise DC
- Service Tasks
    - Connect to a supported AWS service
    - Lambda function, ECS Task, Fargate, DynamoDB, Batch job, SNS topic, SQS queue
- Wait Task
    - To wait for a duration or until a timestamp
- Note: Step Functions does not integration natively with AWS Mechanical Turk

### Step Functions - Standard vs Express

- Standard Workflows
    - Maximum duration - 1 year
    - Supported execution start date - over 2000 per second
    - Supported state transition rate - over 4000 per second per account
    - Pricing - Priced per state transition. A state transition is comunted each time a step in your execution is
      completed (more expensive)
    - Execution history — Executions can be listed and described with Step Functions APIs, and visually debugged through
      the console. They can alos be inspected in CloudWatch Logs by enabling logging on your state machine
    - Execution semantics - Exactly-once workflow execution
- Express Workflows
    - Maximum duration - 5 minutes
    - Supported execution start date - over 100,000 per second
    - Supported state transition rate - Nearly unlimited
    - Pricing - Priced by the number of executions you run, their duration, and memory consumption (cheaper)
    - Execution history - Executions can be inspected in CloudWatch Logs by enabling logging in your state machine
    - Execution semantics - At-least-once workflow execution

### Step Functions - Express Workflow - Synchronous vs. Asynchronous

- Synchronous Express Workflows
    - Wait until the Workflow completes, then return the result
    - Examples: orchestrate microservices, handle errors, retries, parallel tasks,...
- Asynchronous Express Workflows
    - Doesn't for the workflow to complete
    - Examples: Workflows that don't require immediate response, messaging...

### Step Functions - Error Handling

- You can enable error handling, retries, and add alerting to Step Function State Machine
- Example: set up EventBridge to alert via email if a State Machine execution fails

### Step Functions - Solution Architecture

- Rest API Call -> API gateway -> Service proxy
- EventBridge -> Trigger
- AWS SDK / CLI -> Trigger
- -> Step Functions

## SQS

- Serverless, manager queue, integrated with IAM
- Can handle extreme scale, no provisioning required
- Used to decouple services
- Message size of max 256 KB (use a pointer to S3 for large messages)
- Can be read from EC2 (optional ASG), Lambda
- SQS could be used as a write buffer for DynamoDB
- SQS FIFO:
    - receive messages in order they were sent
    - 300 messages/s without batching, 3000/s with batching

### Amazon SQS - Dead Letter Queue

- If a customer fials to process a message within the Visibility Timeout... the message goes back to the queue
- We can set a threshold of how many times a message can go back to the queue
- After the `MaximumRecives` threshold is exceeded, the message goes into a dead letter queue (DLQ)
- UUseful for debugging!
- DLQ of a FIFO queue must also be a FIFO queu
- DLQ of a Standard queue must also be a Standard queu
- Make sure to process the messages in the DLQ before they expire
    - Good to set a retention of 14 days in the DLQ

### SQS DLQ - Redrive to Source

- Feature to help consume in the DLQ to understand what is wrong with them
- When our code is fixed, we can redrive the messages from the DLQ back into the source queue (or any other queue) in
  batches without writing custom code

### SQS - Solution Architecture - Idempotency

- Messages can be processed twice by consumer (in case of failure, timeouts, etc)
- To hedge against that problem, implement idempotency at the consumer level
- Means the same action done twice by the consumer won't duplicate the effect

### Lambda - Event Source Mapping - SQS & SQS FIFO

- Event Source Mapping will poll SQS (Long Polling)
- Specify batch size (1-10 messages)
- Recommended: Set the queue visibility timeout to 6x the timeout of your Lambda function
- To use a DLQ
    - set-up on the SQS queue, not Lambda (DLQ for Lambda is only for async invocations)
    - Or use a Lambda destination for failures

### Lambda - Event Source Mapping - Request / Response queue (async)

- Client -> SQS Request Queue -> Work Processor -> SQS Response Queue -> Client
- Decoupling
- Fault-Tolerance
- Load Balancing

## Amazon MQ

- SQS, SNS are "cloud-native" services: proprietary protocols from AWS
- Traditional applications running from on-premises may use open protocols such as: MQTT, AMQP, STOMP, Openwire, WSS
- When migrating to the cloud, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- Amazon MA is a managed message broker service for RabbitMQ and ActiveMQ
- Amazon MQ doesn't "scale" as much as SQS /SNS
- Amazon MQ runs on servers, can run in Multi-AZ with failover
- Amazon MQ has both queue features (~SQS) and topic features (~SNS)

### Amazon MQ - Re-platform

- IBM MQ, TIBCO EMS, Rabbit MQ, and Apache ActiveMQ can be migrated to Amazon MQ

## Amazon SNS

- The "event producer" only sends message to one SNS topic
- As many "event receivers" (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new featue to filter messages)
- Up to 12,500,000 subscriptions per topic
- 100,000 topic limit
- SNS -> publish -> SQS, Lambda, Kinesis Data Firehose, Emails, SMS & Mobile Notifications, HTTP(S) Endpoints

### SNS integrates with a lot of AWS services

- Many AWS servies can send data fdoirectly to SNS for notifications
- CloudWatch Alarms, AWS Budgets, Lambda, ASG (Notifications), S3 Bucket (Events), DynamoDB
- CloudFormation (State Changes), AWS DMS (New Replic), RDS Events

### AWS SNS - How to publish

- Topic Publish (using the SDK)
    - Create a topic
    - Create a subscription (or many)
    - Publish to the topic
- Direct Publish (for mobile apps SDK)
    - Create a platform application
    - Create a platform endpoint
    - Publish to the platform endpoint
    - Works with Google GSM, Apple APNS, Amazon ADM

### Amazon SNS - Security

- Encryption
    - In-flight encryption using HTTPS API
    - At-rest encryption using KMS keys
    - Client-side encryption if the client wants to perform encryption/decryption itselft
- Access Controls
    - IAM policies to regulate access to the SNS API
- SNS Access Policies (similar to S3 bucket policies)
    - Useful for cross-account access to SNS topics
    - Useful for allowing other services (S3..) to write to an SNS topic

### Amazon SNS - SQS Fan Out Pattern

- Push once in SNS, receive in all SQS queues that are subsribers
- Fully decoupled, no data loss
- SQS allows for: data persistence, deplayed processing and retries of work
- Ability to add more SQS subsribers over tiem
- Make sure your SQS queue access policy allows for SNS to write
- Cross-Region Delivery: works with SQS Queues in other regions

### Application: S3 Events to multiple queues

- For the same combination of: event type (e.g. object create) and prefix (e.g. images/) you can only have one S3 Event
  rule
- If you want to send the same S3 event to many SQS queues , use fan-out

### Application: SNS to Amazon S3 through Kinesis Data Firehose

- SNS can send to Kinesis and therefore we can have the following solutions architecture
- Buying Service -> SNS Topic -> Kinesis Data Firehose -> Amazon S3, Any supported KDF destination

### Amazon SNS - FIFO Topic

- FIFO = First In First Out (oerding of messages in the topic)
- Similar features as SQS FIFO
    - Ordering by Message Group ID (all messages in the same group are ordered)
    - Deduplication using a Deduplication ID or Content Based Deduplication
- Can have SQS Standard and FIFO queues as subscribers
- Limited throughput (same throughput as SQS FIFO)

### SNS FIFO + SQS FIFO: Fan out

- In case you need fan out + ordering + deduplication

### SNS - Message Filtering

- JSON policy used to filter messages sent to SNS topic's subscriptions
- If a subscription doesn't have a filter policy, it receives every message

## Amazon SNS - Message Delivery Retries

- When a message is delivered to an SNS subscriber, in case of server-side errors, a delivery policy is applied

### SNS - Custom Delivery Policies

- Only HTTP/S supports custom policies

### SNS - Dead Letter Queues

- After exhausting the delivery policy, messages that haven't been delivered are discarded unless you set a DLQ (Dead
  Letter Queue)
- DLQ are Amazon SQS queues or Amazon SQS FIFO queues (for SNS FIFO)
- Dead Letter Queues are attached to a subscriptions (rather than a topic)


