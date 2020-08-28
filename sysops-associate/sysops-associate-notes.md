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

- **Alarm** can trigger:
    SNS (multiple SNS topics are possible)  
    Auto scaling action (ASG or ECS)  
    EC2 action (stop, terminate, reboot)
- **Rule** can trigger:  
    SNS, SQS, EC2, ECS Task, Lambda, CodeBuild, CodePipeline, Step Function, SSM, Kinesis Stream, Eventbus in other account, Kinesis Firehose  
    Event Source for the rule can be almost any AWS Service (Event type: CloudTrail API Call)

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
- EBS initialisation for snapshots
- CloudWatch Metrics:
  - `VolumeRead/Write Bytes`  
  - `VolumeRead/Write Ops`  
  - `VolumneTotalRead/Write Time`  
  - `VolumeIdleTime`  
  - `VolumeQueueLength`  
  - `VolumeThroughputPercentage`: only for Provisioned IOPS (SSD)  
  - `VolumeConsumedReadWriteOps`: only for Provisioned IOPS  
- Status Checks:
|Status|I/O Performance Status|
|---|---|
|ok |normal|
|warning|Degrated or Severely Degraded|
|impaired|Stalled or Not Available|
|insufficient data|insufficient data|

## Monitoring ELB
**CloudWatch**:  
**Access logs**: disabled by default, can store data where EC2 instance has been deleted. Detailed information for every request received by ELB, including client IP and server responses
**Request tracing**: _for ALB only_. Track HTTP requests from clients to targets or other services. ALB adds `X-Amzn-Trace-Id` header  
**CloudTrail**: calls made to ELB API

## Monitoring Elasticache
Memcached: https://docs.aws.amazon.com/AmazonElastiCache/latest/mem-ug/CacheMetrics.WhichShouldIMonitor.html  
Redis: https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/CacheMetrics.html  

Metrics to watch:
`CPU Utilisation`: see below
`Swap Usage`: amount of Swap file that is used  
`Evictions`: Memcached: Either Scale up or out. Redis: Only Scale Out  
`Concurrent Connections`: set alarm on that metric

Memcached CPU Utilisation:
 - Multithreaded  
 - Can handle load up to 90%, >90% add more nodes to the cluster  
 - `SwapUsage` should be around 0 and not > than 50Mb. Increase `memcached_connection_overhead` parameter  

Redis CPU Utilisation:
 - Not multithreaded  
 - To determine the point in which to scale, divide 90% by the number of cores  
 - No `SwapUsage` metric, instead use `reserved-memory`  

## CloudWatch Dashboard
Dashboards are cross-region (international), widget added on the region basis

# AWS Organisations
Manage multiple AWS accounts, can create groups of accounts and apply policies to the groups
- Central management of accounts
- Control Access with SCP (allow or deny individual AWS services on account group basis). Attach policies to the OU (applies to all accounts within OU). Can select _black_ or _white_ listing for SCP
- Automate AWS account creation
- Consolidated billing

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

# EC2 Pricing Refresher
On Demand
Reserved:
  - standard RIs
  - convertible RIs
  - scheduled RIs
Spot
Dedicated Hosts

# AWS Config
AWS resource inventory, configuration history and configuration change notification
Region-based
Stores all in S3 Bucket
Can stream configuration changes to SNS
Can trigger Lambda on Events
Can enable rule compliance by monitoring and triggering SNS notification
Compliance checks - periodic or triggered
Managed Rules available (AWS Config Rules)
You can see the timeline of changes
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
 - Personal Health Dashboard (global by default)

# Security
All interactions with AWS API signed with `AccessKeyId` and `SecretAccessKey` using Sig4
`AccessKeyId` and `SecretAccessKey` map to users or roles in IAM

The _default credential provider chain_ implemented by `DefaultAWSCredentialsProviderChain` looks for credentials in this order:
  1. Specified in the code  
  2. Environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`  
  3. Default credential profile in the `~/.aws/credentials` file  
  4. Amazon ECS container credentials: loaded from ECS if environment variable `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI` is set  
  5. Amazon EC2 instance role: used on EC2 instance and delivered via EC2 metadata service  


# EC2 Launch Issues
- `InstanceLimitExceeded`: per region
- `InsufficientInstanceCapacity`: AWS not enough availble On-Demand capacity
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
- **Paravirtual (PV)** AMIs  

# EBS Volumes


















---
