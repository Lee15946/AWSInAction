### What's serverless?

- Serverless is a new paradigm in which the developers don't have to manage servers anymore...
- They just deploy code
- They just deploy... functions
- Initially... Serverless == FaaS (Function as a Service)
- Serverless was pioneered by AWS Lambda but now also includes anything that's managed: "databases, messaging, storage,
  etc."
- Serverless does not meant there are no servers...
- It means you just don't manage / provision / see them

### Serverless in AWS

- AWS Lambda
- DynamoDB
- AWS Cognito
- AWS API Gateway
- Amazon S3
- AWS SNS & SQS
- AWS Kinesis Data Firehose
- Aurora Serverless
- Step Functions
- Fargate

## AWS Serverless

### What's serverless?

- Serverless is a new paradigm in which the developers don't have to manage servers anymore
- They just deploy code or functions
- Initially Serverless == Faas (Function as a Service)
- Serverless was pioneered by AWS Lambda but now also includes anything that's managed: databases, messaging, storage,
  etc.
- Serverless does not mean there are no servers...It means you don't just manage/provision/see them
- Serverless in AWS
    - AWS Lambda
    - DynamoDB
    - AWS Cognito
    - AWS API Gateway
    - Amazon S3
    - AWS SNS&SQS
    - AWS Kinesis Data Firehose
    - Aurora Serverless
    - Step Functions
    - Fargate

### AWS Lambda

- Why AWS Lambda
    - Amazon EC2
        - Virtual Servers in the cloud
        - Limited by RAM and CPU
        - Continuously running
        - Scaling means intervention to add/remove servers
    - Amazon Lambda
        - Virtual functions - no servers to manage
        - Limited by time - short executions
        - Run on-demand
        - Scaling is automated
- Benefits of AWS Lambda
    - Easy pricing:
        - Pay per request and compute time
        - Free tier of 1 million AWS Lambda requests and 400 thousand GBs of compute time
    - Integrated with the whole AWS suite of services
    - Integrated with many programming languages
    - Easy monitoring thorough AWS CloudWatch
    - Easy to get more resources per functions (up to 10GB of RAM)
    - Increasing RAM will also improve CPU and network
- AWS Lambda language support
    - Node.js
    - Python
    - Java
    - C#
    - Golang
    - Powershell
    - Ruby
    - Custom Runtime API (community supported, example Rust)
    - Lambda Container Image
        - The container image mus implement the Lambda Runtime API
        - ECS/ Fargate is preferred for running arbitrary Docker images
- AWS Lambda Integrations
    - API Gateway
    - Kinesis
    - DynamoDB
    - S3
    - CloudFront
    - CloudWatch Events EventBridge
    - CloudWatch Logs
    - SNS
    - SQS
    - Cognito
- Example
    - Serverless Thumbnail creation
    - Serverless CRON Job
- Pricing
    - Pay per calls
    - Pay per duration (in increment of 1 ms)
    - It's usually very cheap to run AWS Lambda, so it's very popular

### Lambda Synchronous Invocations

- Synchronous: CLI, SDK, API Gateway, Application Load Balancer
    - Results are returned right away
    - Error handling must happen client side (retries, exponential backoff, etc...)
- Services
    - User Invoked
        - Elastic Load Balancer (Application Load Balancer)
        - Amazon API Gateway
        - Amazon CloudFront (Lambda@Edge)
        - Amazon S3 Batch
    - Service Invoked
        - Amazon Cognito
        - AWS Step Functions
    - Other Services:
        - Amazon Lex
        - Amazon Alexa
        - Amazon Kinesis Data Firehose

### AWS Lambda & Application Load Balancer

- To expose a Lambda function as an HTTP(S) endpoint
- You can use the Application Load Balancer (or an API gateway)
- The Lambda function must be registered in a target group
- ALB to Lambda: HTTP tp JSON
    - ELB information
    - HTTP Method & Path
    - Query String Parameters as Key/Value pairs
    - Headers as Key/Value pairs
    - Body (for POST,PUT..) & isBase64Encoded
- Lambda to ALB conversions: JSON to HTTP
    - Status Code & Description
    - Headers as Key/Value pairs
    - Body & isBase63Encoded
- ALB Multi-Header Values
    - ALB can support multi header values (ALB setting)
    - When you enable multi-value headers, HTTP headers and query string parameters that are sent with mulitple values
      are shown as arrays within the AWS Lambda event and response objects

### Lambda- Asynchronous Invocations

- S3, SNS, CloudWatch Events...
- The events are placed in an Event Queue
- Lambda attempts to retry on errors
    - 3 tries total
    - 1 minute wait after 1st, then 2 minutes wait
- Make sure the processing is idempotent (in case of retries)
- If the function is retried, you will see duplicate logs entries in CloudWatch Logs
- Can define a DLQ (dead-letter queue) - SNS or SQS - for failed processing (need correct IAM permissions)
- Asynchronous invocations allow you to speed up the processing if you don't need to wait for the result (ex: you need
  1000 files processed)
- Lambda - Asynchronous Invocations - Services
    - Amazon S3
    - Amazon SNS
    - Amazon CloudWatch Events / EventBridge
    - AWS CodeCommit (CodeCommit Trigger: new branch, new tag, new push)
    - AWS CodePipeline (invoke a Lambda function during the pipeline, Lambda must callback)
    - ---- other ----
    - Amazon CloudWatch Logs (log processing)
    - Amazon Simple Email Service
    - AWS CloudFormation
    - AWS Config
    - AWS IoT
    - AWS IoT Events

### Lambda & CloudWatch Events / EventBridge

- CRON or Rate EventBridge Rule -> Trigger every 1 hour -> AWS Lambda Function Perform a task
- CodePipeline EventBridge Rule -> Trigger on State Changes -> AWS Lambda Function Perform a task

### Lambda & S3 Event Notifications

- S3:ObjectCreated, S3:ObjectRemoved, S3:ObjectRestore, S3:Replication...
- Object name filtering possible (*.jpg)
- Use case: generate thumbnails of images uploaded to S3
- S3 event notifications typically deliver events in seconds but can sometimes take a minute or longer
- If two writes are made to a single non-versioned object at the same time, it is possible that only a sinlge event
  notification will be sent
- If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your
  bucket
- Simple S3 Event Pattern - Metadata Sync
    - S3 bucket -> New file event -> Update metadata table -> Table in RDS / DynamoDB Table

### Lambda Event Source Mapping

- Kinesis Data Streams
- SQS & SQS FIFO queue
- DynamoDB Streams
- Common denominator: records need to be polled from the source
- You Lambda function is invoked synchronously
- Streams & Lambda (Kinesis & DynamoDB)
    - An event source mapping creates an iterator for each shard, process items in order
    - Start with new items, from the beginning or from timestamp
    - Processed items aren't removed from the stream (other consumers can read them)
    - Low traffic: use batch window to accumulate records before processing
    - You can process multiple batches in parallel
        - up to 10 batches per shard
        - in-order processing is still guaranteed for each partition key
- Streams & Lambda - Error Handling
    - By default, if your function returns an error, the entire batch is reprocessed until the function succeeds, or the
      items in the batch expire
    - To ensure in-order processing, processing for the affected shard is paused until the error is resolved
    - You can configure the event source mapping to:
        - discard old events
        - restrict the number of retries
        - split the batch on error (to work around Lambda timeout issues)
    - Discarded events can go to a Destination
- Lambda - Event Source Mapping: SQS & SQS FIFO
    - Event Source Mapping will poll SQS (Long Polling)
    - Specify batch size (1-10 messages)
    - Recommended: Set the queue visibility timeout to 6x the timeout of your Lambda function
    - To use a DLQ:
        - set-up on the SQS queue, not Lambda (DLQ for Lambda is only for async invocations)
        - Or use a Lambda destination for failures
- Queues & Lambda
    - Lambda also supports in-order processing for FIFO (first-in, first-out) queues, scaling up to the number of active
      message groups
    - For standard queues, items aren't necessarily processed in order
    - Lambda scales up to process a standard queue as quickly as possible
    - When an error occurs, batched are returned to the queue as individual items and might be processed in a different
      grouping than the original batch
    - Occasinoally, the event source mapping might receive the same item from the queue twice, even if no function error
      occurred
    - Lambda deletes items from the queue after they're processed successfully
    - You can configure the source queue to send items to a dead-letter queue if they can't be processed
- Lambda Event Mapper Scaling
    - Kinesis Data Streams & DynamoDB Streams:
        - One Lambda invocation per stream shard
        - If you use parallelization, up to 10 batches processed per shard simultaneously
    - SQS Standard
        - Lambda adds 60 more instances per minute to scale up
        - Up to 1000 batches of messages processed simultaneously
    - SQS FIFO:
        - Messages with the same GroupID will be processed in order
        - The Lambda function scores to the number of active message groups

### Lambda Event & Context Objects

- Event Object
    - JSON-formatted document contains data for the function to process
    - Contains information from the invoking service (e.g, EventBridge, custom, ....)
    - Lambda runtime converts the event to an object (e.g, dict type in Python)
    - Example: input arguments, invoking service arguments,....
- Context Object
    - Provides methods and properties that provide information about the invocation function, and runtime environment
    - Passed to your function by Lambda at runtime
    - Example: aws_request_id, function_name, memory_limit_in_mb,...

### Lambda - Destinations

- Nov 2019: Can configure to send result to a destination
- Asynchronous invocations - can define destinations for successful and failed event:
    - Amazon SQS
    - Amazon SNS
    - AWS Lambda
    - Amazon EventBridge bus
- Note: AWS recommends you use destinations instead of DLQ now (but both can be used at the same time)
- Event Source mapping: for discarded event batches
    - Amazon SQS
    - Amazon SNS
- Note: you can send events to a DLQ directly from SQS

### Lambda Permissions - IAM Roles & resource Polices

- Lambda Execution Role (IAM Role)
    - Grants the Lambda function permissions to AWS services / resources
    - Sample manages policies for Lambda:
        - AWSLambdaBasicExecutionRole - Upload logs to CloudWatch
        - AWSLambdaKinesisExecutionRole - Read from Kinesis
        - AWSLambdaDynamoDBExecutionRole - Read from DynamoDB Streams
        - AWSLambdaSQSQueueExecutionRole - Read from SQS
        - AWSLambdaVPCAccessExecutionRole - Deploy Lambda function in VPC
        - AWSXRayDaemonWriteAccess - Upload trace data to X-Ray
    - When you use an event source mapping to invoke your function, Lambda uses the execution role to read event data
    - Best practice: create one Lambda Execution Role per function
- Lambda Resource Base Policies
    - Use resource-based policies to give other accounts and AWS services permission to use your Lambda resources
    - Similar to S3 bucket policies for S3 bucket
    - An IAM principal can access Lambda:
        - If the IAM policy attached to the principal authorizes it (e.g: user access)
        - Or if the resource-based policy authorizes (e.g. service access)
    - When an AWS service like Amazon S3 calls your Lambda function, the resource-base policy give it access

### Lambda Environment Variables

- Environment variable = key / value pair in "String" form
- Adjust the function behavior without updating code
- The environment variables are available to your code
- Lambda Service adds its own system environment variables as well
- Helpful to store secrets (encrypted by KMS)
- Secrets can be encrypted by the Lambda service key, or your own CMK

### Lambda Monitoring & X-Ray Tracing

- Lambda Logging & Monitoring
    - CloudWatch Logs:
        - AWS Lambda execution logs are stored in AWS CloudWatch Logs
        - Make sure your AWS Lambda function has an execution role with an IAM policy that authorizes writes to
          CloudWatch Logs
    - CloudWatch Metrics:
        - AWS Lambda metrics are displayed in AWS CloudWatch Metrics
        - Invocations, Durations, Concurrent Executions
        - Error count, Success Rates, Throttles
        - Async Delivery Failures
        - Iterator Age (kinesis & DynamoDB Streams)
    - Lambda Tracing with X-Ray
        - Enable in Lambda configuration (Active Tracing)
        - Runs the X-Ray daemon for you
        - Use AWS X-Ray SDK in Code
        - Ensure Lambda Function has a correct IAM Execution Role
            - The managed policy is called AWSXRayDaemonWriteAccess
        - Environment variables to communicate with X-Ray
            - _X_AMZN_TRACE_ID: contains the tracing header
            - AWS_XRAY_CONTEXT_MISSING: by default, LOG_ERROR
            - AWS_XRAY_DAEMON_ADDRESS: the X-Ray Daemon IP_ADDRESS:PORT

### Lambda@Edge & CloudFront Functions

- Customization At the Edge
    - Many modern applications execute some from of the logic at the edge
    - Edge Function:
        - A code that you write and attach to CloudFront distributions
        - Run close to your users to minimize latency
    - CloudFront provides two types: CloudFront Functions & Lambda@Edge
    - You don't have to manage any servers, deployed globally
    - Use cases: customize the CDN content
    - Pay only for what you use
    - Fully serverless
- Use cases
    - Website Security and Privacy
    - Dynamic Web Application at the Edge
    - Search Engine Optimization (SEO)
    - Intelligently Route Across Origins and Data Centers
    - Bot Mitigation at the Edge
    - Real-time Image Transformation
    - A/B Testing
    - User Authentication and Authorization
    - User Prioritization
    - User Tracking and Analytics
- CloudFront Functions
    - Lightweight functions written in JavaScript
    - For high-scale, latency-sensitive CDN customizations
    - Sub-ms startup times, millions of requests/second
    - Used to change Viewer requests and responses:
        - Viewer Requests: after CloudFront receives a request from a viewer
        - Viewer Response: before CloudFront forwards the response to the viewer
    - Native feature of CloudFront (manage code entirely within CloudFront)
- Lambda@Edge
    - Lambda functions written in NodeJS or Python
    - Scales to 1000s of requests/second
    - Used to change CloudFront requests and responses
        - Viewer Request
        - Origin Request: before CloudFront forwards the request to the origin
        - Origin Response: after CloudFront receives the response from the origin
        - Viewer Response
    - Author your functions in one AWS Region (us-east-1), then CloudFront replicates to its locations
- CloudFront Functions vs Lambda@Edge - Use cases
    - CloudFront Functions
        - Cache key normalization
            - Transform request attributes (headers, cookies, query strings, URL) to create an optimal Cache key
        - Header manipulation
            - Insert/modify/delete HTTP headers in the request or response
        - URL rewrites or redirects
        - Request authentication & authorization
            - Create and validate user-generated tokens to allow/deny requests
    - Lambda@Edge
        - Longer execution time (several ms)
        - Adjustable CPU or memory
        - Your code depends on a 3rd libraries (e.g: AWS SDK to access other AWS services)
        - Network access to use external services for processing
        - File system access or access to the body of HTTP requests

### Lambda in VPC

- Lambda by default
    - By default, your Lambda function is launched outside your own VPC (in an AWS-owned VPC)
    - Therefore, it can not access resources in your VPC (RDS, ElastiCache, internal ELB)
- Lambda in VPC
    - You must define the VPC ID, the Subnets and the Security Groups
    - Lambda will create an ENI (Elastic Network Interface) in your subnets
    - AWS LambdaVPCAccessExecutionRole
- Lambda in VPC - Internet Access
    - A Lambda function in your VPC does not have internet access
    - Deploying a Lambda function in a public subnet does not give it internet access or a public IP
    - Deploying a Lambda function in a private subnet gives it internet access if you have a NAT Gateway / Instance
    - You can use VPC endpoints to privately access AWS services without a NAT
- Lambda with RDS Proxy
    - If Lambda functions directly access your database, they may open too many connections under high load
    - RDS Proxy
        - Improve scalability by pooling and sharing DB connections
        - Improve availability by reducing by 66% the failover time and preserving connections
        - Improve security by enforcing IAM authentication and storing credentials in Secrets Manager
    - The Lambda function must be deployed in your VPC, because RDS Proxy is never publicly accessible

### Lambda Function Performance

- Lambda Function Configuration
    - RAM:
        - From 128MB to 10GB in 1 MB increments
        - The more RAM you add, the more vCPU credits you get
        - At 1792 MB, a function has the equivalent of one full vCPU
        - After 1792 MB, you get more than one CPU, and need to use multi-threading in your code to benefit from it
    - If your application is CPU-bound (computation heavy), increase RAM
    - Timeout: default 3 seconds, maximum is 900 seconds (15 minutes)
- Lambda Execution Context
    - The execution contest is a temporary runtime environment that initializes any external dependencies of your lambda
      code
    - Great for database connections, HTTP clients, SDK clients...
    - The execution context is maintained for some time in anticipation of another Lambda function invocation
    - The next function invocation can "re-use" the context to execution time and save time in initializing connections
      objects
    - The execution context includes the /tmp directory
- Initialize outside the handler
    - The DB connection is established at every function invocation if initialize inside the handler
    - The DB connection is established once and re-used across invocations if initialize outside the handler
- Lambda Functions /tmp space
    - If your Lambda function needs to download a big file to work
    - If your Lambda function needs disk space to perform operations...
    - You can use the /tmp directory
    - Max size is 10 GB
    - The directory content remains when the execution context is frozen, providing transient cache that can be used for
      multiple invocations (helpful to checkpoint your work)
    - For permanent persistence of object (non temporary), use S3
    - To encrypt content on /tmp, you must generate KMS Data Keys

### Lambda Layers

- Custom Runtimes
    - C++
    - Rust
- Externalize Dependencies to re-use them

### Lambda File Systems Mounting

- Lambda functions can access EFS file systems if they are running in a VPC
- Configure Lambda to mount EFS file systems to local directory during initialization
- Must leverage EFS Access Points
- Limitations: watch out for the EFS connection limits (one function instance = one connection) and connection burst
  limits

### Lambda Concurrency

- Lambda Concurrency and Throttling
    - Concurrency limit: up to 1000 concurrent executions
    - Can set a "reserved concurrency" at the function level (=limit)
    - Each invocation over the concurrency limit will trigger a "Throttle"
    - Throttle behavior:
        - If synchronous invocation => return ThrottleError - 429
        - If asynchronous invocation => retry automatically and then go to DLQ
    - If you need a higher limit, open a support ticket
- Concurrency and Asynchronous Invocations
    - If the function doesn't have enough concurrency available to process all events, additional requests are throttled
    - For throttling errors (429) and system errors (500-series), Lambda returns the event to the queue and attempts to
      run the function again for up to 6 hours
    - The retry interval increases exponentially from 1 second after the first attempt to a maximum of 5 minutes
- Cold Starts & Provisioned Concurrency
    - Cold Start:
        - New instance => code is loaded and code outside the handle run (init)
        - If the init is large (code, dependencies, SDK...) this process can take some time
        - First request served by new instances has higher latency than the rest
    - Provisioned Concurrency:
        - Concurrency is allocated before the function is invoked (in advance)
        - So the cold start never happens and all invocations have low latency
        - Application Auto Scaling can manage concurrency (schedule or target utilization)
    - Note:
        - Cold starts in VPC have been dramatically reduced in Oct & Nov 2019

### Lambda External Dependencies

- If your Lambda function depends on external libraries for example AWS X-Ray SDK, Database Clients, etc...
- You need to install the packages alongside your code and zip it together
    - For Node.js, use npm&"node_modules" directory
    - For Python, use pip --target options
    - For Java, include the relevant .jar files
- Upload the zip straight to Lambda if less than 50MB, else to S3 first
- Native libraries work: they need to be compiled on Amazon Linux
- AWS SDK comes by default with every Lambda function

### Lambda and CloudFormation

- Lambda and CloudFormation - inline
    - Inline functions are very simple
    - Use the Code.ZipFile property
    - You can not include with inline functions
- Lambda and CloudFormation - through S3
    - You must store the Lambda zip in S3
    - You must refer the S3 zip location in the CLoudFormation code
        - S3Bucket
        - S3Key: full path to zip
        - S3ObjectVersion: if versioned bucket
    - If you update the code in S3, but don't update S3 bucket, S3Key or S3ObjectVersion, CloudFormation won't update
      your function
- Lambda and CloudFormation - through S3 Multiple accounts
    - Account1
        - S3 bucket with Lambda code
        - Bucket policy: Allow Principal: [Accounts ID..]
    - Account2
        - CloudFormation
        - Execution Role

### Lambda Container Images

- Deploy Lambda function as control images of up to 10GB from ECR
- Pack complex dependencies, large dependencies in a container
- Base images are available for Python, Node.js, Java, .NET, Go, Ruby
- Can create your own image as long as it implements the Lambda Runtime API
- Test the containers locally using the Lambda Runtime Interface Emulator
- Unified workflow to build apps
- Example: build from the base image provided by AWS

```dockerfile
# Use an image that implements the Lambda Runtime API
FROM amazon/aws-lambda-nodejs:12

# Copy your application code and files
COPY app.js pakceage*.json ./

# Install the dependencies in the container
RUN npm install

# Function to run when the Lambda function is invoked
CMD [ "app.lambdaHandler" ]
```

- Lambda Container Images - Best Practices
    - Strategies for optimizing container images:
        - Use AWS-provided Base Images
            - Stable, Built on Amazon Linux 2, cached by Lambda service
        - Use Multi-Stage Builds
            - Build your code in larger preliminary images, copy only the artifacts you need in your final container
              image, discard the preliminary steps
        - Build from Stable to Frequesntly Changing
            - Make your most frequentrly occurring changes as late in your Dockerfile as possible
        - Use a Single Repository for Functions with Large Layers
            - ECR compares each layer of a container image when it is pushed to avoid uploading and storing duplicates
        - Use them to upload large Lambda Functions (up to 10GB)

### AWS Lambda Versions

- When you work on a Lambda function, we work on $LATEST
- When we're ready to publish a Lambda function, we create a version
- Versions are immutable
- Versions have increasing version numbers
- Versions get their own ARN (Amazon Resource Name)
- Version = code + configuration (nothing can be changed - immutable)
- Each version of the lambda function can be accessed

### AWS Lambda Aliases

- Aliases are "pointers" to Lambda function versions
- We can define a "dev", "test", "prod" aliases and have them point at different lambda versions
- Aliases are mutable
- Aliases enable Canary deployment by assigning weights to lambda functions
- Aliases enable stable configuration of our event triggers / destinations
- Aliases have their own ARNs
- Aliases cannot reference e aliases

### Lambda & CodeDeploy

- CodeDeploy can help you automate traffic shift for Lambda aliases
- Feature is integrated within the SAM framework
- LinearL grow traffic every N minutes until 100%
    - Linear10PercentEvery3minutes
    - Linear10PercentEvery10Minutes
- Canary: try X percent then 100%
    - Canary10Percent5minutes
    - Canary10Percent10minutes
- AllAtOnce: immediate
- Can create Pre & Post Traffic hooks to check the health of the Lambda function
- Lambda & CodeDeploy - AppSpec.yml
    - Name (required) - the name if the Lambda function to deploy
    - Alias (required) - the name of the alias to the Lambda function
    - CurrentVersion (required) - the version of the Lambda function traffic currently points to
    - TargetVersion (required) - the version of the Lambda function traffic is shift to

### Lambda - Function URL

- Dedicated HTTPS(S) endpoint for your Lambda function
- A unique URL endpoint is generated for you (never changes)
    - https://<url-id>.lambda-url.<region>.on.aws (dual-stack IPv4 & IPv6)
- Invoke via a web browser, curl, Postman, or any HTTP client
- Access your function URL through the public internet only
    - Does'n support PrivateLink (Lambda functions do support)
- Supports Resource-based Policies & CORS configurations
- Can be applied to any function alias or to $LATEST (can't be applied to other function versions)
- Create and configure using AWS Console or AWS API
- Throttle your function by using Reserved Concurrency

### Lambda - Function URL Security

- Resource-based Policy
    - Authorize other accounts / specific CIDR / IAM principals
- Cross-Origin Resource Sharing (CORS)
    - If you call your Lambda function URL from a different domain
- AuthType NONE - allow public and unauthenticated access
    - Resource-based Policy is always in effect (must grant public access)
- AuthType AWS_IAM - IAM is used to authenticate and authorize requests
    - Both Principal's Identity-based Policy & Resource-based Policy are evaluated
    - Principal must have `lambda:InvokeFunctionUrl` permissions
    - Same account - Identity-based Policy OR Resource-based Policy as ALLOW
    - Cross account - Identity-based Policy AND Resource Based Policy as ALLOW

### Lambda - CodeGuru Integration

- Gain insights into runtime performance of your Lambda functions using CodeGuru Profiler
- CodeGuru creates a Proiler Group for your Lambda function
- Supported for Java and Python runtimes
- Activate from AWS Lambda Console
- When activated, Lambda adds:
    - CodeGuru Profiler layer to your function
    - Environment varaibles to your function
    - AmazonCodeGuruProfilerAgentAccess Policy to your function

### AWS Lambda Limits to Know - per region

- Execution
    - Memory allocation: 128MB - 10GB (2MB increments)
    - Maximum execution time: 900 seconds (15 minutes)
    - Environment variables (4KB)
    - Disk capacity in the "function container" (in /tmp): 512MB to 10GB
    - Concurrency executions: 1000 (can be increased)
- Deployment:
    - Lambda function deployment size (compressed .zip): 50MB
    - Size of uncompressed deployment (code + dependencies): 250MB
    - Can use the /tmp directory to load other files at startup
    - Size of environment variables: 4KB

### RDS - Invoking Lambda & Event Notifications

- Invoking Lambda from RDS & Aurora
    - Invoke Lambda functions from within your DB instance
    - Allows you to process data events from within a database
    - Supported for RDS for PostgreSQL and Aurora MySQL
    - Must allow outbound traffic to your Lambda function from within your DB instance (Public, NAT GW, VPC Endpoints)
    - DB instance must have the required permissions to invoke the Lambda function (Lambda Resource-base Policy & IAM
      Policy)
- RDS Event Notifications
    - Notifications that tell information about the DB instance itself (created, stopped, start...)
    - You don't have any information about the data itself
    - Subscribe to the following event categories: DB instance, DB snapshot, DB Parameter Group, DB Security Group, RDS
      Proxy, Custom Engine Version
    - Near real-time events (up to 5 minutes)
    - Send notifications to SNS or subscribe to events using EventBridge

### AWS Serverless Solution Architecture

- MyTodoList
    - Serverless REST API: HTTPS, API Gateway, Lambda, DynamoDB
    - Using Cognito to generate temporary credentials with STS to access S3 resources this way, Pattern can be applied
      to DynamoDB, Lambda
    - Caching the reads on DynamoDB using DAX
    - Caching the REST requests at the API Gateway level
    - Security for authentication and authorization with Cognito, STS
- Serverless Website: MyBlog.com
    - Static content being distributed using CloudFront with S3
    - The REST API was serverless, didn't need Cognito because public
    - We leveraged a Global DynamoDB table to serve the data globally
    - We could use Aurora Global Database
    - We enabled DynamoDB streams to trigger a Lambda function
    - The lambda function had an IAM role which could use SES
    - SES (Simple Email Service) was used to send emails in a serverless way
    - S3 can trigger SQS/SNS/Lambda to notify of events
- MicroServices Architecture
    - Free to design eah micro-service the way you want
    - Synchronous patterns: API Gateway, Load Balancers
    - Asynchronous patterns: SQS, Kinesis, SNS, Lambda trigger (S3)
    - Challenges with micro-services:
        - Repeated overhead for creating each new microservice
        - Issues with optimizing server destiny/utilization
        - Complexity of running multiple versions of multiple microservices simultaneously
        - Proliferation of client-side code requirements to integrate with many seperate services
    - Some of the chanllenges are solved by Serverless patterns
        - API Gateway, Lambda scale automatically and you pay per usage
        - You can easily clone API, reproduce environments
        - Generated client SDK through Swagger integration for the API Gateway
- Software updates distribution
    - Why CloudFront?
        - No changes to architecture
        - Will cache software update files at the edge
        - Software update files are not dynamic, they are static (never changing)
        - Our EC2 instances aren't serverless
        - But CloudFront is, and will scale for us
        - Our ASG will not scale as much, and we will save tremendously in EC2
        - We will also save in availability, network bandwidth cost, etc...
        - Easy way to make an existing application more scalable and cheaper
