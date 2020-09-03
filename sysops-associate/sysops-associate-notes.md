# SysOps Administrator Associate

![SOAA badge](../media/soaa-badge.png)

Training course notes
These notes are incremental to AWS Certified Developer Associate and Solutions Architect Associate

# Whitepapers



# CloudWatch
- By default EC2 monitoring is 5m, detailed - 1m, but it depends on service
- By default store indefinitely (can change the retention period)
- Host-level metrics: CPU, Network, Disk, StatusCheck
- _Minimum granularity_ for custom metric - 1m

Free metrics:
- RDS, EC2, EBS

- **Alarm** can trigger:
    SNS (multiple SNS topics are possible)  
    Auto scaling action (ASG or ECS)  
    EC2 action (recover, stop, terminate, reboot) - _Per-instance_ metric is required  
- **Rule** can trigger:  
    SNS, SQS, EC2, ECS Task, Lambda, CodeBuild, CodePipeline, Step Function, SSM, Eventbus in other account, Kinesis Data Streams and Firehose  
    Event Source for the rule can be almost any AWS Service (Event type: CloudTrail API Call)

CloudWatch Events:
- consists of three parts:
  - **Event Source**  
  - **Rule**  
  - **Target**: can be more than one  

- Retention:
  - 1m metric - 15d
  - 5m metric - 63d
  - 1h metric - 455d

- Metric resolution:
  - Default: 5m
  - Detailed: 1m
  - High-resolution: 10 or

- Can be used on-prem: Need to install SSM agent and CloudWatch agent

Create EC2 custom metric [LAB](labs/custom-cloudwatch-metric/script.sh):
 - create a EC2 role with CloudWatch access policy
 - send a custom metric to CloudWatch from EC2  
    helper scripts: `curl https://aws-cloudwatch.s3.amazonaws.com/downloads/CloudWatchMonitoringScripts-1.2.2.zip -O`

## Monitoring EBS
- EBS initialisation for
- 5m at no charge
- io1 (provisioned IOPS) send 1m metric automatically
- CloudWatch Metrics:
  - `VolumeRead/Write Bytes`  
  - `VolumeRead/Write Ops`  
  - `VolumneTotalRead/Write Time`  
  - `VolumeIdleTime`: if high, consider using snapshot instead of volume  
  - `VolumeQueueLength`:: if high, consider increasing IOPS  
- Provisioned IOPS (SSD) only
  - `VolumeThroughputPercentage`  
  - `VolumeConsumedReadWriteOps`  
- gp2, st1, sc1 only:
  - `BurstBalance`

- Status Checks, runs every 5m:
|Status|I/O Performance Status|
|---|---|
|ok |normal|
|warning|Degrated or Severely Degraded|
|impaired|Stalled or Not Available|
|insufficient data|insufficient data|

## EBS Volume Initialisation
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-initialize.html

New EBS volumes are at maximum performance immedeately
Volumes restored from snapshots:
- max performance is not reached until all blocks on the volume are read
- must be initialised - reading all blocks

```
lsblk
sudo dd if=/dev/xvdf of=/dev/null bs=1M
```
or
```
sudo yum install -y fio
sudo fio --filename=/dev/xvdf \
  --rw=read \
  --bs-128k \
  --iodepth=32 \
  --ioengine=libaio \
  --direct=1 \
  --name=volume-initialise
```

## Monitoring ELB
### ALB
**CloudWatch**: metrics to monitor:
  - `ActiveConnectionCount`  
  - `HealthyHostCount`  
  - `HTTP code totals`  

**Access logs**: disabled by default, can store data where EC2 instance has been deleted. Detailed information for every request received by ELB, including client IP and server responses. Stored in S3  
**Request tracing**: _for ALB only_. Track HTTP requests from clients to targets or other services. ALB adds `X-Amzn-Trace-Id` header that includes a trace ID  
**CloudTrail**: calls made to ELB API

### NLB
**CloudWatch**:
  - `ActiveFlowCount`, `HealthyHostCount`, `UnhealthyHostCount`  
**VPC Flow Logs**: detailed log of traffic to and from NLB
**CloudTrail**:  records API activity

## ALB components
- Load Balancer
- Listeners
  read the request from the clients
  compare the request to rules and forwards to a target group
- Target Group
  Targets can be:
  - Instance
  - IP (including outside of VPC, e.g. on-prem), supports dynamic port mapping (for microservices)
  - Lambda
  Health checks are configured per target group
  Targets can be in multiple target
ALB content-based routing:
- **Path-based**: forwards based on URL in the request
- **Host-based**: forwards based on the host field of HTTP header

## NLB
- Each AZ assigned gets a node created in it with static IP (or EIP) - reduces latency
- register targets by:
  - InstanceID: source addresses of clients are preserved
  - IP Address: source addresses of clients are the private IP of NLB node (NLB will re-write the headers)

## SSL Offloading
https://aws.amazon.com/blogs/aws/new-application-load-balancer-sni/#

Terminate HTTPS on ELB

## Network Bottlenecks
Potential networking issues:
- EC2 instances:
  - instances in different AZ, regions or continents
  - EC2 instances sizes (larger have better network performance)
  - not using enhanced networking features
  - you can check network performance with `iperf3`
- VPCs can use VPC peering to create a reliable connection:
  - no single point of failure
  - peer VPCs between regions to avoid traffic transiting the public internet

Bandwidth limitations on your VPN to AWS VPC
Use AWS

## CloudFront


## Monitoring Elasticache
Memcached: https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/CacheMetrics.WhichShouldIMonitor.html  
Redis: https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheMetrics.html  

Node types:
- General purpose
- Memory optimized

Metrics to watch:
`CPU Utilisation`: see below
`Swap Usage`: amount of Swap file that is used, should be close to 0    
`Evictions`: Memcached: Either Scale up or out. Redis: Only Scale Out  
`Concurrent Connections`: set alarm on that metric

Memcached CPU Utilisation:
 - Multithreaded  
 - Can handle load up to 90%, >90% __add more nodes to the cluster__  
 - `SwapUsage` should be around 0 and not > than 50Mb. Increase `memcached_connection_overhead` parameter  

Redis CPU Utilisation:
 - Not multithreaded  
 - To determine the point in which to scale, divide 90% by the number of cores  
 - No `SwapUsage` metric, instead use `reserved-memory`  

## CloudWatch Dashboard
Dashboards are cross-region (international), widget added on the region basis

# AWS Organisations
https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html

Manage multiple AWS accounts, can create groups of accounts and apply policies to the groups
- Central management of accounts
- Control Access with SCP (allow or deny individual AWS services on account group basis). Attach policies to the OU (applies to all accounts within OU). Can select _black_ or _white_ listing for SCP
- Automate AWS account creation
- Consolidated billing

## Service Control Policy (SCP)
similar to IAM permissions policies but SCP don't grant any permissions
- **Allow list strategy**: explicitly specify the access that is allowed. All other access is implicitly blocked.
  By default, AWS Organisations attaches an AWS managed policy `FullAWSAccess` to all roots, OUs and accounts.
  When you restrict permissions, you _replace_ the `FullAWSAccess`
  You can't add permissions back at lower lever in the hierarchy
- **Deny list strategy**: explicitly specify the access that is not allowed. All other access is allowed.
  This is the default behavior of AWS Organisations.
  You leave the default `FullAWSAccess` policy in place and attach additional policies that explicitly _deny_ access to unwanted services and actions

# Tagging and Resource Groups
- Sometimes can be inherited
- You can group resources that shares one or more tags
- Region based
- Create resource group:
  - select group type (Tag- or CloudFormation stack-based)
  - select resource type
  - select Tags to group upon
- You can execute automation on the resource group via **Systems Manager**

# AWS Cost Explorer & Cost Allocation Tags
Can generate cost allocation report (need to activate cost allocation tags)
Display data from the last 13 months, projected for the next 3 months

# Cost Optimisation
- EC2 Reserved Instances
- Spot Instances
- Low Utilisation: set CloudWatch alarms to terminate underutilized instances
- Unused ELBs
- EBS Volumes (delete unused, take snapshot. Storing snapshot is cheaper)
- Elastic IPs
- Idle RDS Instances

AWS Trusted Advisor

# AWS Cost & Usage Report
cost details for all resources in AWS
customized reports
Multiple files with flexible column structure + manifest file listing data files in report

# EC2 Pricing Refresher
On Demand
Reserved:
  - standard RIs
  - convertible RIs
  - scheduled RIs
  - you can also purchase reserved capacity for **RDS and ElastiCache** instances
Spot
Dedicated Hosts

# AWS Config
AWS resource inventory, configuration history and configuration change notification
Region-based
Stores all in S3 Bucket
Snapshot of current configuration of your account
Can stream configuration changes to SNS
Can trigger Lambda on Events
Can enable rule compliance by monitoring and triggering SNS notification
Compliance checks - periodic or triggered
Managed Rules available (AWS Config Rules)
You can see the timeline of changes and retrieve past configurations
AWS Config requires IAM role with permissions:
 - RO to recorded resources
 - Write to S3 logging bucket
 - Publish access to SNS


# CloudWatch vs Cloudtrail vs Config

CloudWatch|CloudTrail|Config
---|---|---
Monitors performance|Monitors API calls|Records the state of resources and notify about changes
e.g. what was the CPU utilisation 3 weeks ago|e.g. who provisioned SG 3 weeks ago|e.g. what were the rules on my SG 3 weeks ago

# Health Dashboards
 - Service Health Dashboard: https://status.aws.amazon.com/
 - Personal Health Dashboard (global by default), personalised view of performance and availability of AWS services for your AWS resources: https://phd.aws.amazon.com

# AWS API Access
All interactions with AWS API signed with `AccessKeyId` and `SecretAccessKey` using Sig4
`AccessKeyId` and `SecretAccessKey` map to users or roles in IAM

The _default credential provider chain_ implemented by `DefaultAWSCredentialsProviderChain` looks for credentials in this order:
  1. Specified in the code  
  2. Environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`  
  3. Default credential profile in the `~/.aws/credentials` file  
  4. Amazon ECS container credentials: loaded from ECS if environment variable `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI` is set  
  5. Amazon EC2 instance role: used on EC2 instance and delivered via EC2 metadata service  

## SSH agent forwarding
```
  ssh-add keyfile.pem
  ssh -A ec2-user@<bastion_host_ip>

  (on the bastion host)$ ssh ec2-user@<private_host_ip>
```

# EC2 Launch Issues
- `InstanceLimitExceeded`: per region
- `InsufficientInstanceCapacity`: AWS not enough available On-Demand capacity
Options to resolve: wait, request fewer instances, change instances type, purchase RIs, submit request without AZ

# EC2 Status Checks
System Status Checks
- monitors the systems on which your instances run
- Amazon must solve
- how to resolve:
  - for **EBS-backed** instances: start/stop to obtain new hardware  
  - for **instance-store-backed**: terminate and replace (can't recover the data)

Instance Status Checks
- you must intervene to fix

`aws ec2 describe-instances-status [--instance-ids i-xxxxxx]`  
`aws ec2 describe-instances-status --filters Name=instance-status.status,Values=impaired`

## AMI Virtualisation Types:
- **Hardware Virtual Machine (HVM)** AMIs: faster access to host hardware, enhanced networking and GPU processing  
- **Paravirtual (PV)** AMIs: Runs on hardware that does not support Virtualisation, no hardware extension support  

# EBS Volumes
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html

- max 16TB
- mounted only to **one** instance at a time
- mulitple volumes can be mounted to a single instance
- in one AZ, replicated within one AZ

Performance:
gp2:
- size 1GB to 16TB
- 100 to 16000 IOPS
- 3 IOPS per 1GB, min 100 IOPS
- max throughput 250 MB/s
- burst up to 3000 IOPS for 30m (never deplete for > 1000GB)

io1:
- size 4GB to 16TB
- 100 to 64000 IOPS
- max throughput 1000 MB/s
- AWS SLA = 99.9 of time within 10% of IOPS
- max ratio 50:1
  - 640GB or greater can use max IOPS
- AWS recommends a ratio larger than 2:1 with io1
  - if 4000 IOPS are needed, volume size should be less than 2000GB

## How to preserve data on EBS volume
- uncheck "Delete on Termination"
- create a snapshot before deletion
- create a separate volume and attach to the instance (attached volumes are not deleted when instance is terminated)


# Instance store
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html

You can specify instance store volumes for an instance only when you launch it. You can't detach an instance store volume from one instance and attach it to a different instance.

The data in an instance store persists only during the lifetime of its associated instance. If an instance reboots (intentionally or unintentionally), **data in the instance store persists**. However, data in the instance store is lost under any of the following circumstances:
- The underlying disk drive fails
- The instance stops
- The instance terminates

# EFS
- Multiple AZs
- Storage classes:
  - Infrequent Access
  - Standard
- can apply lifecycle policies
- throughput for parallel workloads
- for on-prem servers, use AWS DX or VPN
- Linux only
- 2 Performance modes (should be selected at creation time):
  - **General Purpose**: ideal for latency-sensitive use cases
  - **Max I/O**: high level of aggregate throughput, but higher latency. Use for highly parallelized apps
  - monitor `PercentIOLimit`
- 2 throughput modes:
  - **Bursting**: scales as size grows
  - **Provisioned throughput**: independent of the amount of data
  - < 1TB - burst to 100MB/s
  - > 1TB - burst to 100MB/s per 1TB of data stored

# EFS Monitoring
https://docs.aws.amazon.com/efs/latest/ug/monitoring-cloudwatch.html

- metric sent at 1m intervals and retained for 15months

## Amazon Lightsail
- VPS (Virtual Private Server)
- Singel instance
- One-click application deployments:
  - WordPress, Magento, Drupal, Joomla!, Redmine, Piesk
- Developement stacks:
  - Node.js, GitLab, LAMP, MEAN, Nginx

## AWS Batch
Runs batch computing workloads at scale

# S3 cross-region replication
- Bucket-level
- versioning must be enabled
- can select key name prefixes (folders)
- can select different storage class for destination
- what is not replicated:
  - objects that existed before replication was activated
  - encrypted objects:
    - SSE-C and SSE-KMS (but for KMS can be explicitly enabled by selecting destination KMS key)
  - objects that were replicated to source bucket (no transitive replication)

# S3 Server Access Logs
https://docs.aws.amazon.com/AmazonS3/latest/dev/ServerLogs.html
provides detailed records for the requests that are made to a bucket
Details: single access request, requester, bucket name, request time, request action, response status, error code
No extra charge for enabling and PUTs (but usual charge for storing)
Logs are saved to a bucket in the same AWS regions as the source bucket
Uses a special log delivery account _Log Delivery Group_ to write access log  
Best effort server log delivery:
The completeness and timeliness of server logging is not guaranteed. The log record for a particular request might be delivered long after the request was actually processed, or _it might not be delivered at all_.

To enable:
1. Turn on logging on S3 bucket
2. Grant S3 _Log Delivery group_ write persmission on the target bucket
    **only through bucket ACL** and not through bucket policy
    only SSE-S3 can be used, **SSE-KMS is not supported**  
    Object Lock cannot be enabled on the target bucket
You can _optionally_ specify prefix in the target bucket while enabling logging

# Amazon GuardDuty
Maintains two types of lists:
- Trusted IP list: whitelisted IPs
- Threat List: malicious IPs for which GuardDuty generates findings



# AWS Storage Gateway
**File Gateway**: stores data on S3
- NFS or SMB
**Volume Gateway**
  - _Cached Volumes_:
    - mounted iSCSI devices, data stored on S3, cached on-prem
  - _Stored Volumes_:
    - store all data locally
    - takes snapshot periodically as incremental backup and store on S3
**Tape Gateway**
- Virtual tape library writes to Glacier
- Can run as VM on-prem or EC2 instance

# AWS Systems Manager
https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-managedinstances.html

Provisioning, Deployment, Management
managed policy `AmazonEC2RoleforSSM` to attach to EC2 role to communicate with SSM
Parts:
- SSM Automation
- SSM Inventory
- Patch Manager
- Run Command
- Parameter Store
- Trusted Advisor and PHD

For set-up of Systems Manager for Hybrid environment:

Step 1: Complete general Systems Manager setup steps
Step 2: Create an IAM service role for a hybrid environment (to communicate with SSM service)
Step 3: Install a TLS certificate on on-premises servers and VMs
Step 4: Create a managed-instance activation for a hybrid environment
Step 5: Install SSM Agent for a hybrid environment (Linux or Windows)
Step 6: (Optional) Enable Advanced-Instances Tier for more than 1000 servers per account per region

## SSM State Manager
https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-ssm-docs.html

uses **Command Documentn** to keep EC2 in predefined state
Different Types of SSM Documents
Type | Use with
---|---
Command Document | Run command, State Manager, Maintenance Windows, apply to configuration
Automation Document | Automation, common deployment/maintenance tasks
Package Document | ZIP archive files that contain software to install on managed instances
Session Document | Session Manager (type of session to start)
Policy Document | Enforcing a policy on a managed instance
Change Calendar Document | Associated events that can allow/prevent Automation ations


# Disaster Recovery
RTO
RPO

**Backup and Restore Method**  
- Slowest restoration time after an event
- Requires frequent snapshots of data
- Storage Gateway enables snapshots of on-prem data to be copied to S3
- Gateway VTL can replace magnetic tape backup
- Used with other DR methods

**Pilot Light Method**
- Quicker than backup and restore
- slower than warm standby
- _most critical core components_ are always running and kept up to date
- typically includes DB servers (replication)
- restoring other components include EBS snapshots and EC2 AMIs

**Warm Standby Method**
- Scaled-down version of fully functional environment is always running
- resize instances after failover
- like pilot light uses DB replication

**Multi-Site Solution Method**
- Fastest possible system restore
- 1:1 copy of all Infrastructure in another AZ/region
- **Active-Active**  
- Can perform weighted DNS routing
- uses ASG and instance resizing to increase capacity in a disaster scenario

# VPC
Size from /16 to /28

Reserved IP addresses (first 4 + last):
- 10.0.0.0 - Network address
- 10.0.0.1 - VPC router
- 10.0.0.2 - DNS server address
- 10.0.0.3 - reserved
- 10.0.0.255 - broadcast address (VPC does not support broadcast)

Default VPC
- Size /16
- Default SN in each AZ size /20
- IGW
- RT with route sending all trafic to IGW
- Default SG allowing all trafic
- Default ACL allowing all traffic
- Default DHCP option

## VPC Flog Logs
Stored in CloudWatch logs (but can be sent to S3 bucket)
Each network interface has a unique log stream
Can be on the level:
  - VPC
  - Subnet
  - Network interface
**Not real-time** - delay of several min
Ability to create multiple flog logs per interface (e.g. accepted, rejected or all traffic)
Launching EC2 _after_ creating flow logs will automatically create logs for each new NI

## VPC Peering
direct network routing between different VPCs using private IP addresses
Limitations:
- no matching or overalpping CIDR blocks for participating VPCs
- no transitive peering
- one peering connection for same two VPC
- DNS resolution for private hostnames must be enabled

## VPN
Components:
- Customer Gateway (initiates VPN connection)
- Virtual private gateway: One per VPC - used with IPSec and AWS DX
- VPN connnection - two VPN tunnels - you need to provision two IP addresses on the customer side

## AWS Direct Connect
For dedicated connections DX requires **single-mode fiber**: 1Gbps (1000BASE-LX) or 10Gbps
The network device on-prem must support BGP
Using a private peered connection might not need extra security

## Elastic IP (EIP) and Elastic Network Interface (ENI)
EIP:
- region specific
- IPv6 not supported
- to implement: 1) allocate 2) associate
- to remove: 1) disassociate 2) release
- any previous public IP is released (DNS hostname changes as well)
- charges for:
  - EIP not associated
  - more then one EIP on an instance

ENI:
- virtual network card
- can include primary and secondary IPv4 addresses
- can include public IPv4, EIP, public IPv6
- security group
- MAC addresses
- description

Every EC2 instance in VPC has a default ENI - **primary network interface** (`eth0`) - cannot be detached from the instance
when attaching/reattaching to instances, the attributes (SG) and traffic follow the interface
you can modify attributes (SG and IP) after creation

# Security
https://aws.amazon.com/compliance/shared-responsibility-model/
https://aws.amazon.com/security/penetration-testing/

![AWS Foundational and Layered Security Services](../media/aws-security-services.png)

## IAM Roles
- Temporary credentials in AWS managed by STS
- another entity can **assume** the specific permissions defined by the role
- roles must be used because policies cannot be directly attached to AWS services
- Services can have only **one** role attached at a time

Other use of roles:
- Cross-account access (delegation)
- Identity Federation
  - these users assume an **identity provider** access role

## PassRole
https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html

- A trust policy for the role that allows the service to assume the role
- A permission policy attached to IAM user that allows the user to pass only those roles that are approved
  `iam:PassRole` is usually is accompanied by `iam:GetRole` (so the user can get details of the role to be passed)



## S3 Bucket Policies
Mandatory elements:
- Effect (Deny or Allow)
- Action
- Resource
- Principal
  - account or user that this policy applies to
  - Specific to S3 bucket policies

Optional:
- Sid
- Condition

Use `/*` to apply policy to **all objects** in the

## Data Integrity
To enable MFA delete the versioning must be enabled on the bucket
Can enforce the use of MFA:
```
"Condition": {
  "Null": {
    "aws:MultiFactorAuthAge": true
  }
}
```

## Security Groups and NACLs
Security Group - Instance level
NACL - Subnet level

## AWS STS: Federation
credentials remain active 15m to 1h (default)
credentials are not stored with the user or service, token is used
uses a single endpoint https://sts.amazonaws.com, resides in `us-east-1`
temporary credentials have global scope

Web Identity Federation Playground

## Amazon Inspector
- Analyze the behavior of your AWS resources
- Test network accessibility and security state
- Accesses for security vulnerabilities and deviations from best practices
- Provides recommendation for resolution

Target: a collection of EC2 instances
Requires an agent installed on EC2 instance

## AWS Certificate Manager (ACM
Naitive integration with AWS Services
No associated costs for certificates
Certificates auto-renew

## AWS WAF
integrated with
- ALB
- API Gateway
- CloudFront

WAF Rules are based on conditions:
- IP addresses
- HTTP headers
- HTTP body
- URI strings
- SQL injection
- Cross-site scripting (XXS)

when using WAF on ALB, rules run in region

## AWS Trusted Advisor
Cost Optimization
Perfromance
Security
Fault Tolerance
Service Limits

Available to all **customers**:  
- seven core checks:
  1. S3 Bucket Permissions
  2. Security Groups - Specific Ports Unrestricted
  3. IAM Use
  4. MFA on Root Account
  5. EBS Public Snapshots
  6. RDS Public Snapshots
  7. Service Limits
Available to **Business and Enterprise Support Customers**:  
- Access to the full set of checks
- Notifications (CloudWatch alerts)
- Programmatic access
  - Retrieve results from the AWS Support API
























---
