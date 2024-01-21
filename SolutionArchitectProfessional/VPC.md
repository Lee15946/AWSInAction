# VPC

## VPC - Basics

### VPC Basics

- CIDR: Block of IP address
    - Example: 192.168.0.0/26: 192.168.0.0 - 192.1698.0.63 (64 IP)
    - Used for security groups, route tables, VPC, subnets, etc...
- Private IP
    - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8) <- in big networks
    - 172.16.0.0 - 172.31.255.255 (172.16.0.0/12)
    - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16) <- example: home networks
- Public IP
    - All the rest
- VPC
    - A VPC must have a defined list of CIDR blocks, that cannot be changed
    - Each CIDR within VPC: min size is /28, max size is /16 (65536 IP addresses)
    - VPC is private, so only Private IP CIDR ranges are allowed
- Subnets
    - Within a VPC, defined as a CIDR that is a subset of the VPC CIDR
    - All instances within subnets get a private IP
    - First 4 IP and last one in every subnet are reserved by AWS
- Route Tables
    - Used to control where the network traffic is directed to
    - Can be associated with specific subnets
    - The "most specific" routing rule is always followed (192.168.0.1/24 beats 0.0.0.0/0)
- Internet Gateway (IGW)
    - Helps our VPC connect to the internet, HA, scales horizontally
    - Acts as a NAT for instances that have a public IPv4 or public IPv6
- Public Subnets
    - Has a route table that sends 0.0.0.0/0 to an IGW
    - Instances must have a public IPv4 to talk to the internet
- Private Subnets
    - Access internet with a NAT Instance or NAT Gateway setup in a public subnet
    - Must edit routes so that 0.0.0.0/0 routes traffic to the NAT

### VPC Basics - NAT Instance

- EC2 instance you deploy in a public subnet
- Edit the route in your private subnet to route 0.0.0.0/0 to your NAT instance
- Not resilient to failure, limited bandwidth based on instance type, cheap
- Must manage failover yourself
- Must disable Source/Destination Check (EC2 setting)

### VPC Basics - NAT Gateway

- Managed NAT solution, bandwidth scales automatically
- Resilient to failure within a single AZ
- Must deploy multiple NAT Gateways in multiple AZ for HA
- Has an Elastic IP, external services see the IP of the NAT Gateway as the source

### VPC Basics

- Network ACL (NACL)
    - Stateless firewall defined at the subnet level, applies to all instances within
    - Support for allow and deny rules
    - Stateless = return traffic must be explicitly allowed by rules
    - Helpful to quickly and cheaply block specific IP addresses
- Security Groups
    - Applied at the instance level, only support for allow rules, no deny rules
    - Stateful = return traffic is automatically allowed, regardless of rules
    - Can reference other security groups in the same region (peered VPC, cross-account)
- VPC Flow Logs
    - Log internet traffic going through your VPC
    - Can be defined at the VPC level, Subnet level, or ENI-level
    - Helpful to capture "defined internet traffic"
    - Can be sent to CloudWatch Logs and Amazon S3
- Bastion Hosts
    - SSH into private EC2 instances through a public EC2 instance (bastion host)
    - You must manage these instances yourself (failover, recovery)
    - SSM Session Manager is a more secure way to remote control without SSH
- IPv6 in short
    - All IPv6 addresses are public, total 3.4x10^38 addresses (vs 4.3 billion IPv4)
    - Example CIDR: 2600:IfI8:80c:a900::/56
    - Addresses are "random" and can't be scanned online (because too many)
- VPC support for IPv6
    - Create an IPv6 CIDR for VPC & use an IGW (supports IPv6)
    - Public subnet:
        - Create an instance with IPv6 support
        - Create a route table entry to ::/0 (IPv6 "all") to the IGW
    - Private subnet (instances cannot be reached by IPv6 but can reach IPv6)
        - Create an Egress-Only Internet Gateway in the VPC
        - Add a route table entry for the private subnet from ::/0 to the Egress-Only IGW

## VPC Peering

- Connect two VPC, privately using AWS' network
- Make them behave as if they were in the same network
- Must not have overlapping CIDR
- VPC Peering connection is not transitive (must be established for each VPC that need to communicate with one anohter)
- You can do VPC peering with other AWS account
- You must update route tables in each VPC's subnets to ensure instances can communicate

### VPC Peering - Good to know

- VPC peering can work inter-region, cross-account
- You can reference a security group of a peered VPC (works cross account)

### VPC Peering - Longest Prefix Match

- VPC uses the longest prefix match to select the most specific route
- Here the longest prefix for 10.0.0.077 is 10.0.0.077/32 (route table VPC A)
- (other way of saying it is "most specific route")

### VPC Peering - Invalid Configurations

- Overlapping CIDR for IPv4
- No Transitive VPC Peering
- No Edge to Edge Routing
    - VPN, Direct Connect, IGW, NAT, Gateway VPC Endpoint (S3 & DynamoDB)

### VPC Peering - Invalid Configurations - No edge to edge routing

- This is an invalid configuration
- VPC Peering does not support edge to edge routing for NAT devices

## Transit Gateway

- For having transitive peering between thousands of VPC and on-premise, hub-and-spoke (star) connection
- Regional resource, can work cross-region
- Share cross-account, using Resource Access Manager (RAM)
- You can peer Transit Gateway across regions
- Route Tables: limit which VPC can talk other VPC
- Works with Direct Connect Gateway, VPN connections
- Supports IP Multicast (not supported by any other AWS service)
- Instances in a VPC can access a NAT Gateway, NLB, PrivateLink, and EFS in other VPCs attached to the AWS Transit Gateway

### Transit Gateway - Central NAT Gateway

- The NAT Gateway is shared in the Egress-VPC
- The private App VPC can access internet through the TGW
- In this example: the App VPCs cannot communicate with each other based on the TGW route table

### Transit Gateway - Sharing through RAM

- You can use AWS RAM to share a Transit Gateway for VPC attachments across accounts or across AWS Organization

### Transit Gateway - Use different Route Tables to Prevent VPC from Communicating

### Transit Gateway - Inter & Intra Region Peering

- Billed hourly for each peering attachment, no data processing charges
- If data goes cross-region through the attachment, standard charges
- Specify the Transit Gateway ID

## VPC Endpoints

- Endpoints allow you to connect to AWS Services using a private network instead of the public www network
- They scale horizontally and are redundant
- No more IGW, NAT, etc... to access AWS Services
- VPC Endpoint Gateway (S3 & DynamoDB)
- VPC Endpoint Interface (all except DynamoDB)
- In case of issues:
    - Check DNS Setting Resolution in your VPC
    - Check Route Tables

### VPC Endpoint Gateway

- Must update route tables entriespriva
- Gateway is defined at the VPC level
- DNS resolution must be enabled in the VPC
- The same public hostname for S3 can be used
- Gateway endpoint cannot be extended out of a VPC (VPN, DX, TGW, peering)

### VPC Endpoints Interface

- Provision an ENI that will have a private endpoint interface hostname
- Leverage Security Groups for security
- Private DNS (setting when you create the endpoint)
    - The public hostname of a service will resolve to the private Endpoint Interface hostname
    - VPC Setting: `Enable DNS hostnmaes` and `Enable DNS Support` must be true
        - Example for Athena
            - `athena.us-east-1.amazonaws.com` (private DNS name)
- Interface can be accessed from Direct Connect and Site-to-Site VPN

## VPC Endpoint Policies

- Endpoint Policies are JSON documents to control access to services
- Does not override or replace IAM user policies or service-specific policies (such as S3 bucket policies)
- Note: the IAM user can still use other SQS API from outside the VPC Endpoint
- You could add an SQS queue policy to deny any action not done through the VPC endpoint

### VPC Endpoint Policy & S3 bucket policy

- S3 bucket policy may have
    - Condition: `aws:sourceVpce:vpce-1a2b3c4d` to Deny any traffic that doesn't come from a specific VPC endpoint (more secure)
    - Condition: `aws:sourceVpc:vpc-111bbb22` for a specific VPC
- The `aws:sourceVpc` condition only works for VPC Endpoints, in case you have multiple endpoints and want to manage access to your S3 buckets for all your endpoints
- The S3 bucket policies can restrict access only from a specific public IP address or an elastic IP address. You can't restrict based on private IP
- Therefore `aws:SourceIp` condition doesn't apply for VPC endpoints

### VPC Endpoint Policies for S3 Troubleshooting

- Check SG (Outbound Rules)
- Route table (Must have route to S3, using Gateway VPC Endpoint)
- VPC Endpoint Gateway (Check VPC Endpoint Policy)
- Check VPC DNS settings, DNS resolution must be enabled
- Verify S3 bucket policy
- Check IAM permission

## PrivateLink

### AWS PrivateLink (VPC Endpoint Services)

- Most secure & scalable way to expose a service to 1000s of VPC (own or other accounts)
- Does not require VPC peering, internet gateway, NAT, route tables
- Requires a network load balancer (Service VPC) and ENI (Customer VPC)
- If the NLB is in multiple AZ, and the ENI in multiple AZ, the solution is fault tolerant

### PrivateLink for Amazon S3 with Direct Connect

- Corporate Data Center -> Direct Connect
    - -> Public VIF (Virtual Interfaces) -> Amazon S3
    - -> Private VIF -> PrivateLink -> Interface VPC Endpoint -> Amazon S3

### VPC Endpoints / PrivateLink and VPC Peering

- us-east-1 (EC2 Instances) -> VPC Peering -> eu-west-1 -> Interface VPC Endpoint -> Amazon S3

## AWS S2S VPN

### Site to Site VPN (AWS ManagedVPN)

- On-premise:
    - Setup a software of hardware VPN appliance to your on-premise network
    - The on-premise VPN should be accessible using a public IP
- AWS-side
    - Setup a Virtual Private Gateway (VPG) and attach to your VPC
    - Setup a Customer Gateway to point the on-premise VPN appliance
- Two VPN connections (tunnels) are created for redundancy, encrypted using IPSec
- Can optionally accelerate it using Global Accelerator (for worldwide networks)

### Route Propagation in Site-to-Site VPN

- Static Routing
    - Create static route in corporate data center for 10.0.0.1/24 through the CGW
    - Create static route in AWS for 10.3.0.0/20 through the VGW
- Dynamic Routing (BGP)
    - Use BGP (Broder Gateway Protocol) to share routes automatically (eBGP for internet)
    - We dont't need to update the routing tables, it will be done for us dynamically
    - Just need to specify the ASN (Autonomous System Number) of the CGW and VGW

### Site to Site VPN and Internet Access

- NAT Gateway
    - NOT OKAY (blocked by NAT Gateway restrictions)
- NAT Instance
    - OKAY (self managed NAT Instance - more control)
- On-premise NAT
    - OKAY (alternative to NAT Instances/Gateway)

### AWS VPN CloudHub

- Can connect up to 10 Customer Gateway for each Virtual Private Gateway (VGW)
- Low cost hub-and-spoke module for primary or secondary network connectivity between locations
- Provides secure communication between sites, if you have multiple VPN connection
- It's a VPN connection so it goes over the public internet
- Can be a fail over connection between your on-premise locations

### VPC to multiple VPC

- For VPN-based customers, AWS recommends creating a separate VPN connection for each customer VPC
- Direct Connect is recommended beacuse it has a Direct Connect Gateway

### Shared Services VPC

- Create a VPN connection between on-premise and shared service VPC
- Replicate services, applications, databases between on-premise and the Shared Services VPC or deploy proxies in the shared service VPC
- Do VPC peering between the VPC and the shared service VPC
- Note: yes this could be replaced by a Transit Gateway, but it's good to see different solution architectures
- VPCs can directly access the shared Service VPC services and do not need VPN connections to on-premise

## AWS Client VPN

- Connect from your computer using OpenVPN to your private network in AWS and on-premises
- Computer with AWS Client VPN (OpenVPN) -> Uses Private IP -> Private Subnet
    - -> VGW -> Site-to-Site VPN -> CGW -> Corporate data center
    - -> Use private IP -> Corporate data center

### Client VPN - PeeredVPC

- Client VPN is compatible with VPC Peering
- Clients -> Client VPN Endpoint -> Client VPN ENI (VPC-A) -> VPC Peering -> VPC-B

### Client VPN - Access to On-Premises

- Access On-Premises resources through AWS with ClientVPN
- Clients -> Client VPN Endpoint -> Client VPN ENI (VPC-A) -> S2S VPN -> Corporate Data Center

### Client VPN - Internet Access

- Users -> Internet -> AWS Client VPN -> ENI -> Internet Gateway
- Users -> Internet -> AWS Client VPN -> ENI (Private Subnet) -> NATGW -> Internet Gateway

### Client VPN - Transit Gateway

- Users -> Internet -> AWS Client VPN -> ENI (VPC - A) -> Transit Gateway
    - VPC - B
    - Direct Connect / VPN Connection -> Customer Gateway -> Corporate Data Center

## Direct Connect

- Provides a dedicated private connection from a remote network to your VPC
- Dedicated connection must be setup between your DC and AWS Direct Connect locations
- More expensive than running a VPN solution
- Private access to AWS services through VIF
- Bypass ISP, reduce network cost, increase bandwidth and stability
- Not redundant by default (must setup a failover DX or VPN)

### Direct Connect - Virtual Interfaces (VIF)

- Public VIF - connect to Public AWS Endpoints (S3 buckets, EC2 service, anything AWS...)
- Private VIF - connect to resources in your VPC (EC2 instances, ALB,...)
- Transit Virtual Interface - connect to resources in a VPC using a Transit Gateway
- VPC Interface Endpoints can be accessed through Private VIF

### Direct Connect Diagram

- VPC (Private Subnet) -> EC2 Instances -> VPG -> AWS Direct Connect Endpoint (AWS Cage) -> Customer or partner router (Customer or partner cage) -> Customer router / firewall (Customer Network)

### Direct Connect - Connection Types

- Dedicated Connections: 1Gbps, 10Gbps, 100Gbps capacity
    - Physical ethernet port dedicated to a customer
    - Request made to AWS first, then completed by AWS Direct Connect Partners
- Hosted Connections: 50Mbps, 500Mbps, to 10Gbps
    - Connection request are made via AWS Direct Connect Partners
    - Capacity can be added or removed on demand
    - 1,2,5,10Gbps available at select AWS Direct Connect Partners
- Lead times are often longer than 1 month to establish a new connection

### Direct Connect - Encryption

- Data in transit is not encrypted but is private
- AWS Direct Connect + VPN provides an IPsec-encrypted private connection
- VPN over Direct Connect connection Uses Publikc VIF
- Good for an extra level of security, but slightly more complex to put in place

### Direct Connect - Link Aggregation Groups

- Get increased speed and failover by summing up exisitng DX connections into a logical one
- Can aggregate up to 4 connections (active-active mode)
- Can add connections over time to the LAG
- All connections in the LAG
    - Must be dedicated connections
    - Must have the same bandwidth
    - Must terminated at the same AWS Direct Connect Endpoint
- Can set a minimum number of connections for the LAG to function

### Direct Connect Gateway

- If you want to setup a Direct Connect to one or more VPC in many different regions (same/cross account), you must use a Direct Connect Gateway
-
    + Transit Gateway

## On-premise Redundant Connections

### Site-to-Site Active-Active Connection

- Active-Active VPN Connection

### Direct Connect - High Availability

- Multiple connections at multiple AWS Direct Connect locations
- Backup VPN Connection

### Direct Connect Gateway — SiteLink

- Allows you to send data from on Direct Connect location to another
- Bypassing AWS Regions
- Use cases: create private network connections between on-premises data centers by connecting them to Direct Connect locations
- Data is sent over the fastest path between Direct Connect locations

## VPC Flow Logs

- Capture information about IP traffic going into your interfaces
    - VPC Flow Logs
    - Subnet Flow Logs
    - Elastic Network Interface (ENI) Flow Logs
- Helps to monitor & troubleshoot connectivity issues
- Flow logs data can go to S3, CloudWatch Logs, and Kinesis Data Firehose
- Captures network information from AWS manages interfaces too: ELB, RDS, ElastiCache, Redshif, WorkSpaces, NATGW, Transit Gateway...

### VPC Flow Logs Syntax

- srcaddr & dstaddr - help identify problematic IP
- `srcport` & `dstport` - help identify prolematic prots
- `Action` - success or failure if the request due to Security Group / NACL
- Can be used to analytics on usage patterns, or malicious behavior
- QueryVPC flow logs using Athena on S3 or CloudWatch Logs Insights

### VPC Flow Logs - Troubleshoot SG & NACI issues

- Look at the "ACTION" filed
- Incoming Requests
    - Inbound REJECT => NACL or SG
    - Inbound ACCEPT, Outbound REJECT => NACL
- Outgoing Requests
    - Outbound REJECT => NACL or SG
    - Outbound ACCEPT, Inbound REJECT => NACL

### VPC Flow Logs - Architectures

- VPC Flow Logs -> CloudWatch Logs -> CloudWatch Contributor Insights -> Top-10 IP addresses
- VPC Flow Logs -> CloudWatch Logs -> Metric Filter (SSG, RDP...) -> CW Alarm -> Alert -> Amazon SNS
- VPC Flow Logs -> S3 Bucket -> Amazon Athena -> Amazon QuickSight

### VPC Flow Logs - with NAT Gateway

- My Virtual Private Cloud (VPC) flow logs show Action - ACCEPT for inbound traffic coming from public IP addresses. However, my understanding of network address tranlation (NAT) gateways was that they don't accept traffics from the internet. Is my NAT gateway acception inbound traffic from the internet?
- Inboud traffic is permitted by SG or NACLs
    - Traffics isn't permitted by the NAT Gateway, its' dropped
    - To confirm, run the folloiwng query in CloudWatch Log Group
        - `filter (disAddr like 'xxx.xxx' and srcAddr like 'public IP' \ stats sum (bytes) as bytesTransferred by srcAddr, dstAddr | limit 10`
- Make 'xxx.xxx' the first two octets of your VPC CIDR
- Replace Public IP with the IP you see in the logs
- You will see traffic on the Private IP of the NAT Gateway but nowherer else: traffics was unsolicited and then dropped

## AWS Network Firewall

### Network Protection on AWS

- To protect network on AWS, we've seen
    - Network Access Control Lists (NACLs)
    - Amazon VPC security groups
    - AWS WAF (protect against malicious requests)
    - AWS Shiled & AWS Shiled Advanced
    - AWS Firewall Manager (to manager them across accounts)
- But what if we want to protect in a sophisticated way our entire VPC?

### AWS Network Firewall

- Protect your entire Amazon VPC
- From Layer 3 to Layer 7 protection
- Any direction, you can inspect
    - VPC to VPC traffic
    - Outbound to internet
    - Inbound from internet
    - To / from Direct Connect & Site-to-Site VPN
- Internally, the AWS Network Firewall uses the AWS Gateway Load Balancer
- Rules can be centrally managed cross-account by AWS Firewall Manager to apply to many VPCs

### Network Firewall - Fine Grained Controls

- Supports 1000s of rules
    - IP & port - example: 10,000s of IPs filtering
    - Protocol - example: block the SMB protocol for outbound communications
    - Stateful domain list rule groups: only allows outbound traffic to *.mycorp.com or third-party software repo
    - General patern matching using regex
- Traffic filtering: Allow, drop, or alert for the traffic that matches the rules
- Active flow inspection to protect against network threats with intrusion-prevention capabilities (like Gateway Load Balacner, but all managed by AWS)
- Send logs of rule matched to Amazon S3, CloudWatch Logs, Kinesis Data Firehose

### AWS Network Firewall - Architecture

- North-South VPC egress to Internet
- North-South VPC to VPN pr Direct Connect
- East-West VPC to VPC





















