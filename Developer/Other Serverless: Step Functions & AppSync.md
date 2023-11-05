## AWS Step Functions

### Step Functions Overview

- Build serverless visual workflow to orchestrate your Lambda functions
- Features: sequence, parallel, conditions, timeouts, error handling...
- Can integrate with EC2, ECS, On-premises servers, API Gateway, SQS queue, etc...
- Possibility of implementing human approval feature
- Model your workflows as state machines (one per workflow)
    - Order fulfillment, Data processing
    - Web applications, Any workflow
- Written in JSON
- Visualization of the workflow, and the execution of the workflows, as well as history
- Start workflow with SDK call, API Gateway, Event Bridge (CloudWatch Event)

### Step Function - Task States

- Do some work in your state machine
- Invoke one AWS service
    - Can invoke a Lambda function
    - Run an AWS Batch job
    - Run an ECS task and wait for it to complete
    - Insert an item from DynamoDB
    - Publish message to SNS, SQS
    - Launch another Step Function workflow
- Run an one Activity
    - EC2, Amazon ECS, on-premises
    - Activities poll the Step functions for work
    - Activities send results back to Step Functions

### Step Function - States

- Choice State - Test for a condition to send to a branch (or default branch)
- Fail or Succeed State - Stop execution with failure or success
- Pass State - Simply pass it input to its output or inject some fixed data, without performing work
- Wait State - Provide a delay for a certain amount of time or until a specified time/date
- Map State - Dynamically iterate steps
- Parallel State - Begin parallel branches of execution

### Step Functions - Error Handling

- Any state can encounter runtime errors for various reasons:
    - State machine definition issues (for example, no matching rule in a Choice state)
    - Task failures (for example, an exception in a Lambda function)
    - Transient issues (for example, network partition event)
- Use Retry (to retry failed state) and Catch (transition to failure path) in the State Machine to handle the errors
  instead of inside the Application Code
- Predefined error codes:
    - States.ALL: matches any error name
    - States.Timeout: Task ran longer than TimeoutSeconds or no heartbeat received
    - States.TaskFailed: execution failure
    - States.Permissions: insufficient privileges to execute code
- The state may report its own errors

### Step Functions - Retry (Task or Parallel State)

- Evaluated from top to bottom
- `ErrorEquals`: match a specific kine of error
- `IntervalSeconds`: initial delay after each retry
- `BackoffRate`: multiple the delay after each retry
- `MaxAttempts`: default to 3, set to 0 for never retried
- When max attempts are reached, the Catch kicks in

### Step Functions - Catch (Task or Parallel State)

- Evaluated from top to bottom
- `ErrorEquals`: match a specific kind of error
- `Next`: State to send to
- `ResultPath`
    - A path that determines what input is sent to the state specified in the Next field
    - Include the error in the input

### Step Functions - Wait for Task Token

- Allows you to pause Step Functions during a Task until a Task Token is returned
- Task might wait for other AWS services, human approval, 3rd party integration, call legacy systems
- Append.`waitForTaskToken` to the Resource field to tell Step Functions to wait for the Task Token to be returned
- Task will pause until it receives that Task Token back with a `SendTaskSuccess` or `SendTaskFailure` API call

### Step Functions - Activity Tasks

- Enables you to have the Task work performed by an Activity Worker
- Activity Worker apps can be running on EC2, Lambda, mobile device...
- Activity Worker poll for a Task using `GetActivityTask` API
- After Activity Worker completes its work, it sends a response of its success/failure using `SendTaskSuccess`
  or `SendTaskFailure`
- To keep the Task active:
    - Configure how long a task can wait by setting TimeoutSeconds
    - Periodically send a heartbeat from your Activity Worker using `SendTaskHearBeat` within the time you set
      in `HeartBeatSeconds`
- By configuring a long `TimeoutSeconds` and actively sending a hearbeat, Activity Task can wait up to 1 year

### Step Functions - Standard vs. Express

- Standard (default)
    - Max. Duration: Up to 1 year
    - Execution Model: Exactly-once Execution
    - Execution Rate: Over 2000/second
    - Execution History: Up to 90 days or using CloudWatch
    - Pricing: # of State Transitions
    - Use cases: Non-idempotent actions (e.g, processing Payments)
- Express
    - Max. Duration: Up to 5 minutes
    - Execution Rate: Over 1000,000 / second
    - Execution History: CloudWatch Logs
    - Pricing: # of execution, duration, and memory consumption
    - Use cases: IoT data ingestion, streaming data, mobile app backends,...
    - Asynchronous
        - At-least once execution
        - Doesn't wait for Workflow to complete (get results from CW Logs)
        - You don't need an immeidiate response (e.g, messaging services)
        - Must manage idempotence
    - Synchronous
        - Wait for Workflow to complete
        - You need an immediate response (e.g, orchestrate microservices)
        - Can be invoked from API Gateway or Lambda function

## AWS AppSync

### AWS AppSync - Overview

- AppSync is a managed service that uses GraphQL
- GraphQL makes it easy for applications to get exactly the date they need
- This includes combining data from one or more resources
    - NoSQL data stores, Relational databases, HTTP APIs...
    - Integrates with DynamoDB, Aurora, OpenSearch & others
    - Custom sources with AWS Lambda
- Retrieve data in real-time with WebSocket or MQTT on WebSocket
- For mobile apps: local data access & data synchronization
- It all starts with uploading one GraphQL schema

### AppSync - Security

- There are four ways you can authorize applications to interact with your AWS AppSync GraphQL API
    - API_KEY
    - AWS_IAM: IAM users / roles / cross-account access
    - OPENID_CONNECT: OpenID Connect Provider / JSON Web Token
    - AMAZON_COGNITO_USER_POOLS
- For custom domain & HTTPS, use CloudFront in front of AppSync\

## AWS Amplify

### AWS Amplify - Create mobile and web applications

- Amplify Studio - Visually build a full-stack app, both front-end UI and a backend
- Amplify CLI - Configure an Amplify backend With a guided CLI workflow
- Amplify Libraries - Connect your app to existing AWS Services (Cognito, S3 and more)
- Amplify Hosting - Host secure, reliable, fast web apps or websites via the AWS content delivery network

### AWS Amplify - Overview

- Set of tools to get started with creating mobile and web applications
- "Elastic Beanstalk for mobile and web applications"
- Must-have features such as data storage, authentication, storage, and machine-learning, all powered by AWS services
- Front-end libraries, with ready-to-use components for React.js, Vue, Javascript, iOS, Android, Flutter, etc...
- Incorporates AWS best practices to for reliability, security, scalability
- Build and deploy with the Amplify CLI or Amplify Studio
- A set of tools and services that helps you develop and deploy scalable full stack web and mobile applications
- Authentication, Storage, API (REST, GraphQL), CI/CD, PubSub, Analytics, AI/ML Predictions, Monitoring...
- Connect your source code from GitHub, AWS CodeCommit, Bitbucket, GitLab, or upload directly
- Configure backend using Amplify CLI -> Connect frontend to backend using Amplify Frontend Libraries -> build using
  Amplify Console & deploy

### AWS Amplify - Important Features

- AUTHENTICATION
    - `amplify add auth`
    - Leverages Amazon Cognito
    - User registration, authentication, account recovery & other operations
    - Support MFA, Social Sign-in, etc...
    - Pre-build UI components
    - Fine-grained authorization
- DATASTORE
    - `amplify add api`
    - Leverages Amazon AppSync and Amazon DynamoDB
    - Work with local data and have automatic synchronization to the cloud without complex code
    - Powered by GraphQL
    - Offline and real-time capabilities

### AWS Amplify - Hosting

- `amplify add hosting`
- Build and Host Modern Web Apps
- CICD (build, test, deploy)
- Pull Request Previews
- Custom Domains
- Monitoring
- Redirect and Custom Headers
- Password protection

### AWS Amplify - End-to-End (E2E) Testing

- Run E2E test in the test phase in Amplify
- Catch regression befroe pushing code to production
- Use the test step to run any test commands at build time (amplify.yml)
- Integrated with Cypress testing framework
    - Allows you to generate UI report for your tests
- Build (run tests while the app is being build) -> Test(E2E) (run test while the app is deployed (staging)) -> Deploy