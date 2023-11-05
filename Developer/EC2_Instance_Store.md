## EC2 instance storage

### EBS volume

- An Elastic Block store is a network drive you can attach to your instances while they run
- It allows your instances to persist data, even after their termination
- They can only mounted to one instance at a time (at the CCP level)
- Multiple-attache feature for some EBS
- Bound to a specific availability zone
- Have a provisioned capacity
- Delete on Termination attribute
    - Preserve root volume when instance is terminated
    - Control the EBS behaviour when an EC2 instance terminates
        - By default, the root EBS volume is deleted (attribute enabled)
        - By default, any other attached EBS volume is not deleted (attribute disabled)
    - This can be controlled by the AWS console / AWS CLI
    - USe case: preserve root volume when instance is terminated
- It's a network drive (i.e not a physical drive)
    - It uses the network to communicate the instance, which means there might be a bit of latency
    - It can be detached from an EC2 instance and attached to another one quickly
- It's locked to an Availability Zone (AZ)
    - An EBS Volume in us-east-1a cannot be attached to us-east-1b
    - To move a volume across, you first need to snapshot it
- Have a provisioned capacity (size in GBs, and IOPS)
    - You get billed for all the provisioned capacity
    - You can increase the capacity of the driver over time

### EBS Snapshots

- Make a backup(snapshot) of your EBS at a point in time
- Not necessary to detach volume to do snapshot, but recommended
- Can copy snapshots across AZ or Region
- Snapshots Features
    - EBS snapshot archive tier
        - Move a Snapshot to an "archive tier" that is 75% cheaper
        - Takes within 24 to 72 hours for restoring the archive
    - Recycle Bin for EBS Snapshots
        - Setup rule to retain deleted snapshots so you can recover them after an accidental deletion
        - Specify retention (1 day to 1 year)
    - Fast Snapshot Restore (FSR)
        - Force full initialization of snapshot to have no latency on the first use ($$$$)

### EBS Volume Types

- EBS Volumes come in 6 types
    - gp2 / gp3 (SSD): General puprose SSD volume that balances price and performace for a wide variety of workloads
    - io1 / io2 (SSD): Highest-performance SSD volume for mission-critical low-latency or high-throughput workloads
    - st1 (HDD): Low cors HDD volume desgined for frequently accessed, throughput-intensive workloads
    - sc1 (HDD): Lowest cost HDD volume designed for less frequently accessed workloads
- EBS Volumes are characterized in Size | Throughput | IOPS (I/O Opes Per Sec)
- When in doubt always consult the AWS documentation - it's good!
- **Only gp2/gp3 and io1/io2 can be used as boot volumes**

### EBS Volume Types Use cases

- General Purpose SSD
    - Cost effective storage, low-latency
    - System boot volumes, Virtual desktops, Development and test environments
    - 1 Gib - 16 TiB
    - gp2/gp3 (SSD 1GB - 16TB): General purpose SSD volume that balances price and performance for a wide variety of
      workloads
        - Cost effective storage, low-latency
        - gp3:
            - Can increase IOPS up to 16000 and throughput up to 1000MiB/s independently
            - Baseline of 3000 IOPS and throughput of 125 MiB/s
        - gp2:
            - Size of the volume and IOPS are linked, max IOPS is 16000
            - Small gps volumes can burst IOPS to 3000
            - 3 IOPS per GB, means at 5334 GB we are at the max IOPS
- Provisioned IOPS (PIOPS) SSD
    - Critical business applications with sustained IOPS performance
    - Or applications that need more than 16000 IOPS
    - Great for databases workloads (sentitive to storage perf and consistency)
    - io1/io2 (SSD 4GB-16TB): Highest-performance SSD volume for mission-critical low-latency or high-throughput
      workloads
        - More than 16000 IOPS
        - Great for database workloads
        - Can increase PIOPS independently from storage size
        - Max PIOPS: 64000 for Nitro EC2 instances & 32000 for other
        - io2 have more durability and more IOPS per GiB
        - io2 Block Express (4GB - 64TB)
            - Sub-millisecond latency
            - Max PIOPS: 256000 with an IOPS:GiB ration of 1000:1
        - Support EBS multi-attach
- Hard Disk Drives (HDD)
    - Can not be boot volume
    - 125 GiB to 16 TiB
    - st1 (HDD 125GB - 16TB): Low cost HDD volume designed for frequently accessed, throughput intensive workloads
        - Throughput Optimized HDD
        - Big data, data warehouses, Log processing
        - Max throughput 500 MiB/s - max IOPS 500
    - sc1 (HDD same as above): Lowest cost HDD volume designed for less frequently accessed workloads
        - Cold HDD
        - For data is infrequently accessed
        - Scenarios where lowest cost is important
        - Max throughput 250 MiB/s - max IOPS 250

### EBS Mull-Attach - io1/io2 family

- Attach the same EBS volume to multiple EC2 instances in same AZ
- Each instance has full read/write permission to the high-performance volume
- Use case:
    - Achieve higher application availability in clustered Linux applications (ex: Teradata)
    - Applications must manage concurrent write operations
- Up to 16 EC2 Instances at a time
- Must use a file system that's cluster-aware (not SFX, EXT4, etc...)

### EBS Encryption

- When create an encrypted EBS volume:
    - Data at rest is encrypted inside the volume
    - All the data in flight moving between the instance and the volume is encrypted
    - All snapshots are encrypted
- Encryption and decryption are handled transparently
- Encryption has a minimal impact on latency
- Copying an unencrypted snapshot allows encryption
- Snapshots of encrypted volumes are encrypted

### AMI

- AMI = Amazon Machine Image
- AMI are a customization of an EC2 instance
    - You add your own software, configuration, operating system, monitoring...
    - Faster boot / config uration time because all your software is pre-packaged
- AMI are built for a specific region (and can be copied across regions)
- You can launch EC2 instances from
    - A public AMI: AWS provided
    - Your own AMI: you make and maintain them yourself
    - An AWS marketplace AMI: an AMI someone else made (and potentially sells)
- AMI Process (from an EC2 instance)
    - Start an EC2 instance and customize it
    - Stop the instance (for data integrity)
    - Build an AMI - this will also create EBS snapshots
    - Launch instances form other AMIs

### EC2 instance store

- EBS volumes are network drives with good but "limited performance"
- If you need a high-performance hardware disk, use EC2 Instance Store
- High performance hardware disk
- Better I/O performance
- EC2 Instance Store lose their storage if they're stopped (ephemeral)
- Good for buffer/cache/scratch data/temporary content
- Risk of data loss if hardware fails
- Backup and Replication are client responsibility

### Amazon EFS - Elastic File System

- Managed NFS (Network file system) that can be mounted on many EC2
- EFS works with EC2 instances in multi-AZ
- Highly available, scalable, expensive (3x gp2), pay per use
- USe cases: content management, web serving, data sharing, Wordpress
- Compatible with Linux based AMI
- Use NFSv4.1 protocol
- Use security group to control access to EFS
- Encryption at rest using KMS
- POSIX file system (~Linux) that has a standard file API
- File system scales automatically, pay-per-use, no capacity planning
- Performance & Storage Classes
    - EFS Sale
        - 1000 of concurrent NFS clients, 10 GB+ /s throughput
        - Grow to Petabyte-scale network file system, automatically
    - Performance mode (set at EFS creation time)
        - General Purpose (default) - latency-sensitive use cases (web server, CMS, etc...)
        - Max I/O - higher latency, throughput, highly parallel (big data, media processing)
    - Throughput Mode
        - Bursting
            - Throughput scales with files system size
            - 1TB = 50 MiB/s + burst of up to 100MiB/s
        - Provisioned
            - Set your throughput regardless of storage size, ex: 1 GiB/s for 1 YB storage
        - Elastic
            - automatically scales throughput up or down based on your workloads
            - Up to 3GiB/s for reads and 1 GiB/s for writes
            - Used for unpredictable workloads
    - Storage Classes
        - Storage Tiers (lifecycle management feature - move file after N days)
            - Standard: for frequently accessed files
            - Infrequent access (EFS-IA): cost to retrieve files, lower price to store. Enable EFS-IA with a Lifecycle
              Policy
        - Availability and durability
            - Standard/Regional: Multi-AZ, great for prod
            - One Zone: One AZ, great for dev, backup enabled by default, compatible with IA (EFS One Zone-IA)
        - Over 90% in cost savings

### EBS vs EFS

- EBS
    - one instance (except multi-attach io1/io2)
    - are locked at the AZ level
    - gp2: IU increases if the disk size increases
    - io1: can increase IO independently
    - To migrate EBS volume across AZ
        - Take snapshot and restore to another AZ
        - Backups use IO and you shouldn't run them while your applicatiopn is handling a lot of traffic
    - Root EBS volumes get terminated by default if the EC2 terminated
- EFS
    - Mount 100s of instances across AZ
    - EFS share websites files (WordPress)
    - Only for Linux instances (POSIX)
    - EFS has a higher price point than EBS
    - Can leverage EFS-IA for cost savings 
