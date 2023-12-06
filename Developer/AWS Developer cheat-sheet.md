- HTTPS is available between clients and CloudFront as well as between CloudFront and backend
- Conditions cannot be used within the Parameters section
- To grant internet access to your Lambda function, its assoicated VPC must have a NAT gateway (or NAT instance) in a
  public subnet
- Some policies replace all instance during the deployment or update. This causes all accumulated Amazon EC2 burst
  balances to be lost. It happens in the following cases:
    - Managed platform updates with instance replacement enabled
    - Immutable updates
    - Deployments with immutable updates or traffic splitting enabled
- A Classic Load Balancer with HTTP or HTTPS listeners might route more traffic to higger-capacity instance types
- With Application Load Balancer, cross-zone load balancing is always enabled
- EBS volumes support both in-flight encryption and encryption at rest using KMS
- AWS requires approximately 5 weeks of usage data to generate budget forecasts
- AWS IAM Access Analyzer helps you identify the resources in your organization and accounts, such as Amazon S3 buckets
  of IAM roles, that are shared with an external entity
- `!GetAtt` intrinsic function returns the value of an attribute from a resource in the template
- With the DeletionPolicy attribute you can preserve, and in some cases, backup a resource when its stack is deleted
- Under the "Test" tab there's an option. (Shareable)
  This event is available to IAM users within the same account who have permissions to access and use shareable events.
- CloudWatch Evidently, Overrides let you pre-define the variation for selected users. to always receive the editable
  variation.
- The edge Lambda function must be in the US East (N. Virginia) Region.
- In Integration Request, add an X-Amz-Invocation-Type header with a static value of 'Event' to make Lambda async
- `ResultPath`A path that determines what input is sent to the state specified in the Next field, Include the error in
  the input
- AppSpec 'resources' section for Amazon ECS deployments
    - TaskDefinition – Required
    - ContainerName – Required
    - ContainerPort – Required
- 您可以使用 AWS AppSync 构建需要实时更新一系列数据源（包括 Amazon DynamoDB）的可扩展应用程序
- You can use the CloudTrail Console to view the last 90 days of recorded API activity. For events older than 90 days,
  use Athena to analyze CloudTrail logs stored in the S3 bucket.
- Code Deploy support one-at-time, half-at-time, all-at-once, custom
- DynamoDB Streams can be sent to KCL, Lambda and Kinesis Data Stream
- Lambda async invocation has S3, SNS, CloudWatch Events...
- CodePipeline integrate with cloudwatch event
- Conditions cannot be used within the Parameters section.
- IAM roles and resource-based policies delegate access across accounts only within a single partition.
- HTTPS can be established between clients and cloudfront as well as between cloudfront and backend
- You can export log data from your CloudWatch log groups to an Amazon S3 bucket and use this data in custom processing
  and analysis, or to load onto other systems.
- CloudFormation parameter can not have a condition
- ELB provides access logs that capture detailed information about requests sent to your load balancer. Each log
  contains information such s the time the request was received, the client's IP address, latencies, request paths and
  server responses
- Amazon API Gateway authorizer is an API Gateway feature that uses a Lambda function to control access to your API,
  uses a bearer token authentication strategy such as OAuth or SAML
    - Token-based Lambda authorizer
    - Request parameter-based Lambda authorizer
- Use backlog per instance with target tracking scaling policy can adjust to the demand curve of your application more
  effectively
- You need to activate IAM user access to the Billing and Cost Management console for all the users who need access
- IAM Access Analyzer helps you identify the resources in your organization and accounts, such as Amazon S3 bucket or
  IAM roles, that are shared with an external entity
- Some policies replace all instances during the deployment or update, this causes all accumulated Amazon EC2 burst
  balances to be lost. It happens in the following cases:
    - Managed platform updates with instance replacement enabled
    - Immutable updates
    - Deployments with immutable updates or traffic splitting enabled
- AWS requires approximately 5 weeks of usage data to generate budget forecasts.
- SAM supports the following resource types:
    - AWS::Serverless:::Api
    - Application
    - Function
    - HttpApi
    - LayerVersion
    - SimpleTable
    - StateMachine
- The visibility timeout for the queue is in seconds, Valid values are: an integer from 0 to 43200 (12 hours), the
  default value is 30
- After successful authentication, Amazon Cognito returns user pool tokens to your app, you can use the tokens to grant
  your user access to your own server-side resources, or to the Amazon API Gateway
- AWS Organization SCP and Permission boundary only limit permissions but cannot grant permissions
- Burstable performance instances which are T3, T3a and T2 instances are designed to proivde a baseline level of CPU
  performance with the ability to burst to a higher level when required by your workload
- IAM is used as a certificate manager only when you must support HTTPS connections in a Regions that is not supported
  by ACM
- To deploy a container image to Lambda, the container image must implement the Lambda Runtime API
- The dry-run option in AWS CLI checks whether you have the required permissions for the action
- AWS CodeBuild monitors functions on your behalf and reports metrics through Amazon CloudWatch.
- Lambda does not support functions that use multi-architecture container images
- CloudFormation currently supports the following parameter types:
    - String
    - Number
    - List<Number>
    - CommaDelimitedList
    - EC2 Key Pair
    - Security group Id(s)
    - Subnet Id(s)
    - VPC Id(s)
- AWS STS authentication is not supoorted by API Gateway
- Configure Application Auto Scaling to manage Lambda provisioned concurrency on a schedule can mitigate latency
- The maximum ratio of provisioned IOPS to requested volume size is 50:1
- UpdateItem edits an existing item's attributes or adds a new item to the table if it does not already exist
- Zone reserved instances provide capacity reservations as well as discounts
- A regional instance does not provide a capacity reservation
- With high-resolution, CloudWatch stores it with a resolution of 1 second, and you can read and retrieve it with a
  period of 1, 5, 10, 30, or any multiple of 60 seconds
- Maximum delay for SQS is 15 minutes
- Maximum visibility timeout is 12 hours, default is 30 seconds
- You can noticeably reduce your build time by caching dependencies and will allow the code bundle to deployed to
  Beanstalk to have both the dependencies and codes
- YOu cannot use a resource-based policy with a parameter in the Parameter Store
- S3 Object Ownership is an Amazon S3 bucket setting that you can use to control ownership of new objects that are
  uploaded to your buckets
    - Object writer
    - Bucket owner preferred - if the object is uploaded with the `bucket-owner-full-control` canned ACL
- Approval actions can't be added to Source stages. Source stages can contain only source actions
- Controlling access to Amazon S3
    - IAM policies
    - bucket policies
    - ACLs
    - Query string authentication - pre-signed URL
- You can create an identity-based policy that allows Amazon Cognito users to access objects in a specific S3 bucket
  by identity variable
- For most standard queues, there can be a maximum of approximately 120000 inflight messages
- ElastiCache can improve performance of compute-intensive and read-heavy application workloads
- aws ec2 monitor-instances --instance-ids i-1234567890abcdef0 - This enables detailed monitoring for a running
  instance.
- If the bucket owner is also the object owner, the bucket owner gets the object access logs. Ohterwise, the bucket
  owner must get permissions, through the object ACl, for the same object API to get the same object-access API logs
- `AWS STS decode-authorization-message` can decode additional information about the authorization status of a request
- The client must send a request that contains the `Cahce-Control: max-age=0` header to invalidate an existing cache
  entry and reload if from the integraiton endpoint for individual requests
- Wehn using Redis with cluster mode enabled, there are some limitations:
    - You cannot manually promote any of the replica nodes to primary
    - Multi-AZ is required
    - You can not change the structure of a cluster, the node type, and the number of nodes by restoring from a backup
- Data in AWS CodeCommit repositories is encrypted in transit and at rest
- If you specify targets using an instance ID, traffic is routed to instances using the primary IP address speicified in
  the primary netowrk inteface for the instance
- If you speicfy target using IP addresses, you can route traffcis to an instance using any private UP address from one
  or more network interfacts, this enables multiple applications on an instance to use the same port
- List of ephemeral port ranges:
    - Many Linux kernels (including the Amazon Linux kernel) use ports 32768 - 61000
    - ELB using 1024 - 65535
    - Windows server 2003 using 1025 - 5000
    - Windows server 2008 and later using 49125 - 65535
    - NAT gateway us 1024 - 65535
    - AWS Lambda use 1024 -65535
- The intrinsic function `Fn::ImportValue` returns the value of an output exported by another stack
- Amazon S3 inventory is one of the tools Amazon S3 provideds to help manage your storage. You can use it to audit and
  report on the replication and encryption status of your objects for business, compliance and regulatory needs
- `AWS_XRAY_DAEMON_ADDRESS` set the host and port of the X-Ray daemon listener, by default, the SDK uses 127.0.0.1:2000
  for both trace data (UDP) and sampling (TCP)
- CloudFront routes all incoming requests to the primary origin, even when a previous request failed over to the
  secondary origin
- CloudFront fails over to the secondary origin only when the HTTP method of the viewer request is GET,HEAD or OPTIONS
- AWS STS is used by API Gateway for looging data to cloudwatch logs
- In access logging, only $context is supported
- You are charged for accessing method-level CloudWatch metrics
- `aws ecr get-login` command retrieves a token that is valid for a specified registry for 12 hours, and then it prints a dokcer login command with that authorization token
- Bucket configurations have an eventual consistency model
- Amazon S3 provides strong read-after-write consistency for PUTs and DELETEs of objects in your Amazon S3 buckert in all AWS regiosn
- Using the awslogs driver you can configure the containers in your tasks to send log infomration to CloudWatch Logs, if you are using the Fargate launch type for your tasks, you need to add the required logConfiguration parameters to your taksk definition to turn on the awslogs log friver
- Amazon S3 event notifications are designed to be delivered at least once. Typically, event notifications are delivered in seconds but can sometimes take a minute or longer.
- If two writes are made to a single non-versioned object at the same time, it is possible that only a single event notification will be sent.
- EBS encryption by default is a Region-specific setting
- AWS CloudTrail event logs for 'CreateVolume' aren't available for EBS volumes created during an Amazon EC2 launch
- AppSync can use real time update data
- Resource policy + sigV4 can combine for cross-account API Gateway access
- The TLS Certificate must be in the same region as CloudFront, in us-east-1 for API Gateway
- CloudFormation
  - Set the DeletionPolicy attribute to prevent the deletion of an individual resource at the stack level.
  - Assign a stack policy to prevent updates to stack resources.
- rely on the context object to get access to execution ID 
- Lambda test sharable event is available to IAM users within the same account who have permissions to access and use shareable events.
- Filters do not retroactively filter data. Filters only publish the metric data points for events that happen after the filter was created.

