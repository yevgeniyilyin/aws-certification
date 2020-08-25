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

Create EC2 custom metric [LAB](/labs/custom-cloudwatch-metric/script.sh):
 - create a EC2 role with CloudWatch access policy
 - send a custom metric to CloudWatch from EC2 (use `curl https://aws-cloudwatch.s3.amazonaws.com/downloads/CloudWatchMonitoringScripts-1.2.2.zip -O`)

## Monitoring EBS
