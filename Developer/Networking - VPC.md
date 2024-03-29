## Networking - VPC

### CIDR, Private vs Public IP

- CIDR
    - Classless Inter-Domain Routing (无类域间路由)- a method for allocating IP addresses
    - Used in Security Groups rules and AWS networking in general
    - They help to define an IP address range:
        - We've seen WW.XX.YY.ZZ/32 => one IP
        - We've seen 0.0.0.0/0 => all IPs
        - But we can define 192.168.0.0/26 => 192.168.0.0 - 192.168.0.63 (64 IP addresses)
    - A CIDR consists of two components
    - Base IP
        - Represents an IP contained in the range (XX.XX.XX.XX)
        - Example: 10.0.0.0.0, 192.168.0.0...
    - Subnet Mask
        - Define how many bits can change in the IP
        - Example: /0, /24, /32
        - Can take two forms
            - /8 => 255.0.0.0
            - /16 => 255.255.0.0
            - /24 => 255.255.255.0
            - /32 => 255.255.255.255
        - The Subnet Mask basically allows part of the underlying IP to get additional next values from the base IP
        - Quick Memo
            - /32 - no octet can change
            - /24 - last octet can change
            - /16 - last 2 octets can change
            - /8 - last 3 octets can change
            - /0 - all octets can change
- Public vs Private IP (IPv4)
    - The Internet Assigned numbers authority (IANA) established certain blocks of IPv4 addresses for the use of
      private (LAN) and public (Internet) addresses
    - Private IP can only allow certain values
        - 10.0.0.0 - 10.255.255.255 (10.0.0.0/8) <- in big networks
        - 172.16.00 - 172.31.255.255 (172.16.00/12) <- AWS default VPC in that range
        - 192.168.0.0 - 192.168.255.255 (192.168.0.0/16) <- e.g, home networks
    - All the rest of the IP addresses on the Internet are public

### Default VPC

- All new AWS Accounts have a default VPC
- New EC2 instances are launched into the default VPC if no subnet is specified
- Default VPC has Internet connectivity and all EC2 instances inside it have public IPv4 addresses
- We also get a public and a private IPv4 DNS names

### VPC

- VPC = Virtual Private Cloud
- You can have multiple VPCs in an AWS region (max. 5 per region - soft limit)
- Max.CIDR per VPC is 5, for each CIDR
    - Min size is /28(16 IP addresses)
    - Max size is /16(65536 IP addresses)
- Because VPC is private, only the private IPv4 ranges are allowed
- You VPC CIDR should NOT overlap with your other networks (e.g, corporate)

### Subnet

- VPC - Subnet (IPv4)
    - AWS reserves 5 IP addresses (first 4 & last 1) in each subnet
    - These 5 IP addresses are not available for use and can't be assigned to an EC2 instance
    - Example: if CIDR is 10.0.0.0/24, then reserved IP addresses are:
        - 10.0.0.0 - Network Address
        - 10.0.0.1 - reserved by AWS for the VPC router
        - 10.0.0.2 - reserved by AWS for mapping to Amazon-provided DNS
        - 10.0.0.3 - reserved by AWS for future use
        - 10.0.0.255 - Network Broadcast Address, AWS does not support broadcast in a VPC, therefore the address is
          reserved
    - Exam Tip, if you need 29 IP addresses for EC2 instances
        - You can't choose a subnet of size /27 (32 Ip addresses)
        - You need to choose a subnet of size /26 (63 IP addresses)

### Internet Gateway (IGW)

- Allows resources (e.g, EC2 instances in a VPC connect to the Internet
- It scales horizontally and is highly available and redundant
- Must be created seperately from a VPC
- One VPC can only be attached to one IGW and vice versa
- Internet Gateway on their own do not allow Internet access
- Route tables must also be edited!

### Bastion Hosts

- We can use a Bastion Host to SSH into our private EC2 instances
- The bastion is in the public subnet which is then connected to all other private subnets
- Bastion Host security group must allow inbound from the internet on port 22 from restricted CIDR, for example the
  public CIDR of your corporation
- Security Group of the EC2 Instances must allow the Security Group of the Bastion Host, or the private IP of the
  Bastion host

### NAT Instance (outdated)

- NAT = Network Address Translation
- Allows EC2 instances in private subnets to connect to the Internet
- Must be launched in a public subnet
- Must disable EC2 setting: Source/destination Check
- Must have Elastic IP attached to it
- Route Tables must be configured to route traffic from private subnets to the NAT Instance
- Pre-configured Amazon Linux AMI is available
    - Reached the end of standard support on December 31, 2020
- Not highly available / resilient setup out of the box
    - You need to create as ASG in multi-AZ + resilient user-data script
- Internet traffic bandwidth depends on EC2 instance type
- You must managed Security Groups & rules:
    - Inbound
        - Allow HTTP/HTTPS traffics coming from Private Subnets
        - Allow SSH from your home network (access is provided thorugh Internet Gateway)
    - Outbound
        - Allow HTTP/HTTPS to the Internet

### NAT Gateway

- AWS-managed NAT, higher bandwidth, high availability, no administration
- Pay per hour for usage and bandwidth
- NATGW is created in a specific Availability Zone, uses an Elastic IP
- Can't be used by EC2 instance in the same subnet (only for other subnet)
- Requires an IGW (Private Subnet => NATGW => IGW)
- 5 Gbps of bandwidth with automatic scaling up to 100 Gbps
- No Security Groups to manage / required
- NATGateway with High Availability
    - NAT Gateway is resilient within a single Availability Zone
    - Must create mutiple NAT Gateways in multiple AZs for fault-tolerance
    - There is no cross-AZ failover needed because if an AZ goes down it doesn't need NAT

### NACL & Security Groups

- Network Access Control List (NACL)
    - NACL are like a firewall which control traffic from and to subnets
    - One NACL per subnet, new subnets are assigned the Default NACL
    - You define NACL Rules:
        - Rules have a number (1-32766), higher precedence with a lower number
        - First rule match will drive the decision
        - The last rule is an asterisk(*) and denies a request in case of no rule match
        - AWS recommends adding rules by increment of 100
    - Newly created NACLs will deny everything
    - NACL are a great way of blocking a specific IP address at the subnet level
- Default NACL
    - Accepts everything inbound/outbound with the subnets it's assoicated with
    - Do NOT modify the Default NACL, instead create custom NACLs
- Ephemeral Ports
    - For any two endpoints to establish a connection, they must use ports
    - Clients connect to a defined port, and expect a response on a ephemeral port
    - Different Operating Systmes use different port ranges, examples:
        - IANA & MS Windows 10 -> 49152 - 65535
        - Many Linux Kernels -> 32768 - 60999
- Security Group vs NACLs
    - Security Group
        - Operates at the instance level
        - Supports allow rules only
        - Stateful: return traffics is automatically allowed, regardless of any rules
        - All rules are evaluated before deciding whether to allow traffics
        - Applies to an EC2 instance when specified by someone
    - NACL
        - Operates at the subnet level
        - Supports allow rules and deny rules
        - Stateless: return traffic must be explicitly allowed by rules (think of ephemeral ports)
        - Rules are evaluated in order (lowest to highest) when deciding whether to allow traffic, first match wins
        - Automatically applies to all EC2 instances in the subnet that it's associated with

### VPC Peering

- Privately connect two VPCs using AWS network
- Make then behave as if they were in the same network
- Must no have overlapping CIDRs （IP address range)
- VPC Peering connection is NOT transitive (must be established for each VPC that need to communicate with one another)
- You must update route tables in each VPC's subnets to ensure EC2 instances can communicate with each other
- You can create VPC Peering connection between VPCs in difference AWS accounts/regions
- You can reference a security group in a peered VPC (work across accounts - same region)

### VPC Endpoints

- VPC Endpoints (AWS PrivateLink)
    - Every AWS service is publicly exposed (public URL)
    - VPC Endpoints (powered by AWS PrivateLink) allows you to connect to AWS services using a private network instead
      of using the public internet
    - This gives you enhanced security and lower latency to access AWS services
    - Only used within your VPC
    - They are redundant and scale horizontally
    - They removed the need to IGW, NATGW... to access AWS Services
    - In case of issues:
        - Check DNS Setting Resolution in your VPC
        - Check Route Tables
- Type of Endpoints
    - Interface Endpoints (powered by PrivateLink)
        - Provisions an ENI (private IP address) as an entry point (must attach an Security Group)
        - Supports most AWS services
        - $ per hour + $ per GB of data processed
    - Gateway Endpoints
        - Provisions a gateway and must be used as a target in a route table (does not use security groups)
        - Supports both S3 and DynamoDB
        - Free
- Gateway or Interface Endpoint for S3?
    - Gateway is most likely going to preferred all the time at the exam
    - Cost: free for Gateway, $ for interface endpoint
    - Interface Endpoint is preferred access is required from on-premises (Site to Site VPN or Direct Connect), a
      difference VPC or a different region

### VPC Flow Logs

- Capture information about IP traffic going into your interfaces:
    - VPC Flow Logs
    - Subnet Flow Logs
    - Elastic Network Interface(ENI) Flow Logs
- Helps to monitor & troubleshoot connectivity issues
- Flow logs data can go to S3, CloudWatch Logs, and Kinesis Data Firehose
- Capture network information from AWS managed interfaces too: ELB, RDS, ElastiCache, Redshift, WorkSpaces, NATGW,
  Transit Gateway
- VPC Flow Logs Syntax
    - srcaddr * dstaddr - help identify problematic IP
    - srcport - dstport - help identify problematic ports
    - Action - success or failure of the request due to Security Group / NACL
    - Can be used for analytics on usage patterns, or malicious behavior
    - Query VPC Flow Logs using Athena on S3 or CloudWatch Logs Insights
- VPC Flow Logs - Troubleshoot SG & NACL issues
    - Look at the "ACTION" filed
    - Incoming Requests
        - Inbound REJECT => NACL or SG
        - Inbound ACCEPT, Outbound REJECT => NACL
    - Outgoing Requests
        - Outbound REJECT => NACL or SG
        - Outbound ACCEPT, Inbound REJECT => NACL
- VPC Flow Logs - Architectures
    - VPC Flow Logs -> CloudWatch Logs -> CloudWatch Contributor Insights -> Top-10 IP addresses
    - VPC Flow Logs -> CloudWatch Logs -> CW Alarm -> Amazon SNS
    - VPC Flow Logs -> S3 Bucket -> Amazon Athena -> Amazon QuickSight

### Site-Site VPN, Virtual Private Gateway & Customer Gateway

- AWS Site-to-Site VPN
    - Overall
        - Connect an on-premises VPN to AWS
        - The connection is automatically encrypted
        - Goes over the public internet
    - Virtual Private Gateway (VGW)
        - VPN concentrator on the AWS side of the VPN connection
        - VGW is create and attached to the VPC from which you want to create the Site-to-Site VPN connection
        - Possibility to customize the ASN (Autonomous System Number)
    - Customer Gateway (CGW)
        - Software application or physical device on customer side of the VPN connection
- Site-to-Site VPN connections
    - Customer-Gateway Device (On-premises)
        - What IP address to use
            - Public Internet-routable IP address for your Customer Gateway device
            - If it's behind a NAT device that's enabled for NAT traversal(NAT-T), use the pulic IP of the NAT device
    - Important step: enable Route Propagation for the Virtual Private Gateway in the route table that is associated
      witn your subnets
    - If you need to ping your EC2 instances from on-premises, make sure you add the ICMP protocol on the inbound of
      your security groups
- AWS VPN CloudHub
    - Provide secure communication between multiple sites, if you have multiple VPN connections
    - Low-cost hub-and-spoke model for primary or secondary network connectivity between different locations (VPN only)
    - It's a VPN connection so it goes over the public internet
    - To set it up, connect multiple VPN connections on the same VGW, setup dynamic routing and configure route tables

### Direct Connect (DX)

- Overall
    - Establish a physical connection between on-premises and AWS
    - The connection is private, secure and fast
    - Goes over a private network
    - Take at least a month to establish
- Provides a dedicated private connection from a remote network to your VPC
- Dedicated connection must be setup between your DC and AWS Direct Connect locations
- You need to setup a Virtual Private Gateway on your VPC
- Access public resources (S3) and private (EC2) on same location
- Use cases:
    - Increase bandwidth throughput - working wiht large data sets - lower cost
    - More consistent network experience - applications using real-time data feeds
    - Hybrid Environments (on prem + cloud)
- Supports both IPv4 and IPv6
- Direct Connect Diagram
    - Customer router/firewall (Customer Network)-> Customer or partner router (Customer or partner cage) -> AWS Direct
      Connect Endpoint (AWS Cage) (AWS Direct Connect Location)-> Private Virtual Interface -> EC2 instances
    - Customer router/firewall -> Customer or partner router -> AWS Direct Connect Endpoint -> Public Virtual
      Interface -> EC2 instances
- Direct Connect Gateway
    - If you want to setup a Direct Connect to one or more VPC in many different regions (same account), you must use a
      Direct Connect Gateway
- Direct Connect - Connection Types
    - Dedicated Connections: 1Gbps, 10Gbps, and 100 Gbps capacity
        - Request made to AWS first, then completed by AWS Direct Connect Partners
    - Hosted Connections: 50Mbps, 500 Mbps, to 10 Gbps
        - Connection requests are made via AWS Direct Connect Partners
        - Capacity can be added or removed on demand
        - 1, 2, 5, 10 Gbps available at select AWS Direct Connect Partners
    - Lead times are often longer than 1 month to establish a new connection
- Direct Connect - Encryption
    - Data in transit is not encrypted but is private
    - AWS Direct Connect + VPC provides an IPsec-encrypted private connection
    - Good for an extra level of security, but slightly, more complex to put in place
- Direct Connect - Resiliency
    - High Resiliency for Critical Workloads
        - One connection at multiple locations
    - Maximum Resiliency for Critical Workloads
        - Maximum resilience is achieved by separate connections terminating on separate devices in more than one
          location
- Site-to-Site VPC connection as a backup
    - In case Direct Connect fails, you can set up a backup Direct Connect connection (expensive), or a Site-to-Site VPN
      connection

### Transit Gateway

- For having transitive peering between thousands of VPC and on-premises, hub-and-spoke (star) connection
- Regional resource, can work cross-region
- Share cross-account using Resource Access Manager (RAM)
- You can peer Transit Gateways across regions
- Route Tables: limit with VPC can talk with other VPC
- Works with Direct Connect Gateway, VPN connections
- Support IP Multicast (not supported by any other AWS service)
- Transit Gateway: Site-to-Site VPN ECMP
    - ECMP = Equal-cost multi-path routing
    - Routing strategy to allow to forward a packer over multiple best path
    - Use case: create multiple Site-to-Site VPN connections to increase the bandwidth of your connection to AWS
    - Transit Gateway - Share Direct Connect between multiple accounts
        - You can use AWS Resource Access Manager to share Transit Gateway with other account

### VPC - Traffic Mirroring

- Allows you to capture and inspect work traffic in your VPC
- Route the traffic to security appliances that you manage
- Capture the traffic
    - From (Source) - ENIs
    - To (Targets) - an ENI or a Network Load Balancer
- Capture all packets or capture the packets of your interest (optionally, truncate packets)
- Source and Target can be in the same VPC or different VPCs (VPC peering)
- Use cases: content inspection, threat monitoring, troubleshooting

### IPv6 for VPC

- What is IPv6
    - IPv4 designed to provide 4.3 billion addresses (they'll be exhausted soon)
    - IPv6 is the successor of IPv4
    - IPv6 is designed to provide 3.4x10^38 unique IP addresses
    - Format -> x.x.x.x.x.x.x.x (x is hexadecimal,. range can be from 0000 to ffff)
- IPv6 in VPC
    - IPv4 can not be disabled for your VPC and subnets
    - You can enable IPv6 (they are public IP addresses) to operate in dual-stack mode
    - Your EC2 instances will get at least a private internal IPv4 and a public IPv6
    - They can communicate using either IPv4 or IPv6 to the internet through an Internet Gateway
- IPv6 Troubleshooting
    - IPv4 cannot be disabled for your VPC and subnets
    - So if you can not launch an EC2 instance in your subnet
        - It's not because it can not acquired an IPv6 (the space is very large)
        - It's because there are no available IPv4 in your subnet
    - Solution: create a new IPv4 CIDR in your subnet

### Egress-only InternetGateway

- Used for IPv6 only
- similar to a NAT Gateway but for IPv6
- Allows instances in your VPC outbound connections over IPV6 while preventing the internet to initiate an IPv6
  connection to your instances
- You must update the Route Tables

### VPC Section Summary

- CIDR - IP Range
- VPC - Virtual Private Cloud => we define a list of IPv4 & IPv6 CIDR
- Subnets - tied to an AZ, we define a CIDR
- Internet Gateway - at the VPC level, provide IPv4 & IPv6 Internet Access
- Route Tables - must be edited to add routes from subnets to the IGW, IPV Peering Connections, VPC Endpoints...
- Bastion Host - public EC2 instance to SSH into, that has SSH connectivity to EC2 instances in private subnets
- NAT Instances - gives Internet access to EC2 instances in private subnets, Old, must be setup in a public subnet,
  disable Source/Destination check flag
- NAT Gateway - managed by AWS, provide scalable Internet access to private EC2 instances, IPv4 only
- NACL - stateless, subnet rules for inbound and outbound, don't forget Ephemeral Ports
- Security Groups - stateful, operate at the EC2 instance level
- VPC Peering - connect two VPCs with non overlapping CIDR, non-transitive
- VPC Endpoints provide private access to AWS services (S3, DynamoDB, CloudFormation, SSM) within a VPC
- VPC Flow Logs - can be setup at the VPC /Subnet/ENI level, for ACCEPT and REJECT traffic, helps identify attacks,
  analyze using Athena or CloudWatch Logs Insights
- Site-to-Site VPN - setup a Customer Gateway on DC, a Virtual Private Gateway on VPC, and site-to-site VPN over public
  Internet
- AWS VPN CloudHub - hub-and-spoke VPN model to connect your sites
- Direct Connect - setup a Virtual Private Gateway on VPC, and establish a direct private connection to an AWS Direct
  Connect Location
- Direct Connect Gateway - setup a Direct Connect to many VPCs in difference AWS regions
- AWS Private Link / VPC Endpoint Services
    - Connect services privately from your services VPC to customer VPC
    - Doesn't need VPC Peering, public internet, NAT Gateway, Route Tables
    - Must be used with Network Load Balancer & ENI
- ClassicLink - connect EC2-Classic EC2 instances privately to your VPC
- Transit Gateway - transitive peering connections for VPC, VPN & DX
- Traffic Mirroring - copy network traffic from ENIs for further analysis
- Egress-only Internet Gateway - like a NAT Gateway, but for IPv6

### Networking Costs in AWS

- Free to traffic in
- Free if using private IP in same AZ
- $0.01 if using private IP in different AZ
- $0.02 if using public IP/Elastic IP in different AZ
- $0.02 Inter-region
- Use private IP instead of Public IP for good savings and better network performance
- Use same AZ for maximum savings (at the cost of high availability)
- Minimizing egress traffic network cost
    - Egress traffic: outbound traffic (from AWS to outside)
    - Ingress traffic: inbound traffic - from outside to AWS (typically free)
    - Try to keep as much internet traffic within AWS to minimize costs
    - Direct Connect location that are co-located in the same AWS Region result in lower cost for egress network
- S3 Data Transferring Pricing - Analysis for USA
    - S3 ingress: free
    - S3 to internet: $0.09 per GB
    - S3 Transfer Acceleration:
        - Faster transfer time (50 to 500% better)
        - Additional cost on top of Data Transfer Pricing: +$0.04 to $0.08 GB
    - S3 to CloudFront: $0.00 per GB
        - CloudFront to Internet: $0.085 per GB
            - Caching capability (lower latency)
            - Reduce costs assoicated with S3 Requests Pricing (7x cheaper with CloudFront)
    - S3 Cross Region Replication: $0.02 perGB
- Pricing: NAT Gateway vs Gateway VPC Endpoint
    - $0.045 NAT Gateway / hour
    - $0.045 NAT Gateway data processed / GB
    - $0.09 Data transfer out to S3(cross-region)
    - $0.00 Data transfer out to S3(same-region)
    - No cost for using Gateway Endpoint, $0.01 Data transfer in/out (same-region)

### AWS Network Firewall

- Network protection on AWS
    - To protect network on AWS, we've seen
        - Network Access Control Lists (NACLs)
        - Amazon VPC Security Groups
        - AWS WAF (protect against malicious requests)
        - AWS Shield & AWS Shield Advanced
        - AWS Firewall Manager (to manage them across accounts)
- AWS Network Firewall
    - Protect your entire Amazon VPC
    - From Layer3 to Layer7 protection
    - Any direction, you can inspect
        - VPC to VPC traffic
        - Outbound to internet
        - Inbound from internet
        - To/From Direct Connect & Site-to-Site VPN
    - Internally, the AWS Network Firewall uses the AWS Gateway Load Balancer
    - Rules can be centrally managed cross-account by AWS Firewall Manager to apply to many VPCs
- Network Firewall - Fine Grained Controls
    - Support 1000s of rules
        - IP & port - example: 10000s if IPs filtering
        - Protocol - example: block the SMB protocol for outbound communications
        - Stateful domain list rule groups: only allow outbound traffic to *.mycorp.com or third-party software repo
        - General pattern matching using regex
    - Traffic filtering: Allow, drop, or alert for the traffic that matches the rules
    - Active flow inspection to protect against network threats with intrusion-prevention capabilities (like Gateway
      Load Balancer, but all managed by AWS)
    - Send logs of rule matched to Amazon S3, CloudWatch Logs, Kinesis Data Firehose
