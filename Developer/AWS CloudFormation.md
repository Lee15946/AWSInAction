## AWS CloudFormation

### Infrastructure as Code

- Currently, we have been doing a lot of manual work
- All this manual work will be very tough to reproduce
    - In another region
    - In another AWS account
    - Within the same region if everything was deleted
- Wouldn't it be great, if all our infrastructure was... code?
- That code would be deployed and create/update/delete our infrastructure

### What is a CloudFormation

- CloudFormation is a declarative way of outlining your AWS Infrastructure for any resources (most of them are
  supported)
- CloudFormation creates resources in the right order, with the exact configuration that you specify

### Benefits of AWS CloudFormation

- Infrastructure as code
    - No resources are manually created, which is excellent for control
    - The code can be version controlled for example using git
    - Changes to the infrastructure are reviewed through code
- Cost
    - Each resources within the stack is tagged with an identifier so you can easily see how much a stack costs you
    - You can estimate the costs of your resources using the CloudFormation template
    - Savings strategy: In Dev, you could automation deletion of templates at 5 PM and recreated at 8 AM, safely
- Productivity
    - Ability to destroy and re-create an infrastructure on the cloud on the fly
    - Automated generation of Diagram for your templates
    - Declarative programming (no need to figure out ordering and orchestration)
- Separation of concern: create many stacks for many apps, and many layers, Ex:
    - VPC stacks
    - Network stacks
    - App stacks
- Don't re-invent the wheel
    - Leverage existing templates on the web
    - Leverage the documentation
    - Supports(almost) all AWS resources
        - Everything we'll see in this course is supported
        - You can use "custom resources" for resources that are not supported
    - CloudFormation Stack Designer
        - We can see all the resources
        - We can see the relations between the components

### How CloudFormation Works

- Templates have to be uploaded in S3 and then referenced in CloudFormation
- To update a template, we can't edit previous ones. We have to re-upload a new version of the template to AWS
- Stacks are identified by a name
- Deleting a stack deletes every single artifact that was created by CloudFormation

### Deploying CloudFormation templates

- Manual way:
    - Editing templates in the CloudFormation Designer
    - Using the console to input parameters, etc
- Automated way:
    - Editing templates in a YAML file
    - Using the AWS CLI (Command Line Interface) to deploy the templates
    - Recommended way when you fully want to automated your flow

### CloudFormation Building Blocks

- Template components (one course section for each)
    - Resources: your AWS resources declared in the template (MANDATORY)
    - Parameters: the dynamic inputs for your template
    - Mappings: the static variables for your template
    - Outputs: Reference to what has been created
    - Conditionals: List of conditions to perform resource creation
    - Metadata
- Template helpers:
    - Reference
    - Functions

### CloudFormation Resources

- Resources are the core of your CloudFormation template (MANDATORY)
- They represent the different AWS Components that will be created and configured
- Resources are declared and can reference each other
- AWS figures out creation, updates and deletes of resources for us
- There are over 224 types of resources
- Resource types identifiers are of the form:
    - AWS::aws-product-name::data-type-name
- FAQ for resources
    - Can I create a dynamic amount of resources?
        - NO, you can't. Everything in the CloudFormation template has to be declared. You can't perform code generation
          there
    - Is every AWS Service supported?
        - Almost. Only a select few niches are not there yet
        - You can work around that using AWS Lambda Custom Resources

### CloudFormation Parameters

- Parameters are a way to provide inputs to your AWS CloudFormation template
- They're important to know about if:
    - You want to reuse your templates across the company
    - Some inputs can not be determined ahead of time
- Parameters are extremely powerful, controlled, and can prevent errors from happening in your templates thanks to types
- When should you use a parameter?
    - Ask yourself this:
        - Is this CloudFormation resource configuration likely to change in the future?
        - If so, make it a parameter
    - You won't have to re-upload a template to change its content
- Parameter Settings:
    - Type
        - String
        - Number
        - CommandDelimitedList
        - List<Type>
        - AWS Parameter (to help catch invalid values - match against existing values in the AWS Account)
    - Description
    - Constraints
    - ConstraintDescription (String)
    - Min/MaxLength
    - Min/MaxValue
    - Defaults
    - AllowedValues (array)
    - AllowedPattern (regexp)
    - NoEcho (Boolean)
- How to Reference a Parameter
    - The Fn::Ref function can be leveraged to reference parameters
    - Parameters can be used anywhere in a template
    - The shorthand for this in YAML is !Ref
    - The function can also reference other elements within the template
- Concept: Pseudo Parameters
    - AWS offers us pseudo parameters in any CloudFormation template
    - These can be used at any time and are enabled by default
        - AWS::AccountId
        - AWS::NotificationARNs
        - AWS:NoValue
        - AWS::Region
        - AWS:StackId
        - AWS::StackName

### CloudFormation Mappings

- Mappings are fixed variables within your CloudFormation Template
- They're very handy to differentiate between different environments (dev vs prod), regions (AWS regions), AMI types,
  etc
- All the values are hardcoded within the template
- When would you use mappings vs parameters?
    - Mappings are great when yo know in advance all the values that can be taken and that tehy can be deduced from
      variables such as
        - Region
        - Availability Zone
        - AWS Account
        - Environment (dev vs prod)
        - Etc...
    - They allow safer control over the template
    - Use parameters when the values are really user specific
- Fn::FindInMap Accessingg Mapping Values
    - We use Fn::FindInMap to return a named value from a specific key
    - `!FindInMap[ MapName, TopLevelKey, SecondLevelKey ]`

### CloudFormation Outputs

- The Outputs section declares optional values that we can import into other stacks (if you export them first)
- You can also view the outputs in the AWS Console or in using the AWS CLI
- They're very useful, for example, if you define a network CloudFormation, and output the variables such as VPC ID and
  your Subnet IDs
- It's the best way to perform some collaboration cross stack, as you let expert hanlde their own part of the stack
- You can't delete a CloudFormation Stack if its outputs are being referenced by another CloudFormation stack
- Outputs Example

```yaml
Outputs:
  StackSSHSecurityGroup:
    Description: The SSH Security Group for our Company
    Value: !Ref MyCompanyWideSSHSecurityGroup
    Export:
      Name: SSHSecurityGroup
```

- Cross Stack Reference
    - We then create a second template that leverages that security group
    - For this, we use the Fn::ImportValue function
    - You can't delete the underlying stack until all the references are deleted too

### CloudFormation Conditions

- Conditions are used to control the creation of resources or outputs based on a condition
- Conditions can be whatever you want them to be, but common ones are:
    - Environment (dev/test/prod)
    - AWS Region
    - Any parameter value
- Each condition can reference another condition, parameter value or mapping
- How to define a condition?
    - The logical ID is for you to choose, It's how you name condition
    - The intrinsic function (logical) can be any of the following
        - Fn::And
        - Fn::Equals
        - Fn::If
        - Fn::Not
        - Fn::Or

```yaml
Conditions:
  CreateProdResources: !Equals [ !Ref EnvType, prod ]
```

- Using a condition
    - Conditions can be applies to resources / outputs / etc...

```yaml
Resources:
  MountPoint:
    Type: "AWS::EC2::VolumeAttachment"
    Condition: CreateProdResources

```

### CloudFormation Intrinsic Functions

- Fn::Ref
    - The Fn::Ref function can be leveraged to reference
        - Parameters => returns the value of the parameter
        - Resources => returns the physical ID of the underlying resource (ex: EC2 ID)
    - The shorthand for this in YAML is !Ref
- Fn::GetAtt
    - Attributes are attached to any resources you create
    - To know the attributes of your resources, the best place is to look at is the documentation
    - `!GetAtt EC2Instance.AvailabilityZone`
- Fn::FindInMap Accessing Mapping Values
    - We use Fn::FindInMap to return a named value from a specific key
    - `!FindInMap [ MapName, TopLevelKey, SecondLevelKey ]`
- Fn::ImportValue
    - Import values that are exported in other templates
    - For this, we use Fn::ImportValue function
- Fn::Join
    - Join values with a delimiter
        - `!Join [ delimiter, [ commoa-delimited list of values ] ]`
    - This creates "a:b:c"
        - `!Join [ ":", [ a, b, c ] ]`
- Fn::Sub
    - Fn::Sub, or !Sub as a shorthand, is used to substitute variables from a text. It's very handy function that will
      allow you to fully customize your templates
    - For example, you can combine Fn::Sub with Reference or AWS Pseudo variables
    - String must contain ${VariableName} and will substitute them
    - `!Sub String`

```yaml
!Sub
- String
- { Var1Name: Var1Value, Var2Name: Var2Value }
```

- Condition Functions
    - The logical ID is for you to choose, It's how you name condition
    - The intrinsic function (logical) can be any of the following
        - Fn::And
        - Fn::Equals
        - Fn::If
        - Fn::Not
        - Fn::Or

### CloudFormation Rollbacks

- Stack Creation Fails:
    - Default: everything rolls back (gets deleted). We can look at the log
    - Option to disable rollback and troubleshoot what happened
- Stack Update Fails:
    - The stack automatically rolls back to the previous known working state
    - Ability to see in the log what happened and error messages

### CloudFormation Stack Notifications

- Send Stack events to SNS Topic (Email, Lambda, ...)
- Enable SNS Integration using Stack Options
- CloudFormation Stack (SNS Integration enabled) -> SNS -> Lambda Function (Filter only ROLLBACK_IN_PROGRESS events) ->
  SNS -> Email -> User

### CloudFormation ChangeSets, Nested Stacks & StackSet

- ChangeSets
    - When you update a stack, you need to know what changes before it happens for greater confidence
    - ChangeSets won't say if the update will be successful
- Nested stacks
    - Nested stacks are stacks as part of other stacks
    - They allow you to isolate repeated patterns / common components in seprate stacks and call them from other stacks
    - Example:
        - Load Balancer configuration that is re-used
        - Security Group that is re-used
    - Nested stacks are considered best practice
    - To update a nested stack, always update the parent (root stack)
- CloudFormation - Cross vs Nested Stacks
    - Cross Stacks
        - Helpful when stacks have different lifecycles
        - Use Outputs Export and Fn::ImportValue
        - When you need to pass export values to many stacks (VPC Id, etc...)
    - Nested Stacks
        - Helpful when components must be re-used
        - Ex: re-use how to properly configure an Application Load Balancer
        - The nested stack only is important to the higher level stack (it's not shared)
- CloudFormation - StackSets
    - Create, update or delete stacks across multiple accounts and regions with a single operation
    - Administrator account to create StackSets
    - Trusted accounts to create, update, delete stack instances from StackSets
    - When you update a stack set, all associated stack instances are updated throughout all accounts and regions

### CloudFormation Drift

- CloudFormation allows you to create infrastructure
- But it doesn't protect you against manual configuration changes
- How do we know if our resources have drifted
- We can use CloudFormation drift
- Not all resources are supported yet

### CloudFormation Stack Policies

- During a CloudFormation Stack update, all update actions are allowed on all resources (default)
- A Stack Policy is a JSON document that defines the update actions that are allowed on specific resources during Stack
  updates
- Protect resources from unintentional updates
- When you set a Stack Policy, all resources in the Stack are protected by default
- Specify an explicit ALLOW for the resources you want to be allowed to be updated