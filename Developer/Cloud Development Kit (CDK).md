## AWS Cloud Development Kit (CDK)

### CDK Overview

- Define your cloud infrastructure using a familiar language
    - JavaScript/TypeScript, Python, Java and .NET
- Contains high level components called c constructs
- The code is "compiled" into a CLoudFormation template (JSON/YAML)
- You can therefore deploy infrastructure and application runtime code together
    - Great for Lambda functions
    - Great for Docker containers in ECS/EKS
- CDK vs SAM
    - SAM:
        - Serverless focused
        - Write your template decoratively in JSON or YAML
        - Great for quickly getting started with Lambda
        - Leverages CloudFormation
    - CDK:
        - All AWS services
        - Write infra in a programming language JavaScript/TypeScript, Python, Java and .NET
        - Leverages CloudFormation
- CDK + SAM
    - You can use SAM CLI to locally test your CDK apps
    - You must first run cdk synth
    - `sam local invoke -t MyCDKStack.template.json myFunction`

### CDK - Constructs

- CDK Constructs is a component that encapsulates everything CDK needs to create the final CloudFormation stack
- Can represent a single AWS resource (e.g, S3 bucket) or multiple related resources (e.g, worker queue with compute)
- AWS Construct Library
    - A collection of Constructs included in aWS CDK which contains Constructs for every AWS resource
    - Contains 3 different levels of Constructs available (L1,L2,L3)
- Construct Hub - contains additional Constructs from AWS, 3rd parties, and open-source CDK community
- Layer 1 Constructs (L1)
    - Can be called CFN Resources which represents all resources directly available in CloudFormation
    - Constructs are periodically generated from CloudFormation Resource Specification
    - Construct names start with Cfn (e.g, CfnBucket)
    - You must explicitly configure all resource properties
- Layer 2 Constructs (L2)
    - Represents AWS resources but with a higher level (intent-based API)
    - Similar functionality as L1 but with convenient defaults and boilerplate
        - You don't need to know all the details about the resource properties
    - Provide methods that make it simpler to work with the resource (e.g, bucket.addLifeCycleRule())
- Layer 3 Constructs (L3)
    - Can be called Patterns, which represents multiple related resources
    - Helps you complete common tasks in AWS
    - Examples:
        - `aws-apigateway.LambdaRestApi` represents an API Gateway backed by a Lambda function
        - `aws-ecs-patterns.ApplicationLoadBalancerFargateServer` which represents an architecture that includes a
          Fargate cluster with Application Load Balancer

## CDK - Important Commands to know
- `npm install -g aws-cdk-lib`: install the CDK CLI and libraries
- `cdk init app`: Create a new CDK project from a specified template
- `cdk synth`: Synthesizes and prints the CloudFormation template
- `cdk bootstrap`: Deploy the CDK Toolkit staging Stack
- `cdk deploy`: Deploy the Stack(s)
- `cdk diff`: View differences of local CDK and deployed Stack
- `cdk destroy`: Destroy the Stack(s)

### CDK -Bootstrapping
- The process of provisioning resources for CDK before you can deploy CDK appls into an AWS environment
- AWS Environment = account & region
- CloudFormation Stack called CDKToolkit is created and contains
  - S3 Bucket - to store files
  - IAM Roles - to grant permissions to perform deployments
- You must run the following command for each new environment:
  - `cdk bootstrap aws://<aws_account>/<aws_region>`
- Otherwise, you will get an error "Policy contains a statement with one or more invalid principal"

### CDK -Unit Testing
- To test CDK apps, use CDK Assertions Module combined with popular test frameworks such as Jest(JavaScript) or Pytest (Python)
- Verify we have specific resources, rules, conditions, parameters...
- Two types of tests:
  - Fine-grained Assertions (common) - test specific aspects of the CloudFormation template (e.g, check if a resource has this property with this value)
  - Snapshot Tests - test the synthesized CloudFormation template against a previously stored baseline template
- To imporat a tempalte
  - Template.fromStack (MyStack): stacks built in CDK
  - Template.fromString (mystring): stack build outside CDK