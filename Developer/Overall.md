## AWS Cloud Overview

### AWS Global Infrastructure

#### AWS Regions

- AWS has Regions all around the world
- Names can be us-east-1, eu-west-3
- A region is a cluster of data centers
- Most AWS Services are region-scoped

#### How to choose an AWS Region

- Compliance: with data governance and legal requirement: data never leaves a region without your explicit permission
- Proximity to customers: reduced latency
- Available services: within a Region: new services and new features aren't available in every region
- Pricing: pricing varies region to region and is transparent in the service pricing page

#### AWS Availability Zones

- Each region has many availability zones (usually 3, min is 3, max is 6). Example:
    - ap-southeast-2a
    - ap-southeast-2b
    - ap-southeast-2c
- Each AZ is one or more discrete data centers with redundant power, networking and connectivity
- They are separate from each other, so that they are isolated from disasters
- They are connected with high bandwidth, ultra-low latency networking

#### AWS Edge Locations / Points of Presence

- Amazon has 400+ Points of Presence (400+ Edge locations & 10+ Regional Caches) in 90+ cities across 40+ countries
- Content is delivered to end users with lower latency

#### Tour of the AWS Console

- AWS has Global Services
    - IAM
    - Route53
    - CloudFront
    - WAF
- Most AWS services are Region-scoped
    - Amazon EC2
    - Elastic Beanstalk
    - Lambda
    - Rekognition