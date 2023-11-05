## Scalability & High Availability

- Scalability means that an application / system can handle greater loads by adapting
- Scalability
    - Vertical Scalability
        - Increase instance size
        - Vertical scalability is very common for non distributed systems, such as database
        - RDS, ELastiCache are services that can scale vertically
        - There's usually a limit to how much you can vertically scale(hardware limit)
        - Scale up/down
    - Horizontal Scalability (Elasticity)
        - Increase number of instances / systems for your application
        - Horizontal scaling implies distributed systems
        - This is very common for web applications / modern applications
        - Scale out/in
- High Availability
    - High usually goes hand in hand with horizontal scaling
    - Running your application in at least 2 data centers (AZs)
    - The goal of high availability is to survive a data center loss
    - The high availability can be passive (for RDS Multi AZ for example)
    - The high availability can be active (for horizontal scaling)

### ELB (Elastic load balancer)

- Load balancing are servers that forward traffic to multiple servers downstream
- Why use Load Balancer
    - Spread load across multiple downstream instances
    - Expose a single point of access (DNS) to your application
    - Seamlessly handle failures of downstream instances
    - Do regular health checks to your instances
    - Provide SSL termination(SSL) for your websites
    - Enforce stickiness with cookies
    - High availability across zones
    - Separate public traffic from private traffic
- Why use an Elastic Load Balancer
    - Managed Load Balancer
        - AWS guarantees that it will be working
        - AWS takes care of upgrades maintenance, high availability
        - AWS provides only a few configuration knobs
    - It costs less to set up your own load balancer, but it will be a lot more effort on your end
    - It's integrated with many AWS offerings/services
        - EC2, EC2 AGS, Amazon ECS
        - AWS Certificate Manager, CloudWatch
        - Route53, AWS WAF, AWS Global Accelerator
- Health Checks
    - Health checks are crucial for load balancers
    - Let LB know if instances it forwards traffics to are available to reply to requests
    - Health check is done on a port and a route (/health is common)
    - If the response is not 200 (OK), then the instance is unhealthy
- Types of load balancer on AWS
    - 4 kinds of managed Load Balancers
        - HTTP, HTTPS, TCP, SSL (secure TCP)
    - Classic Load Balancer - Deprecated
    - Application load balancer
        - HTTP, HTTPS, WebSocket
        - Static URL
    - Network Load Balancer
        - TCP, TLS(Secure TCP), UDP - layer 4
        - High performance
    - Gateway Load Balancer
        - Operate at layer3 - IP Protocol
        - Route traffic to firewalls
        - Intrusion detection
    - Some LB can be set up as internal(private) or external(public) ELBs
- Load Balancer security groups
    - Load Balancer Security Group
    - Application Security Group: Allow traffic only from Load Balancer

### Application Load Balancer (V2)

- Layer 7 (HTTP)
- Load balancing to multiple HTTP applications across machines (target groups)
- Load Balancer to multiple applications on the same machine (containers)
- Support for HTTP/2 and WebSocket
- Support redirects (From HTTP to HTTPS for example)
- Routing tables to different target groups
    - Routing based on path in URL
    - Routing based on hostname in URL
    - Routing based on Query String, Headers
- ALB are great fit for microservices & container-based application
- Has a port mapping feature to redirect to a dynamic port in ECS
- In comparison, we'd need multiple Classic Load Balancer per application
- Target Groups
    - EC2 instances (can be managed by an ASG) - HTTP
    - ECS tasks (managed by ECS itself) - HTTP
    - Lambda functions - HTTP request is translated into a JSON event
    - IP Addresses - must be private IPs
    - ALB can route to multiple target groups
    - Health checks are at the target group level
- Good to know
    - Fixed hostname (xxx.region.elb.amazonaws.com)
    - The application servers don't see the IP of the client directly
        - The true IP of the client is inserted in the header X-Forwarded-For
        - We can also get port (X-Forwarded-Port) and proto (X-Forwarded-Proto)

### Network Load Balancer (v2)

- NLB (Layer4) allow to
    - Forward TCP/UDP traffic to your instances
    - Handle millions of requests per seconds
    - Less latency ~ 100ms (vs 400 ms for ALB)
- NLB has **one static IP per AZ** and supports assigning Elastic IP (helpful for whitelisting specific IP)
- NLB are used for extreme performance, TCP or UDP traffic
- Not included in AWS free tier
- Target Groups
    - EC2 instances
    - IP addresses - must be private IPs
    - Application Load Balancer
    - Health checks support the TCP, HTTP and HTTPS Protocols

### Gateway Load Balancer

- Deploy, Scale and manage a fleet of 3rd party network virtual appliances in AWS
- Example: Firewalls, Intrusion Detection and Prevention Systems, Deep Packet Inspection Systems, payload manipulation
- Operates at Layer3 (Network layer) - IP Packets
- Combines the following functions:
    - Transparent Network Gateway - single entry/exit for all traffic
    - Load Balancer - distributes traffic to your virtual appliances
- Uses the GENEVE protocol on port **6081**
- Target Groups
    - EC2 instances
    - IP addresses - must be private IPs

### ELB Sticky Sessions (Session Affinity)

- It's possible to implement stickiness so that the same client is always redirected to the same instance behind a load
  balancer
- This works for Classic/Application/Network Load Balancer
- The "cookie" used for stickiness has an expiration date you control
- Use case: make sure the user doesn't lose the session data
- Enabling stickiness may bring imbalance to the load over the backend EC2 instances
- Cookie Names
    - Application-based Cookies
        - Custom Cookie
            - Generated by the target
            - Can include any custom attributes required by the application
            - Cookie name must be specified individually for each target group
            - Don't use AWSALB, AWSALBAPP, or AWSALBTG
        - Application cookie
            - Generated by the load balancer
            - Cookie name is AWSALBAPP
    - Duration-based Cookies
        - Cookie generated by the load balancer
        - Cookie name is AWSALB for ALB, AWSELB for CLB

### Cross-Zone Load Balancing

- With Cross Zone Load Balancing
    - each load balancer instance distributes evenly across all registered instances in all AZ
- Without Cross Zone Load Balancing
    - Requests are distributed in the instances of the node of the Elastic Load Balancer
- Application Load Balancer
    - Enabled by default (Can be disabled at the target group level)
    - No charge for inter AZ data
- Network Load Balancer & Gateway Load Balancer
    - Disabled by default
    - You pay charges for inter AZ data if enabled
- Classic Load Balancer
    - Disabled by default
    - No charges for inter AZ data if enabled

### SSL/TLS Certificates

- Basics:
    - An SSL Certificate allows traffic between your clients and your load balancer to be encrypted in transit (
      in-flight encryption)
    - SSL refers to Secure Socket Layer, used to encrypt connections
    - TLS refers to Transport Layer Security, which is a newer version
    - TLS certificates are mainly used nowadays
    - Public SSL are issued by Certificate Authorities (CA)
    - SSL certificated has an expiration date and must be renewed
- Load Balancer - SSL certificates
    - The load balancer uses an X.509 certificate (SSL/TLS server certificate)
    - Can manage certificate using ACM (AWS Certificate Management)
    - Can upload your certificate alternatively
    - Clients can use SNI (Server Name Indication) to specify the hostname they reach
    - Ability to specify a security policy to support older versions of SSL/TLS (legacy clients)
- SSL - Server Name Indication
    - SNI solves the problem of loading multiple SSL certificates onto one web server (to multiple websites)
    - It's a newer protocol and requires the client to indicate the hostname of the target server in the initial SSL
      handshake
    - The server will find the correct certificate, or return the default one
    - Only works for ALB/NLB, CloudFront
    - Does not work for CLB
- ELastic Load Balancers -SSL Certificates
    - Classic Load Balancer (v1)
        - Support only one SSL certificate
        - Must use multiple CLB for multiple hostname with multiple SSL certificates
    - Application Load Balancer (v2)
        - Supports multiple listeners with multiple SSL certificates
        - Use Server Name Indication (SNI) to make it work
    - Network Load Balancer (v2)
        - Supports multiple listeners with multiple SSL certificates

### Connection Draining

- Feature Naming
    - Connection Draining - CLB
    - Deregistration Delay - ALB&NLB
- Time to complete 'in-flight' requests while the instance is de-registering on unhealthy
- Stop sending new requests to the EC2 instance which is de-registering
- Between 1-3600 seconds (default: 300 seconds)
- Can be disabled (set value to 0)
- Set to low value if your request is short

### Auto Scaling Group

- In real-life, the load on your websites and application can change
- In the cloud, you can create and get rid of servers very quickly
- The goal of ASG is to:
    - Scale out (add EC2 instances) tom match an increase load
    - Scale in (remove EC2 instances) to match a decrease load
    - Ensure we have a minimum and maximum number of EC2 instances running
    - Automatically register new instances to load balancer
    - Re-create an EC2 instance in case a previous one is terminated (ex: if unhealthy)
- ASG are free (you only pay for the underlying EC2 instances)
- ASG Attributes
    - A Launch Template (Older Launch Configurations are deprecated)
        - AMI + Instance Type
        - EC2 User Data
        - EBS Volumes
        - Security Groups
        - SSH Key Pair
        - IAM roles for EC2 instances
        - Network + Subnets Information
        - Load Balancer Information
    - Min size / Max size / Initial Capacity
    - Scaling Policies
- CloudWatch Alarms & Scaling
    - Scale ASG based on CloudWatch alarms
    - An alarm monitors a metric (such as an Average CPU, or a custom metric)
    - Metrics such as average CPU are computed for the overall ASG instances
    - Based on the alarm, we can:
        - Scale-out policies
        - Scale-in policies

### ASG Dynamic Scaling Policies

- Strategies
    - Manual scaling
    - Dynamic scaling
        - Target tracking scaling
            - Most simple and easy to set up
            - Example: Want the average ASG CPU to stay at around 40%
        - Simple/step scaling
            - When a CloudWatch alarm is triggered (example CPU > 70%), then add 2 units
            - When a CloudWatch alarm is triggered (example CPU < 30%), then remove 1 unit
    - Scheduled scaling
        - Anticipate a scaling based on known usage pattern
        - Example: increase the min capacity to 10 at 5 pm on Fridays
    - Predictive scaling
        - Continuously forecast load and schedule scaling ahead
- Good metrics to scale on
    - CPU Utilization: Average CPU utilization across your instances
    - Request Count per Target: to make sure the number of requests per EC2 instances is stable
    - Average Network In/Out (if your appliction is network bound)
    - Any custom metric (that you push using CloudWatch)
- Scaling Cooldowns
    - After a scaling activity happens, you are in the cooldown period (default 300 seconds)
    - During the cooldown period, the ASG will not launch or terminate additional instances (to allow metrics to
      stabilize)
    - Advice: Use a ready-to-use AMI to reduce configuration time in order to be serving request faster and reduce the
      cooldown period

### Auto Scaling - Instance Refresh

- Goal: update launch template and then re-creating all EC2 instances
- For this we can use the native feature of Instance Refresh
- Setting of minimum healthy percentage
- Specify warm-up time (how long until the instance is ready to use)
