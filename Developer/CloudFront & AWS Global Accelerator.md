## CloudFront & AWS Global Accelerator

### AWS CloudFront

- CDN (Content Delivery Network)
- Improve read performance, content is cached at the edge
- Improve users experience
- 216 Point of Presence globally (edge locations)
- DDoS protection, integration with Shield, WAF
- CloudFront - Origins
    - S3 bucket
        - For distributing files and caching them at the edge
        - Enhanced seucrity with CloudFront Origin Access Control (OAC)
        - OAC is replacing Origin Access Identity (OAI)
        - CloudFront can be used as an ingress (to upload files to S3)
    - Customer Origin (HTTP)
        - Application Load Balancer
        - EC2 instance
        - S3 website (must first enable the bucket as a static S3 website)
        - Any HTTP backend you want
- CloudFront vs S3 Cross Region Replication
    - CloudFront:
        - Global Edge network
        - Files are cached for a TTL
        - Great for static content that must be available everywhere
    - S3 Cross Region Replication
        - Must be setup for each region you want replication to happen
        - Files are updated in near real-time
        - Read Only
        - Great for dynamic content that needs to be available at low-latency in few regions
- ALB or EC2 as an origin
    - EC2 instances or ALB must be public

### CloudFront Geo Restriction

- You can restrict who can access your distribution
    - Allowlist: Allow your users to access your content only if they're in one of the countries on a list of
      approved countries
    - Blocklist: Prevent your users from accessing your content if they're in one of the countries on a list of
      banned countries
- The country is determined using a 3rd party Geo-IP database
- Use case: Copyright Laws to control access to content

### CloudFront Caching

- The cache lives at each CloudFront Edge Location
- CloudFront identifies each object in the cache using the Cache Key
- You want to maximize the Cache Hit ration to minimize requests to the origin
- You can invalidate part of the cache using the CreateInvalidation API
- What is CloudFront Cache Key?
    - A unique identifier for every object in the cache
    - By default, consists of hostname + resource portion of the URL
    - If you have an application that serves up content that varies based on user, device, language, location...
    - You can add other elements (HTTP headers, cookies, query strings) to the Cache Key using CloudFront Cache Policies

### CloudFront Policies - Cache Policy

- Cache based on:
    - HTTP Headers: None - Whitelist
    - Cookies: None - Whitelist - Include All-Except - All
    - Query Strings None - Whitelist - Include All-Except - All
- Control the TTL (0 seconds to 1 year), can be set by the origin using the Cache-Control header, Expires header...
- Create your own policy or use Predefined Managed Policies
- All HTTP headers, cookies, and query strings that you include in the Cache Key are automatically included in origin
  requests
- HTTP Headers
    - None:
        - Don't include any headers in the Cache Key (except default)
        - Headers are not forwarded (except default)
        - Best caching performance
    - Whitelist:
        - only specified headers included in the Cache Key
        - Specified headers are also forwarded to Origin
- Query Strings
    - None
        - Don't include any query strings in the Cache Key
        - Query strings are not forwarded
    - Whitelist
        - Only specified query strings included in the Cache Key
        - Only specified query strings are forwarded
    - Include All - Except
        - Include all query strings in the Cache Key except the specified list
        - All query strings are forwarded except the specifief list
    - All
        - Include all query strings in the Cache Key
        - All query strings are forwarded
        - Worst caching performance

### CloudFront Policies - Origin Request Policy

- Specify values that you want to include in origin requests without including them in the Cache Key (no duplicated
  cached content)
- You can include:
    - HTTP headers: None - Whitelist - All viewer headers options
    - Cookies: None - Whitelist - All
    - Query Strings: None - Whitelist - All
- Ability to add CloudFront HTTP headers and Custom Headers to an origin request that were not included in the viewer
  requests
- Create your own policy or use Predefined Managed Policies

### CloudFront - Cache Invalidations

- In case you update the back-end origin, CloudFront doesn't know about it and will only get the refreshed content after
  the TTL has expired
- However you can force an entire or partial cache refresh (thus bypassing the TTL) by performing a CloudFront
  Invalidation
- You can invalidate all files (*) or a special path (/images/*)

### CloudFront - Cache Behaviors

- Configure different settings for a given URL path pattern
- Example: one specific cache behavior to images/*.jpg files on your origin web server
- Route to different kind of origins/origin groups based on the content type or pattern
    - /images/*
    - /api/*
    - /* (default cache behavior)
- When adding additional Cache Behaviors, the Default Cache Behaviour is always the last to be processed and is
  always /*
- Example: Sing In Page
    - /login -> EC Instance -> Generate Signed Cookies
    - /* -> With Signed Cookies -> S3 Buckets
- Example: Maximize cache hits by separating static and dynamic distributions
    - Dynamic -> Cache based on correct headers and cookie -> Dynamic Content (REST, HTTP server): ALB + EC2
    - Static requests: No headers/ session caching rules, Required for maximizing cache hits -> Static content -> S3

### CloudFront - ALB or EC2 as an origin

- EC2 Instances Must be public
    - Allow Public IP of Edge Locations
- Application Load Balancer Must be Public
    - EC2 Instances Can be private, allow security group of ALB
    - Allow Public IP of Edge Locations

### CloudFront Signed URL / Cookies

- You want to distribute paid shared content to premium users over the world
- We can use CloudFront Signed URL / Cookie. WE attach a policy with:
    - Include URL expiration
    - Include IP ranges to access the data from
    - Trusted signers (which AWS accounts can create signed URLs)
- How long should the URL be valid for?
    - Shared content (movie, music): make it short (a few minutes)
    - Private content (private to the user): you can make it last for years
- Signed URL = access to individual files (one signed URL per file)
- Signed Cookies = access to multiple files (one signed cookie for many files)
- CloudFont Signed URL vs S3 Pre-Signed URL
    - CloudFront Signed URL:
        - Allow access to a path, no matter the origin
        - Account wide key-pair, only the root can manage it
        - Can filter by IP, path, date, expiration
        - Can leverage caching features
    - S3 Pre-Signed URL:
        - Issue a request as the person who pre-signed the URL
        - USes the IAM key of the signing IAM principal
        - Limited lifetime
- CloudFront Signed URL Process
    - Two types of signers:
        - Either a trusted key group (recommended)
            - Can leverage APIs to create and rotate keys (and IAM for API security)
        - An AWS Account that contains a CloudFront Key Pair
            - Need to manage keys using the root account and the AWS console
            - Not recommended because you shouldn't use the root account for this
    - In your CloudFront distribution, create one or more trusted key groups
    - You generate your own public / private key
        - The private is used by your applications (e.g. EC2) to sign URLs
        - The public key (uploaded) is used by CloudFront to verify URLs

### CloudFront - Price Classes

- CloudFront Edge locations are all around the world
- The cost of data out per edge location varies
- You can reduce the number of edge locations for cost reduction
- Three price classes:
    - Price Class: All regions - best performance
    - Price Class 200: most regions, but excludes the most expensive regions
    - Price Class 100: only the least expensive regions

### CloudFront - Multiple Origin

- To route a different kind of origins based on the content type
- Based on path pattern:
    - /images/*
    - /api/*
    - /*

### CloudFront - Origin Groups

- To increase high-availability and do failover
- Origin Groups: one primary and one secondary origin
- If the primary origin fails, the second one is used

### CloudFront - Field Level Encryption

- Protect user sensitive information through application stack
- Adds and additional layer for security along with HTTPS
- Sensitive infomation encrypted at the edge close to user
- Use asymmetric encryption
- Usage:
    - Specify set of fields of POST reuqests that you want to be encrypted (up to 10 fields)
    - Specify the public key to encrypt them

### CloudFront - Real Time Logs

- Get real-time requests received by CloudFront sent to Kinesis Data Streams
- Monitor, analyze, and take actions based on content delivery performance
- Allows you to choose:
    - Sampling Rate - percentage of request for which you want to receive
    - Specific fields and specific Cache Behaviors (path patterns)
- Real-time Processing
    - Users -> CloudFront ->  Logs -> Kinesis Data Streams -> Lambda
- Near Real-time Processing
    - Users -> CloudFront -> Logs -> Kinesis Data Streams -> Kinesis Data Firehose

### AWS Global Accelerator

- Global users for our application
    - You have deployed an application and have global users who want to access it directly
    - They go over the public internet, which can add a lot of latency due to many hops
    - We wish to go as fast as possible through AWS network to minimize latency
- Unicast IP: one server holds one IP address
- Anycast IP: all servers hold the same IP address and the client is routed to the nearest one
- AWS Global Accelerator
    - Leverage the AWS internal network to route to your application
    - 2 Anycast IP are created for your application
    - The Anycast IP send traffic directly to Edge Locations
    - The Edge locations sent the traffic to your application
    - Works with Elastic IP, EC2 instances, ALB, NLB, public or private
    - Consistent Performance
        - Intelligent routing to lowest latency and fast regional failover
        - No issue with client cache (because the IP doesn't change)
        - Internal AWS network
    - Health Checks
        - Global Accelerator performs a health check of your applications
        - Help make your application global (failover less than 1 minute for unhealthy)
        - Great for disaster recovery
    - Security
        - Only 2 external IP need to be whitelisted
        - DDos protection thanks to AWS Shield
- AWS Global Accelerator vs CloudFront
    - They both use the AWS global network and its edge locations around the world
    - Both services integrate with AWS Shield for DDoS protection
    - CloudFront
        - Improves performance for both cachable content (such as images and videos)
        - Dynamic content (such as API acceleration and dynamic site delivery)
        - Content is served at the edge
    - Global Accelerator
        - Improve performance for a wide range of applications over TCP or UDP
        - Proxying packets at the edge to applications running in one or more AWS Regions
        - Good fir for non-HTTP use cases, such as gaming(UDP), IoT(MQTT) or Voice over IP
        - Good for HTTP use cases that required static IP addresses
        - Good for HTTP use cases that required deterministic, fast regional failover
