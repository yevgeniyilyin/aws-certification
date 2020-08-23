# SA Associate Course Notes


/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install awscli

S3 Master class on A Cloud Guru
S3 FAQs


# Cross-region replication
https://docs.aws.amazon.com/AmazonS3/latest/dev/replication-what-is-isnot-replicated.html#replication-what-is-not-replicated
https://aws.amazon.com/solutions/implementations/cross-region-replication-monitor/
https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region
https://docs.aws.amazon.com/AmazonS3/latest/dev/replication-add-config.html


# CloudFront
https://aws.amazon.com/cloudfront/features/


# Alias and non-alias records
http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html


# Routing policy chaning
https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-complex-configs.html

CIDR.xyz


Video - Configure a VPN over AWS Direct Connect



ELB FAQ

# Maxing CPU out (single core)
while true; do true; done



Amazon Linux 2 AMI (HVM), SSD Volume Type - ami-08f3d892de259504d (64-bit x86) / ami-0ba960472fc891755 (64-bit Arm)



curl http://checkip.amazonaws.com

https://aws.amazon.com/premiumsupport/plans/?nc1=h_ls

https://broadcast.amazon.com/videos/219823

###########################################################

# Whitepapers
https://d1.awsstatic.com/whitepapers/building-a-scalable-and-secure-multi-vpc-aws-network-infrastructure.pdf

# FAQs
https://aws.amazon.com/directconnect/faqs/
https://aws.amazon.com/elasticloadbalancing/faqs/
https://aws.amazon.com/s3/faqs/
https://aws.amazon.com/global-accelerator/faqs/
https://aws.amazon.com/api-gateway/faqs/
https://aws.amazon.com/lambda/faqs/
https://aws.amazon.com/ec2/autoscaling/faqs/
https://aws.amazon.com/datasync/faqs/
https://aws.amazon.com/redshift/faqs/


# AWS SSO
https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html


# all links from here
https://linuxacademy.com/cp/courses/lesson/course/4444/lesson/1/module/341


http://www.davidc.net/sites/default/subnets/subnets.html
https://subnettingpractice.com/calc.html


https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-comparison.html
https://aws.amazon.com/blogs/security/securely-connect-to-linux-instances-running-in-a-private-amazon-vpc/
https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports


# IAM and Security
https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html
https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html
https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html#rotating_access_keys_console

# Identity Federation
https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRole.html
https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithSAML.html
https://docs.aws.amazon.com/STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html
Web Identity Federation Playground

# Cost Management
https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/budgets-managing-costs.html


# EC2
https://www.youtube.com/watch?v=e8DVmwj3OEs
https://aws.amazon.com/ec2/instance-types/
https://github.com/linuxacademy/content-aws-csa2019/blob/master/lesson_files/03_compute/Topic1_Fundamentals/05_SecurityGroups/userdata1.txt
https://cloudinit.readthedocs.io/en/latest/
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide//ec2-scheduled-instances.html
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html
https://aws.amazon.com/about-aws/whats-new/2016/06/introducing-elastic-network-adapter-ena-the-next-generation-network-interface-for-ec2-instances/
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html#TroubleshootingInstancesConnectionTimeout
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Hibernate.html#instance_hibernate


# Auto Scaling Groups
https://aws.amazon.com/ec2/autoscaling/faqs/
https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroupLifecycle.html
https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-simple-step.html
https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-alarms-trigger-actions/
https://aws.amazon.com/premiumsupport/knowledge-center/auto-scaling-troubleshooting/
https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html
https://docs.aws.amazon.com/autoscaling/ec2/userguide/Cooldown.html


# EC2 Storage
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.html
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html
https://aws.amazon.com/blogs/storage/migrating-storage-with-aws-datasync/

# API Gateway
https://aws.amazon.com/api-gateway/faqs/
https://docs.aws.amazon.com/apigateway/latest/developerguide/integrating-api-with-aws-services-lambda.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html


# lambda
https://aws.amazon.com/lambda/faqs/?nc1=h_ls
https://docs.aws.amazon.com/lambda/index.html
https://aws.amazon.com/de/blogs/architecture/understanding-the-different-ways-to-invoke-lambda-functions/
https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html


# DNS
https://www.iana.org/domains/root/servers
https://www.iana.org/domains/root/db


# S3
https://aws.amazon.com/blogs/security/iam-policies-and-bucket-policies-and-acls-oh-my-controlling-access-to-s3-resources/
https://aws.amazon.com/blogs/security/how-to-prevent-uploads-of-unencrypted-objects-to-amazon-s3/
https://docs.aws.amazon.com/AmazonS3/latest/dev/cors.html
https://docs.aws.amazon.com/AmazonS3/latest/dev/DeletingObjectVersions.html
https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMFADelete.html
https://aws.amazon.com/s3/storage-classes/
https://docs.aws.amazon.com/AmazonS3/latest/user-guide/restore-archived-objects.html
https://docs.aws.amazon.com/AmazonS3/latest/dev/GettingObjectsUsingAPIs.html

# cloudfront
https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html
https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/QueryStringParameters.html


# S3 static website with cloudfront
https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html
https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-access-to-amazon-s3/
http://d11313wa705ekn.cloudfront.net


# efs
https://docs.aws.amazon.com/efs/latest/ug/efs-access-points.html
https://docs.aws.amazon.com/efs/latest/ug/encryption.html
https://docs.aws.amazon.com/efs/latest/ug/performance.html#throughput-modes

# RDS
https://aws.amazon.com/rds/details/multi-az/
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithOptionGroups.html
https://aws.amazon.com/rds/mysql/pricing/
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.IAM.html
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Limits.html
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html

# Aurora
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.setting-capacity.html
https://aws.amazon.com/rds/aurora/pricing
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Replication.html
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-mysql-parallel-query.html
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Managing.Backtrack.html
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Migrating.RDSMySQL.Import.html
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Managing.FaultInjectionQueries.html
https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Integrating.Lambda.html#AuroraMySQL.Integrating.LambdaAccess


# DynamoDB
https://aws.amazon.com/blogs/database/choosing-the-right-dynamodb-partition-key/
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.Partitions.html
https://aws.amazon.com/blogs/database/automatically-archive-items-to-s3-using-dynamodb-time-to-live-with-aws-lambda-and-amazon-kinesis-firehose/
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Programming.Errors.html#Programming.Errors.MessagesAndCodes
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html


# Kinesis
https://aws.amazon.com/blogs/big-data/scaling-amazon-kinesis-data-streams-with-aws-application-auto-scaling/
https://aws.amazon.com/blogs/big-data/under-the-hood-scaling-your-kinesis-data-streams/

# AWS Application Auto Scaling
https://docs.aws.amazon.com/autoscaling/application/APIReference/Welcome.html

# Redshift
https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-snapshots.html#cross-region-snapshot-copy
https://aws.amazon.com/about-aws/whats-new/2016/09/amazon-redshift-now-supports-enhanced-vpc-routing/


# AWS Data Pipeline
https://docs.aws.amazon.com/datapipeline/latest/DeveloperGuide/what-is-datapipeline.html

# AWS Global Accelerator
https://docs.aws.amazon.com/global-accelerator/latest/dg/introduction-benefits-of-migrating.html


# CloudWatch
https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html
https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Anomaly_Detection.html


# CloudTrail
https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-validation-intro.html
https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-concepts.html#cloudtrail-concepts-global-service-events


# OpsWorks
https://docs.aws.amazon.com/opsworks/latest/userguide/welcome_classic.html
https://docs.chef.io/quick_start.html
https://docs.chef.io/cookbooks.html


# HPC
https://aws.amazon.com/blogs/opensource/scale-hpc-workloads-elastic-fabric-adapter-and-aws-parallelcluster/
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa-working-with.html
