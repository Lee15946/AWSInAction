## AWS Integration & Messaging

### Section Introduction

- When we start deploying multiple applications, they will inevitably need to communicate with one other
- There are two patterns of application communication
    - Synchronous communications (application to application)
    - Asynchronous / Event based (application to queue to application)
- Synchronous between applications can be problematic if there are sudden spikes of traffic
- What if you need to suddenly encode 1000 vid eos but usually it's 10?
- In that case, it's better to decouple your applications
    - Using SQS: queue model
    - Using SNS: pub/sub model
    - Using Kinesis: real-time streaming model
- These services can scale independently of our application

### Amazon SQS

- What's a queue?
    - Producer -> Send messages -> SQS Queue -> Poll messages -> Consumer
- Standard Queue
    - Oldest offering (over 10 years old)
    - Fully managed service, used to decouple applications
    - Attributes:
        - Unlimited throughput, unlimited number of messages in queue
        - Default retention of messages: 4 days, maximum of 14 days
        - Low latency (<10 ms on publish and receive)
        - Limitation of 256KB per message sent
    - Can have duplicate messages (at least once delivery, occasionally)
    - Can have out of order messages (best effort ordering)
- SQS - Producing Messages
    - Produced to SQS using the SDK (SendMessage API)
    - The message is persisted in SQS until a consumer deletes it
    - Message retention: default 4 days, up to 14 days
    - SQS standard: unlimited throughput
- SQS - Consuming Messages
    - Consumers (running on EC2 instances, servers, or AWS Lambda)
    - Poll SQS for messages (receive up to 10 messages at a time)
    - Process the messages (example: insert the message into an RDS database)
    - Delete the messages using the DeleteMessage API
- SQS - Multiple EC2 Instances Consumers
    - Consumers receive and process messages in parallel
    - At least once delivery
    - Best-effort message ordering
    - Consumers delete messages after processing them
    - We can scale consumers horizontally to improve throughput of processing
- SQS with Auto Scaling Group
    - CloudWatch Metric - Queue Length
        - ApproximateNumberOfMessages
    - CloudWatch Alarm
    - ASG
- SQS to decouple between application tiers
- Amazon SQS - Security
    - Encryption:
        - In-flight encryption using HTTPS API
        - At-rest encryption using KMS keys
        - Client-side encryption if the client wants to perform encryption/decryption itself
    - Access Controls: IAM policies to regulate access to the SQS API
    - SQS Access Policies (similar to S3 bucket policies)
        - Useful for cross-account access to SQS queues
        - Useful for allowing other services (SNS, S3...) to write to an SQS queue

### SQS - Message Visibility Timeout

- After a message is polled by a consumer, it becomes invisible to other consumers
- By default, the "message visibility timeout" is 30 seconds
- That means the message has 30 seconds to be processed
- After the message visibility timeout is over, the message is "visible" in SQS
- If a message is not processed within the visibility timeout, it will be processed twice
- A consumer could call the ChangeMessageVisibility API to get more time
- If visibility timeout is high(hours), and consumer crashed, re-processing will take time
- If visibility timeout is too low(seconds), we mat get duplicates

### Amazon SQS - Dead Letter Queues

- If a consumer fails to process a message within the Visibility Timeout.., the message goes back to the queue
- We can set a threshold of how many times a message can go back to the queue
- After the MaximumReceives threshold is exceeded, the message goes into a dead letter queue (DLQ)
- Useful for debugging
- DLQ of a FIFO queue must also be a FIFO queue
- DLQ of a Standard queue must also be a Standard queue
- Make sure to process the messages in the DLQ before they expire:
    - Good to set a retention of 14 days in the DLQ
- SQS DLQ - Redrive to Source
    - Feature to help consume messages in the DLQ to understand what is wrong with them
    - When our code is fixed, we can redrive the messages from the DLQ back into the source queue (or any other queue)
      in batches without writing custom code

### Amazon SQS - Delay Queue

- Delay a message (consumers don't see it immediately) up to 15 minutes
- Default is 0 seconds (message is available right away)
- Can set a default at queue level
- Can override the default on send using the DelaySeconds parameter

### Amazon SQS - Long Polling

- When a consumer requests messages form the queue, it can optionally "wait" for messages to arrive if there are none
  in the queue
- This is called Long Polling
- LongPolling decreases the number or API calls made to SQS while increasing the efficiency and latency of your
  application
- The wait time can be between 1 sec to 20 sec (20 sec preferable)
- Long Polling is preferable to Short Polling
- Long polling can be enabled at the queue level or at the API level using WaitTimeSeconds

### SQS Extended Client

- Message size limit is 256KB, how to send large message, e.g. 1GB?
- Using the SQS Extended Client (Java Library)
    - Producer send large message to S3
    - Produce send small metadata message to SQS queue
    - Consumer receive small metadata message and retrieve large message from S3

### SQS - Must know API

- CreateQueue (MessageRetentionPeriod), DeleteQueue
- PurgeQueue: delete all the messages in queue
- SendMessage (DelaySeconds), ReceiveMessage, DeleteMessage
- MaxNumberOfMessages: default 1, max 10 (for ReceiveMessageAPI)
- ReceiveMessageWaitTimeSeconds: Long Polling
- ChangeMessageVisibility: change the message timeout
- Batch APIs for SendMessage, DeleteMessage, ChangeMessageVisibility helps decrease your costs

### SQS - FIFO Queue

- FIFO = First In First Out (ordering of messages in the queue)
- Limited throughput: 300 msg/s without batching, 3000 msg/s with
- Exactly-once send capability (by removing duplicates)
- Messages are processed in order by consumer
- Queue name must end with .fifo

### SQS- FIFO Queue Advanced

- Deduplication
    - De-duplication interval is 5 minutes
    - Tow de-duplication methods:
        - Content-based deduplication: will do a SHA-256 hash of the message body
        - Explicitly provide a Message Deduplication ID
- Message Grouping
    - If you specify the same value of MessageGroupID in an SQS FIFO queue, you can only have one consumer, and all the
      messages are in order
    - To get ordering at the level of a subset of messages, specify different values for MessageGroupID
        - Messages that share a common Message Group ID will be in order within the group
        - Each Group ID can have a different consumer (parallel processing!)
        - Ordering across groups is not guaranteed

### SQS + Auto Scaling Group

- SQS with Auto Scaling Group (ASG)
- SQS as a buffer to database writes
- SQS to decouple between application tiers

### Amazon SNS

- The event producer only sends message to one SNS topic
- As many event receivers (subscriptions) as we want to listen to the SNS topic notifications
- Each subscriber to the topic will get all the messages (note: new feature to filter messages)
- Up to 12500000 subscriptions per topic
- 100000 topics limit
- Many AWS services can send data directly to SNS for notifications
- How to publish
    - Topic Publish (using the SDK)
        - Create a topic
        - Create a subscription (or many)
        - Publish to the topic
    - Direct Publish (for mobile apps SDK)
        - Create a platform application
        - Create a platform endpoint
        - Publish to the platform endpoint
        - Works with Google GCM, Apple APNS, Amazon ADM...
- Security
    - Encryption:
        - In-flight encryption using HTTPS API
        - At-rest encryption using KMS keys
        - Client-side encryption if the client wants to perform encryption/decryption itself
    - Access Controls: IAM policies to regulate access to the SNS API
    - SNS Access Policies (similar to S3 bucket policies)
        - Useful for cross-account access to SNS topics
        - Useful for allowing other services to write to an SNS topic

### SNS and SQS - Fan Out Pattern

- SNS + SQS: Fan Out
    - Push once in SNS, receive in all SQS queues that are subscribers
    - Fully decoupled, no data loss
    - SQS allows for data persistence, delayed processing and retries of work
    - Ability to add more SQS subscribers over time
    - Make sure your SQS queue access policy allows for SQS to write
    - Cross-Region Delivery: works with SQS Queues in other regions
- Application: S3 Events to multiple queues
    - For the same combination of: event type(e.g: object create) and prefix(e.g: images/) you can only have one S3
      Event rule
    - If you want to send the same S3 event to many SQS queues, use fan-out
- Application: SNS to Amazon S3 through Kinesis Data Firehose
    - SNS can send to Kinesis, and therefore we can have the following solutions architecture
- Amazon SNS - FIFO Topic
    - FIFO = First In First Out (ordering of messages in the topic)
    - Similar features as SQS FIFO:
        - Ordering by Message Group ID (all messages in the same group are ordered)
        - Deduplication using a Deduplication ID or Content Based Deduplication
    - Can only have SQS FIFO queues as subscribers
    - Limited throughput (same throughput as SQS FIFO)
    - SNS FIFO + SQS FIFO: Fan Out
        - In case you need fan out + ordering + deduplication
- SNS - Message Filtering
    - JSON policy used to filter messages sent to SNS topic's subscriptions
    - If a subscription doesn't have a filter policy, it receives every message

### Amazon Kinesis - Overview

- Make it easy to collect, process and analyze streaming data in real-time
- Ingest real-time data such as: Application logs, Metrics, Website click-streams, IoT telemetry data...
- Kinesis Data Streams: capture, process, and store data streams
- Kinesis Data Firehose: load data streams into AWS data stores
- Kinesis Data Analytics: analyze data streams with SQL or Apache Flink
- Kinesis Video Streams: capture, process and store video streams

### Kinesis Data Streams

- Applications/Client/SDk,KPL/Kinesis agent as Producers
    - Can produce Record
        - Partition Key
        - Data Blob (up to 1MB)
        - 1MB/sec or 1000 msg/sec per shard
- Can scale # of shards
- Apps(KCL, SDK)/Lambda/Kinesis Data Firehose/Kinesis Data Analytics
    - Can consume Record
        - Partition Key
        - Sequence no.
        - Data Blob
        - 2MB/sec (shard) Per shard all consumers
        - Or 2 MB/sec (enhanced) Per shard per consumer （enhanced fan-out)
- Retention between 1 day to 365 days
- Ability to reprocess (replay) data
- Once data is inserted in Kinesis, it can't be deleted (immutability)
- Data that shares the same partition goes to the same shard (ordering)
- Producers: AWS SDK, Kinesis Producer Library (KPL), Kinesis Agent
- Consumers:
    - Write your own: Kinesis Client Library (KCL), AWS SDK
    - Managed: AWS Lambda, Kinesis Data Firehose, Kinesis Data Analytics
- Capacity Modes
    - Provisioned mode:
        - You choose the number of shards provisioned, scale manually or using API
        - Each shard gets 1MB/s in (or 1000 records per second)
        - Each shard gets 2MB/s out (classic or enhanced fan-out consumer)
        - You pay per shard provisioned per hour
    - On-demand mode:
        - No need to provision or manage the capacity
        - Default capacity provisioned (4MB/s in or 4000 records per second)
        - Scales are automatically based on observed throughput peak during the last 30 days
        - Pay per stream per hour & data in/out per GB
- Kinesis Data Streams Security
    - Control access / authorization using IAM policies
    - Encryption in flight using HTTPS endpoints
    - Encryption at rest using KMS
    - You can implement encryption/decryption of data on client side (harder)
    - VPC Endpoints available for Kinesis to access within VPC
    - Monitor API calls using CloudTrail

### Kinesis Producers

- Puts data records into data streams
- Data record consists of:
    - Sequence number (unique per partition-key within shard)
    - Partition key (must specify while put records into stream)
    - Data blob (up to 1 MB)
- Producers:
    - AWS SDK: simple producer
    - Kinesis Producer Library (KPL): C++, Java, batch, compression, retries
    - Kinesis Agent: monitor log files
- Write throughput: 1MB/sec or 1000 records/sec per shard
- PtRecord API
- Use batching with PutRecords API to reduce costs & increase throughput
- Example:
    - Partition Key = Device Id -> Hash function -> Same partition key = Same shard
    - 1 MB/sec or 1000 records/sec per shard
    - Use highly distributed partition key to avoid "hot partition"
- Kinesis - ProvisionedThroughputExceeded
    - 2MB/sec -> ProvisionedThroughputExceeded
    - Solution:
        - Use highly distributed partition key
        - Retries with exponential backoff
        - Increase shards (scaling)

### Kinesis Data Streams Consumers

- Get data records from data streams and process them
    - AWS Lambda
    - Kinesis Data Analytics
    - Kinesis Data Firehose
    - Custom Consumer (AWS SDK) - Classic or Enhanced Fan-Out
    - Kinesis Client Library (KCL): library to simplify reading from data stream
- Kinesis Consumers - Custom Consumer
    - Shared (Classic) Fna-out Consumer
        - 2 MB/sec per shard across all consumers
        - GetRecords() API
    - Enhanced Fan-out Consumer
        - 2 MB/sec per consumer per shard
        - SubscribeToShard() API
- Kinesis Consumers Types
    - Shared (Classic) Fan-out Consumer - pull
        - Low number of consuming applications
        - Read throughput: 2MB/sec per shard across all consumers
        - Max 5 GetRecords API calls/sec
        - Latency ~200 ms
        - Minimize cost ($)
        - Consumer poll data from Kinesis using GetRecords API call
        - Returns up to 10 MB (then throttle for 5 seconds) or up to 1000 records
    - Enhanced Fan-out Consumer - push
        - Multiple consuming applications for the sam stream
        - 2MB/sec per consumer per shard
        - Latency ~70 ms
        - Higher costs ($$$)
        - Kinesis pushes data to consumers over HTTP/2 (SubscribeToShard API)
        - Soft limit of 5 consumer applications (KCL) per data stream (default)
- Kinesis Consumers - AWS Lambda
    - Supports Classic & Enhanced fan-out consumers
    - Read records in batches
    - Can configure batch size and batch window
    - If error occurs, Lambda retries until succeeds or data expired
    - Can process up to 10 batches per shard simultaneously
    - Example
        - Lambda functions -> GetBatch() API -> process records & save to DynamoDB

### Kinesis Client Library (KCL)

- A Java library that helps read record from a Kinesis Data Stream with distributed applications sharing the read
  workload
- Each shard is to be read by only one KCL instance
    - 4 shards = max 4 KCL instances
    - 6 shards = max 6 KCL instances
- Progress is checkpointed into DynamoDB (needs IAM access)
- Track other workers and share the work amongst shards using DynamoDB
- KCL can run on EC2, Elastic Beanstalk, and on-premises
- Records are read in order at the shard level
- Versions:
    - KCL 1.x (supports shared consumer)
    - KCL 2.x (supports shared & enhanced fan-out consumer)

### Kinesis Operation - Shard Splitting

- Used to increase the Stream capacity (1MB/s data in per shard)
- Used to divide a "hot shard"
- The old shard is closed and will be deleted once the data is expired
- No automatic scaling (manually increase/decrease capacity)
- Can't split into more than two shards in a single operation

### Kinesis Operation - Merging Shards

- Decrease the Stream capacity and save costs
- Can be used to group two shards with low traffic (cold shards)
- Old shards are closed and will be deleted once the data is expired
- Can't merge more than two shards in a single operation

### Kinesis Data Firehose

- Fully Managed Service, no administration, automatic scaling, servers, can batch writes:
    - AWS: Redshift/Amazon S3/OpenSearch
        - Redshift is copied from S3
    - 3rd party partner: Splunk/MongoDB/DataDog/NewRelic/...
    - Custom: send to any HTTP endpoint
- Pay for data going through Firehose
- Near Real Time
    - 60 seconds latency minimum for non full batches
    - or minimum 1MB of data at a time
- Supports many data formats, conversions, transformations, compression
- Supports custom data transformations using AWS Lambda
- Can send failed or all data to a backup S3 bucket

### kinesis Data Streams vs Firehose

- Kinesis Data Streams
    - Streaming service for ingesting at scale
    - Write custom code (producer/consumer)
    - Real-time (~200ms)
    - Managing scaling (shard splitting/merging)
    - Data storage for 1 to 365 days
    - Supports replay capability
- Kinesis Data Firehose
    - Load streaming data into S3/Redshift/OpenSearch/3rd party/custom HTTP
    - Fully managed
    - Near real-time (buffer time min .60 sec)
    - Automatic scaling
    - No data storage
    - Doesn't support replay capability

### Kinesis Data Analytics

- Kinesis Data Analytics for SQL applications
    - Real-time analytics on Kinesis Data Stream & Firehose using SQL
    - Add reference data from Amazon S3 to enrich streaming data
    - Fully managed, no servers to provision
    - Automatic scaling
    - Pay for actual consumption rate
    - Output:
        - Kinesis Data Streams: create streams out of the real-time analytics queries
        - Kinesis Data Firehose: send analytics query result to destinations
    - Use cases:
        - Time-series analytics
        - Real-time dashboards
        - Real-time metrics
- Kinesis Data Analytics for Apache Flink
    - Use Flink (Java, Scala or SQL) to process and analyze streaming data
    - Read from Kinesis Data Streams, Amazon MSK
    - Run any Apache Flink application on a managed cluster on AWS
        - provisioning compute resources, parallel computation, automatic scaling
        - application backups (implemented as checkpoints and snapshots)
        - Use an Apache Flink programming features
        - Flink does not read from Firehose (use Kinesis Analytics for SQL instead)

### Data Ordering for Kinesis vs SQS FIFO

- Ordering data into Kinesis
    - 100 trucks on the road sending their GPS positions regularly into AWS
    - Want to consume the data in order for each truck
    - Using "Partition Key" value of the "truck_id"
    - The same key will always go to the same shard
- Ordering data into SQS
    - For SQS standard, there is no ordering
    - For SQS FIFO, if you don't use a Group ID, messages are consumed in the order they are sent, with only one
      consumer
    - Want to scale the number of consumers, but want messages to be "grouped" when they are related to each other
    - Then you use a Group ID (similar to Partition Key in Kinesis)
- Kinesis vs SQS ordering
    - Assume 100 trucks, 5 kinesis shards, 1 SQS FIFO
        - Kinesis
            - On average you'll have 20 trucks per shard
            - Trucks will have their data ordered within each shard
            - The maximum amount of consumers in parallel we can have is 5
            - Can receive up to 5 MB/s of data
        - SQS FIFO
            - You only have one SQS FIFO queue
            - You will have 100 Group ID
            - You can have up to 100 consumers (due to the 100 Group ID)
            - You have up to 300 messages per second (or 3000 if using batching)

### SQS vs SNS vs Kinesis

- SQS
    - Consumer pull data
    - Data is deleted after being consumed
    - Can have as many workers (consumers) as we want
    - No need to provision throughput
    - Ordering guarantees only on FIFO queues
    - Individual message delay capability
- SNS
    - Push data to many subscribers
    - up to 12500000 subscribers
    - Data is not persisted (lost if not delivered)
    - Pub/Sub
    - Up to 100000 topics
    - No need to provision throughput
    - Integrates with SQS for fan-out architecture pattern
    - FIFO capability for SQS FIFO
- Kinesis
    - Standard: pull data
        - 2MB per shard
    - Enhanced-fan out: push data
        - 2MB per shard per consumer
    - Possibility to replay data
    - Meant for real-time big data, analytics and ETL
    - Ordering at the shard level
    - Data expires after X days
    - Provisioned mode or on-demand capacity mode

### Amazon MQ

- SQS, SNS are cloud-native services: proprietary protocols from AWS
- Traditional applications running from on-premises may use open protocol such as: MQTT, AMQP, STOMP, Openwire, WSS
- When migrating to the cloud, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- Amazon MQ is a managed message broker service for RabitMQ and ActiveMQ
- Amazon MQ doesn't scale as much as SQS/SNS
- Amazon MQ runs on servers, can run in Multi-AZ with failover
- Amazon MQ has both queue feature and topic feature
- High availability
    - Deploy in multi-AZ
    - Has back-up storage mounted in EFS
