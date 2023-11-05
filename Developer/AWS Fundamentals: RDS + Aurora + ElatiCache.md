## AWS Fundamental: RDS + Aurora + ElasticCache

### Amazon RDS Overview

- RDS stands for Relational Database Service
- It's a managed DB service for DB use SQL as query language
- It allows you to create databases in the cloud that managed by AWS
    - Postgres
    - MySQL
    - MariaDB
    - Oracle
    - Microsoft SQL Sever
    - Aurora (AWS Proprietary database)

### Advantage over using RDS versus deploying DB on EC2

- RDS is a managed service
    - Automated provisioning, OS patching
    - Continuous backups and restore to specific timestamp (Point in time restore)
    - Monitoring dashboards
    - Read replicas for improved read performance
    - Multi AZ setup for DR (Disaster Recovery)
    - Maintenance windows for upgrades
    - Scaling capability (vertical and horizontal)
    - Storage backend by EBS (gp2 or io1)
- But you can't SSH into your instances

### RDS - Storage Auto Scaling

- Increase your storage on your RDS DB instance dynamically
- When RDS detects you are running out of free database storage, it scales automatically
- Avoid manually scaling your database storage
- You have to set the Maximum Storage Threshold （maximum limit for DB storage)
- Automatically modify storage if:
    - Free storage is less than 10% of allocated storage
    - Low-storage lasts at least 5 minutes
    - 6 hours have passed since last modification
- Useful for applications with unpredictable workloads
- Supports all RDS database engines (MariaDB, MySQL, PostgreSQL, SQLServer, Oracle)

### RDS Read Replicas for read scalability

- Up to 15 read replicas
- Within AZ, Cross AZ or Cross Region
- Replications are ASYNC, so reads are eventually consistent
- Replicas can be promoted to their own DB
- Application must update the connection string to leverage read replicas
- Use cases
    - You have a production database that is taking on nrmal load
    - You want to run a reporting application to run some analytics
    - You create a Read Replica to run the new workload there
    - The production application is unaffected
    - Read replicas are used for SELECT(=read) only kind of statements (not INSERT, UPDATE, DELETE)
- Network cost
    - In AWS there's a network cost when data goes from one AZ to another
    - For RDS read replicas with the same region, you don't pay for the fee

### RDS Multi AZ (Disaster Recovery)

- SYNC replication
- One DNS name - automatic app failover to standby
- Increase availability
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps
- Not used for scaling
- Note: The Read Replicas be setup as Multi AZ for Disaster Recovery (DR)
- RDS - From Single AZ to Multi-AZ
    - Zero downtime operation
    - Just click on 'modify' for the database
    - The following happens internally:
        - A snapshot is taken
        - A new DB is restored from the snapshot in a new AZ
        - Synchronization is established between two databases

### RDS Custom

- Managed Oracle and Microsoft SQL Server Database with OS and database customization
- RDS: Automated setup, operation and scaling of database in AWS
- Custom: access to the underlying database and OS, so you can:
    - Configure settings
    - Install patches
    - Enable native features
    - Access the underlying EC2 instance using SSH or SSH Session Manager
- De-active Automation Mode to perform your customization, better to take a DB snapshot before
- RDS vs RDS Custom:
    - RDS: entire database and the OS to be managed by AWS
    - RDS custom: full admin access to the underlying OS and the database

### Amazon Aurora

- Aurora is a proprietary technology from AWS (not open-sourced)
- Postgres and MySQL are both supported as Aurora DB (that means your drivers will work as if Aurora was a Postgres or
  MySQL database)
- Aurora is "AWS cloud optimized" and claims 5x percentage improvement over MySQL on RDS, over 3x the performance of
  Postgres on RDS
- Aurora storage automatically grows in increments of 10 GB, up to 128TB
- Aurora can have up to 15 replicas, and the replication process is faster than MySQL (sub 10 ms replica lag)
- Failover in Aurora is instantaneous. It's High Availability native
- Aurora coast more than RDS (20% more) - but it's more efficient

### Aurora High availability and Read Scaling

- 6 copies of your data across 3 AZ:
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 needed for reads
    - Self healing with peer-to-peer replication
    - Storage is striped across 100s of volumes
- One Aurora Instance takes write (master)
- Automated failover for master in less than 30 seconds
- Master + up to 15 Aurora Read Replicas server reads
- Support for Cross Region Replication
- Aurora DB Cluster
    - Writer Endpoint
        - Pointing to master
    - Reader Endpoint
        - Connection Load Balancing

### Features of Aurora

- Automatic fail-over
- Backup and Recovery
- Isolation and security
- Industry compliance
- Push-button compliance
- Automated Patching with Zero Downtime
- Advanced Monitoring
- Routine Maintenance
- Backtrack: restore data at any point of time without using backups

### Aurora Advanced Concept

- Aurora Replicas - Auto Scaling
- Aurora - Custom Endpoints
    - Define a subset of Aurora Instances as a Custom Endpoint
    - Example: Run analytical queries on specific replicas
    - The Reader Endpoint is generally not used after defining Custom Endpoints
- Aurora Serverless
    - Automated database instantiation and auto-scaling based on actual usage
    - Good for infrequent intermittent or unpredictable workloads
    - No capacity planning needed
    - Pay per second, can be more cost-effective
- Aurora Multi-Master
    - In case you want continuous write availability for the writer nodes
    - Every node does R/W - vs promoting a Read Replica as the new master
- Global Aurora
    - Aurora Cross Region Read Replicas
        - Useful for disaster recovery
        - Simple to put in place
    - Aurora Global Database (recommended)
        - 1 Primary Region (read/write)
        - Up to 5 secondary (read-only) regions, replication lag is less than 1 second
        - Up to 16 Read Replicas per secondary region
        - Helps for decreasing latency
        - Promoting another region (for disaster recovery) has an RTO of < 1 minute
        - Typical cross-region replication takes less than 1 second
    - Aurora Machine Learning
        - Enables you to add ML-based predications to your applications via SQL
        - Simple, optimized and secure integration between Aurora and AWS ML services
        - Supported services
            - Amazon SageMaker
            - Amazon Comprehend
        - Use cases: fraud detection, ads targeting, sentiment analysis, product recommendations

### RDS & Aurora - Backup and Monitoring

- RDS Backups
    - Automated backups
        - Daily full backup of the database (during the backup window)
        - Transaction logs are backed-up by RDS every 5 minutes
        - ability to restore to any point in time (from oldest backup to 5 minutes ago)
        - 1 to 35 days retention, set 0 to disable automated backups
    - Manual DB snapshots
        - Manually triggered by the user
        - Retention of backup for as long as you want
    - Trick: in a stopped RDS database, you will still pay for storage.
      If you plan on stopping it for a long time, you
      should snapshot&restore instead
- Aurora Backups
    - Automated backups
        - 1 to 35 days (can not be disabled)
        - point-int-time recovery in that timeframe
    - Manual DB Snapshots
        - Manually triggered by the user
        - Retention of backup for as long as you want
- RDS & Aurora Restore options
    - Restoring a RDS/Aurora backup or a snapshot create a new database
    - Restoring MySQL RDS database from S3
        - Create a back-up from an on-premises database
        - Store it on Amazon S3 (object storage)
        - Restore the backup file onto a new RDS instance running MySQL
    - Restoring MySQL Aurora cluster from S3
        - Create a backup from your on-premises database using Percona XtraBackup
        - Store the backup file on Amazon S3
        - Restore the backup file onto a new Aurora cluster running MySQL
- Aurora Database Cloning
    - Create a new Aurora DB cluster from an existing one
    - Faster than snapshot & restore
    - Uses copy-on-write protocol
        - The new DB cluster uses the same data volume as the original DB cluster (fast and efficient - no copying is
          needed)
        - When updated are made to the new DB cluster data, the additional storage is allocated and data is copied to be
          separated
    - Very fast and cost-effective
    - Useful to create a "staging" database from a "production" database without impacting the production database

### RDS & Aurora Security

- At-rest encryption:
    - Database master & replicas encryption using AWS KMS - must be defined at launch time
    - If the master is not encrypted, the read replicas cannot be encrypted
    - To encrypt an unencrypted database, go through a DB snapshot & restore as encrypted
- In-fligt encryption: TLS-ready by default, use the AWS TLS root certificates client side
- IAM Authentication: IAM roles to connect to your database (instead of username/pw)
    - MySQL
    - MariaDB
    - PostgreSQL
- Security Groups: Control Network access to your RDS/Aurora DB
- No SSH available except on RDS custom
- Audit logs can be enabled and sent to AWS CloudWatch Logs for longer retention

### Amazon RDS Proxy

- Fully Managed database proxy for RDS
- Allows apps to pool and shared DB connections established with the database
- Improving database efficient by reducing the stress on database resources and minimizing open connections (and time
  outs)
- Serverless, autoscaling, highly available (mutli-AZ)
- Reduced RDS & Aurora failover time by up 66%
- Supports RDS(MySQL, PostgresSQL, MariaDB,MS SQL Server) and Aurora (MySQL,PostgresSQL)
- No code changes required for most apps
- Enforce IAM Authentication for DB, and securely store credentials in AWS Secret Manager
- RDS Proxy is never publicly accessible (must be accessed from VPC)

### Amazon ElastiCache

- Overview
    - Get managed Redis or Memcached
    - Caches are in-memory databases with really high performance, low latency
    - Helps reduce load off to databases for read intensive workloads
    - Helps make your application stateless
    - AWS takes care of OS maintenance / patching, optimization, setup, configuration, monitoring, failure recovery and
      backups
    - Using ElastiCache involves heavy application code changes
- SA - DB Cache
    - Applications queries ElastiCache, if not available, get from RDS and store in ElastcCache
    - Helps relieve load in RDS
    - Cache must have an invalidation strategy to make sure only the most current data is used in there≈
- SA - User Session Store
    - Users logs into any of the application
    - The application writes the session data into ElastiCache
    - The user hits another instance of our application
    - The instance retrieves the data and the user is alraedy logged in
- Redis vs Memcached
    - Redis
        - Multi AZ with Auto-Failover
        - Up to 5 read replicas to scale reads and have high availability
        - Data durability using AOF persistence
        - Backup and restore features
        - Supports Sets and Sorted Sets
    - Memcached
        - Multi-node for partitioning of data (sharding)
        - No high availability (replication)
        - Non persistent
        - No backup and restore
        - Multi-threaded architecture

### ElastiCache - Cache Security

- ElastiCache supports IAM Authentication for Redis
- IAM polices on ElastiCache are only used for AWS API-level security
- Redis Auth
    - password/token for Redis cluster
    - Extra level of security for your cache (on top of security group)
    - Support SSL in flight encryption
- Memcached
    - Supports SASL-based authentication (advanced)

### Patterns for ELasitCache

- Lazy Loading: all the read data is cached, data can become stale in cache
- Write Through: Adds or update data in the cache when written in DB (no stale data)
- Session Store: store temporary session data in cache (using TTL features)
- Redis use case:
    - Gaming Leaderboards are computationally complex
    - Redis Sorted sets guarantee both uniqueness and element ordering
    - Each time a new element added, it's randked in real time, then added in correct order

### ElasticCache Strategies

- Caching Implementation Considerations
    - Is it safe to cache data? Data may be out of date, eventually consistent
    - Is caching effective for that data?
        - Pattern: data changing slowly, few keys are frequently needed
        - Anti patterns: data changing rapidly, all large key space frequently needed
    - Is data structured well for caching?
        - example: key value caching, or caching of aggregations results?
    - Which caching design pattern is the most appropriate?
- Lazy Loading / Cache-Aside / Lazy Population
    - Pros:
        - Only requested data is cached (the cache isn't filled up with unused data)
        - Node failures are not fatal (just increased latency to warm the cache)
    - Cons:
        - Cache miss penalty that results in 3 round trips, noticeable delay for that request
        - Stale data: data can be updated in the database and outdated in the cache
- Write Through - Add or Update cache when database is updated
    - Pros:
        - Data in cached is never stale, reads are quick
        - Write penalty vs Read penalty (each write requires 2 calls)
    - Cons:
        - Missing Data until it is added / updated in the DB. Mitigation is to implement Lazy Loading strategy as well
        - Cache churn - a lot of the data will never de read
    - Cache Evictions and Time-to-live (TTL)
        - Cache eviction can occur in three ways:
            - You delete the item explicitly in the cache
            - Item is evicted because the memory is full and not recently used (LRU)
            - You set an item time-to-live (or TTL)
        - TTL are helpful for any kind of data
            - Leaderboards
            - Comments
            - Activity streams
        - TTL can range from few seconds to hours or days
        - If too many evictions happen due to memory, you should scale up or out
  - Final words of wisdom
    - Lazy Loading / Cache aside is easy to implement and works for many situations as a foundation, especially on the read side
    - Write-through is usually combined with Lazy Loading as targeted for the queries or workloads that benefit from this optimization
    - Setting a TTL is usually not a bad idea, except when you are using Write-through. Set it to a sensible value for your application
    - Only cache the data that makes sense (user profiles, blogs, etc...)
    - Quote: There are only two hard things in Computer Science: cache invalidating and naming things

### AMazin MemoryDB for Redis

- Redis-compatible, durable, in-memory database service
- Ultra-fast performance with over 160 millions requests/second
- Durable in-memory data storage with Multi-AZ transactional log
- Scale seamlessly from 10s GBs to 100s TBs of storage
- Use cases: web and mobile apps, online gaming, media streaming...

### Classic ports

- PostgreSQL: 5432
- MySQL: 3306
- Oracle RDS: 1521
- MSSQL Server: 1433
- MariaDB: 3306
- Aurora: 5432/3306
