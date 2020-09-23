# Solutions Architect Professional

![CSAP badge](../media/csap-badge.png)

Training course notes

# Whitepapers
📒[AWS Securing Data at Rest with Encryption](https://d0.awsstatic.com/whitepapers/aws-securing-data-at-rest-with-encryption.pdf)  
📒[AWS Web Hosting Best Practices](https://d0.awsstatic.com/whitepapers/aws-web-hosting-best-practices.pdf?refid=em_)  
📒[AWS Migrate resources to a new Region](http://d0.awsstatic.com/whitepapers/aws-migrate-resources-to-new-region.pdf?refid=70138000001adyu)  

# AWS Accounts
Consist of three discrete domains:
- Authentication
- Authorization
- Billing

By default, every AWS Account has separate billing, users and permission (**blast  radius**)  

You cannot limit permission of Root User (unrestricted permission)
Identity Store
Authorization Store


# HA, Fault Tolerance (FT), DR
 - **HA**: _Ability_ to recover from the failure and minimize the outage, might be disruption
 - **FT**: _Able_ to operate during the failure, no disruption
 - **DR**: _Protecting_ the critical system data

 - **RPO**: The time between when a disaster occurs and the last recoverable copy of key business data was created (amount of data lost). Minimize the length of this time period via regular backups, snapshots, transaction logs  

 - **RTO**: The time between when a disaster occurs and when the system can be restored to an operational state and handed over to the business for testing.

# Data Persistence

 - **Ephemeral**: data is local to resource and is lost when that resource is powered down:
  - _Instance Store Volume_ (fastest type of storage available in AWS). If instance reboots, the data in the instance store persists. If instance stops, terminates or the underlying disk fails, the data is lost
  - _Amazon ElastiCache_

- **Transient**: data that exists in a form while it's passed between sources
  - _Queue_

- **Persistent**: data that is durable and survives power events
  - _EBS_
  - _EFS_

# OSI 7-Layer Networking Model
📒https://en.wikipedia.org/wiki/OSI_model  

1. **Physical**
2. **Data Link**: Mac-Addresses
3. **Network**: IP-Addresses, Network Devices, Not reliable IP-protocol, no ordering
4. **Transport**: TCP (reliable) & UDP
5. **Session**: Ports, Connections
6. **Presentation**: Encryption, SSL
7. **Application**: HTTP, Application Devices

# IAM
Only two ways for user to authenticate: via username/password or via access keys - both are long-term credentials

**Real identities**: user and roles:
- have Amazon ARN
- can be referenced (e.g. in resource polices or permission policies

5000 IAM users per account limit

## Identity and Resource Policies
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html  
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html  
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html  

Mandatory elements:
 - `Statement` - contain single statement or array of individual statements
 - `Effects` (Allow, Deny)
 - `Action`/`NotAction`
 - `Resource`/`NotResource`

 Elements:
  - `Principal`/`NotPrincipal` - specify the principal that is allowed or denied to a resource. Not allowed in IAM identity-based policy, but mandatory in trust policies for IAM roles and resource-based policies
  [Principals](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html) can be:
  - AWS accounts and root user
  - IAM users
  - Federated users
  - IAM roles
  - Assumed-role sessions
  - AWS services
  - Anonymous users (not recommended)

Conditions:
```json
"Condition" : { "{condition-operator}" : { "{condition-key}" : "{condition-value}" }}
```

[Variables](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html) (all are case-insensitive):

Available in ALL requests
- **`aws:CurrentTime`**
- **`aws:EpochTime`**
- **`aws:TokenIssueTime`**
- **`aws:PrincipalType`**
- **`aws:SecureTransport`**
- **`aws:SourceIp`**
- **`aws:UserAgent`**
- **`aws:userid`**
- **`aws:username`**
- **`ec2:SourceInstanceARN`**


## IAM Roles and Temporary Security Credentials
📒https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-instance-metadata.html  

A role has tow components: a **trust** policy (which defines a principle and conditions under which the role can be assumed) and a **permission** policy which defines the AWS access rights granted during `AssumeRole`

A role has _no_ long-term credentials on its own. STS generates temporary credentials (min 15m to max 12h)

### AWS API Access
All interactions with AWS API signed with `AccessKeyId` and `SecretAccessKey` using Sig4
`AccessKeyId` and `SecretAccessKey` map to users or roles in IAM

The _default credential provider chain_ implemented by `DefaultAWSCredentialsProviderChain` looks for credentials in this order:
  1. Specified in the code  
  2. Environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` and `AWS_SESSION_TOKEN`
  3. Default credential profile in the `~/.aws/credentials` file  
  4. Amazon ECS container credentials: loaded from ECS if environment variable `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI` is set  
  5. Amazon EC2 instance role: used on EC2 instance and delivered via EC2 metadata service:
  `curl http://169.254.169.254/latest/meta-data/iam/security-credentials/<name of IAM role>`

### Revoke session
_Revoke session_ adds an additional `Deny` policy to the role with the condition:
```json
  "Condition": {
    "DateLessThan": {
      "aws:TokenIssueTime":"<current time>"
    }
  }
```

You _cannot_ revoke the existing temporary credentials, but _Revoke session_ (with the condition policy) will invalidate (with explicit Deny) all sessions with token older than the specified `aws:TokenIssueTime`

### Cross-Account Access
📒https://docs.aws.amazon.com/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-8  
📒https://aws.amazon.com/blogs/security/iam-policies-and-bucket-policies-and-acls-oh-my-controlling-access-to-s3-resources/  

Cross-Account Access to S3 Buckets and Objects

ACL | Bucket Policy | IAM Role
---|---|---
ACLs is a legacy access control for S3. Access control only. Objects are _owned by the identity who PUTS them_. ACL can apply to objects! | Permission control is handled within S3. There is no IAM involved. Account who PUTS objects is the owner. Bucket policies can require the bucket owner account be the owner for objects as they are put (buy a different account) in the bucket | Objects are owned by the role in account which owns the role. Permission are managed by IAM, not S3

Scenarios for **ACL** use:
- control access to the objects
- S3 Logging (Log Delivery Group)

You can grant bucket/object access to other account (using canonical id) via ACL
**Main limitation** for ACL - all objects created by the account B in the bucket owned by account A, are owned by the account B and the account A doesn't have permission to these objects

The same situation with access via **Bucket Policy** (the objects are owned by the creator of the object and not by the bucket owner account).

To give full control to the bucket owner account, the following bucket policy should be applied to grant Cross-Account Permissions to Upload Objects While Ensuring the Bucket Owner Has Full Control:

```json
{
   "Version":"2012-10-17",
   "Statement":[
     {
       "Effect":"Allow",
       "Principal":{"AWS":"123456789012"},
       "Action":"s3:PutObject",
       "Resource":"arn:aws:s3:::awsexamplebucket1/*"
     },
     {
       "Effect":"Deny",
       "Principal":{"AWS":"123456789012"},
       "Action":"s3:PutObject",
       "Resource":"arn:aws:s3:::awsexamplebucket1/*",
       "Condition": {
         "StringNotEquals": {"s3:x-amz-acl":"bucket-owner-full-control"}
       }
     }
   ]
}
```

## Advanced Identity in AWS

### Identity Federation
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html  
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc_cognito.html  

When to use STS:
- Identity Federation:
  - Enterprise identity federation, STS supports SAML (allows use of Microsoft AD)
  - Web identity federation (Facebook, Google, Amazon) supporting OIDC (OpenID Connect)
- Roles for cross-account access
- Roles for Amazon EC2 and other AWS services
  - grant access to application running on EC2 to access other AWS services without having to imbed credentials

For mobile applications [**Cognito use is recommended**](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc_cognito.html)

STS API:
- `AssumeRole`  
- `AssumeRoleWithWebIdentity`  
- `AssumeRoleWithSAML`  

When requested via STS API call, a credential object is returned containing:
- Session Token
- Access Key ID
- Secret Acess Key
- Expiration Timestamp

**`AssumeRoleWithWebIdentity`**  
1. Log into Google, ID token returned
2. Using ID tocken call STS API `AssumeRoleWithWebIdentity`  
3. STS provides temporary credentials
4. Use temp credentials to call AWS API

**`AssumeRoleWithSAML`**  
❗Possible to have [access to AWS Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_enable-console-saml.html) - SSO - (not possible with WebIdentity)

![SAML-Federation](../media/SAML-Federation.png)

![SAML-SSO](../media/SAML-SSO.png)


### IAM Permission Boundaries
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html  

Limit the maximum permissions an identity can have.
Can be applied to:
- IAM Users
- IAM Roles
- AWS Organisations (SCP)

Do not give any permissions
Used to delegate the access

**Resource-based policies** – Resource-based policies control how the specified principal can access the resource to which the policy is attached. Within an account, an implicit deny in a permissions boundary does not limit the permissions granted by a resource-based policy. Permissions boundaries reduce permissions that are granted to an entity by identity-based policies, and then resource-based policies provide additional permissions to the entity.

**Organizations SCPs** – SCPs are applied to an entire AWS account. They limit permissions for every request made by a principal within the account. An IAM entity (user or role) can make a request that is affected by an SCP, a permissions boundary, and an identity-based policy. In this case, the request is allowed only if all three policy types allow it. The effective permissions are the intersection of all three policy types. An explicit deny in any of these policies overrides the allow.

**Session policies** – Session policies are advanced policies that you pass as a parameter when you programmatically create a temporary session for a role or federated user. The permissions for a session come from the IAM entity (user or role) used to create the session and from the session policy. The entity's identity-based policy permissions are limited by the session policy and the permissions boundary. The effective permissions for this set of policy types are the intersection of all three policy types.

### Policy Evaluation Logic
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html

Organisation Boundaries -> User or Role Boundaries -> Role Polices -> Permissions (Identity Polices + Resource Polices)

explicit DENY -> explicit ALLOW -> implicit DENY

# Account Management

## AWS Accounts and AWS Organisations
📒https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/useconsolidatedbilling-discounts.html  

Only one single master account in any organisation
**Master account** (root container):
- A master account is the AWS account you use to create your organization
- You cannot change which account in your organization is the master account
- Create organisations and OUs
- Invite an external account to join organisation
- Pay all charges accrued by all accounts in organisation
- **Never affected by SCP**  

Applying policies at the root level propagates them to all OUs and accounts below

Organisations operate in either **Consolidated Billing** or **All Features** mode (can be modified later)

When you create a member account in your organization, AWS Organizations automatically creates an IAM role `OrganizationAccountAccessRole` in the member account that enables IAM users in the master account to exercise full administrative control over the member account. This role is subject to any service control policies (SCPs) that apply to the member account.

AWS Organizations also automatically creates a service-linked role named `AWSServiceRoleForOrganizations` that enables integration with select AWS services. You must configure the other services to allow the integration.

## Service Control Policies (SCP)
similar to IAM permissions policies but SCP _don't grant any permissions_  
SCP's don't have any effect on **master** account, but for all other accounts they **impact** IAM and ROOT user

If multiple SCPs apply to an account - only the **overlap** of those SCPs is permitted

- **Allow list strategy**: explicitly specify the access that is allowed. All other access is implicitly blocked.
  By default, AWS Organisations attaches an AWS managed policy `FullAWSAccess` to all roots, OUs and accounts.
  When you restrict permissions, you _replace_ the `FullAWSAccess`
  You can't add permissions back at lower lever in the hierarchy
- **Deny list strategy**: explicitly specify the access that is not allowed. All other access is allowed.
  This is the default behavior of AWS Organisations.
  You leave the default `FullAWSAccess` policy in place and attach additional policies that explicitly _deny_ access to unwanted services and actions

## AWS Account Limits
📒https://docs.aws.amazon.com/general/latest/gr/aws-general.pdf#aws-service-information  
📒https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html  
📒https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-limits.html  

## AWS Support Tiers
📒https://aws.amazon.com/premiumsupport/plans/  
- Developer
- Business
- Enterprise

# AWS Config
📒https://aws.amazon.com/blogs/aws/aws-config-rules-dynamic-compliance-checking-for-cloud-resources  
📒https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config-rules.html  

- AWS resource inventory, configuration history and configuration change notification
- Region-based
- Stores all in S3 Bucket
- Snapshot of current configuration of your account
- Can stream configuration changes to SNS
- Can trigger Lambda on Events
- Can enable rule compliance by monitoring and triggering SNS notification
- Compliance checks - periodic or triggered
- Managed Rules available (AWS Config Rules)
- You can see the timeline of changes and retrieve past configurations

AWS Config **requires IAM role** with permissions:
 - Read-only to recorded resources
 - Write to S3 logging bucket
 - Publish access to SNS

AWS Config rules can, e.g:
- Ensure that EC2 instances launched in a particular VPC are properly tagged.
- Make sure that every instance is associated with at least one security group.
- Check to make sure that port 22 is not open in any production security group.

# AWS Service Catalog
📒https://docs.aws.amazon.com/servicecatalog/latest/adminguide/getstarted-iamenduser.html  

Regional service

Administrators define products and portfolios (groups of products and configurations) make them available for end users
personalised portal - deploy only approved resources that comply with organisational policies and budget constraints
- Access Control
- Enforce Standards

Portfolio - collection or grouping of products, selectively grant access
Product - definition, support contract, owner - CFN template
Add users to the portfolio
Can share portfolio cross-account or within AWS Organisations

Service Catalog Admin:
1. Create portfolio
2. Author template (CloudFormation)
3. Create product (upload the CloudFormation template)
4. Add constraints and access
8. Events from CloudFormation

need to create a `Service Catalog` service role for product cloud formation template (which creates all resources for the product, not the end users)

End Users:
5. Browse products
6. Launch products
7. Events from CloudFormation

# Resource Billing Modes: On-Demand, Reserved, and Spot
📒https://aws.amazon.com/blogs/compute/new-amazon-ec2-spot-pricing/  
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html  
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-capacity-reservations.html  
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-reserved-instances.html  
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html  
📒https://github.com/open-guides/og-aws#billing-and-cost-management  

![Differences between Capacity Reservations, Reserved Instances, and Savings Plans](../media/capacity-reservation.png)


# EC2
:question:https://aws.amazon.com/ec2/faqs/

## Virtualization and EC2 Instance Type
📒https://aws.amazon.com/ec2/instance-types/

![EC2 Virtualization types](http://www.brendangregg.com/blog/images/2017/ec2-types-numbered.png)

🔸**General Purpose (A, T, M)**
  - M5: default instance type, general workhorse inside AWS, steady state 80-90% CPU
  - M5a: using AMD CPU
  - T*: burstable CPU performance
  - A1: Arm CPU AWS Graviton

🔸**Compute Optimized (C)**
  - instances use more CPUs
  - C5
  - C5n with additional network capabilities

🔸**Memory Optimized (R, X)**
  - more memory
  - X1 and X1e: the highest amount of memory

🔸**Accelerated Computing (P, Inf, G, F)**
  - GPU-based instances
  - P: GPU-based general computing
  - G: graphic-intense application
  - F: customizable hardware-acceleration with FPGAs

🔸**Storage Optimized (I, D, H)**
  - additional NVMe SSD-backed instance storage optimized for low latency
  - H: up to 16TB local HDD-storage
  - I: up to 60TB local NVMe SSD-storage

## Creating and using AMIs
- AMIs are objects (meta data) containing all the information required to launch an instance, the owner of AMI, launch permissions (public, explicit or implicit), the architecture and operating system and block device mapping of all volumes required.

- AMIs contain mapping to any volumes, these mappings reference EBS volume snapshots in the same region
- AMIs are regional but can be copied between regions which **also copies any volume snapshots**

When AMI created from the instance `create image`:
- Root volume snapshot created
- other volume snapshots created

By default no permissions are stored when AMI is created (implicit creator permission)
You can modify permission to be public or private (shared with specific AWS accounts)

❗when creating AMIs from instances with instance store root volumes - a **bundle** is created and stored on S3 containing ALL the root volume data

## EC2 Storage and Snapshots
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSPerformance.html
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html

Block-level storage:
- **Instance store**
  - local physical storage attached to hosts
  - Temporary storage
  - High I/O and Throughput (**highest** I/O available in AWS)
  - volumes mapped as `ephemmeral0` to `23` (max 24 volumes)
  - included in the instance cost

  ❗**no support for snapshots and not resilience**  

- **EBS**
 - network storage associated with one instance
 - Persistence
 - Durability (but EBS is not the most durable storage service)
 - Elasticity - requirements for variable or changing performance demands over time
 - Provisioned Performance - certain EBS types can provide guaranteed performance levels
 - EBS Optimized instances provide a dedicated storage network (dedicated ENI)

 ❗Max IOPS per instance - 160000
 ❗Max throughput per instance - 4750MB/s

  🔸**General Purpose gp2**
    - 1-16TB
    - max 16000 IOPS
    - max throughput 250 MB/s
    - burst up to 3000 IOPS
    - baseline 3 IOPS/GB (100 - 16000)
  🔸**Provisioned IOPS SSD io1, io2**
    - 4-16TB
    - max 64000 IOPS
    - max 1000 MB/s
  🔸**Throughput Optimized HDD st1**
    - 500GB-16TB
    - max 500 IOPS
    - max 500 MB/s
  🔸**Cold HDD sc1**
    - 500GB-16TB
    - max 250 IOPS
    - max 250 MB/s

## EC2 Instance Profiles and Roles
Instance profile allow a role to be assumed to a single EC2 instance, and for applications running on that instance to assume a role while being abstracted away from any AWS identity. Applications running on EC2 are not AWS Identities and so it's instance profile which bridges that gap.

`http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLENAME`

Credentials are automatically renewed when expired
Only a single instance profile can be associated with an EC2 instance containing a single EC2 role

❗If associating a role via the console, the instance profile is **automatically** created and associated.
If using API or CLI or CloudFormation the two steps are distinct and must be done explicitly.

❗All applications running on the instance share the role credentials - it's not possible to be more granular

## HPC and Placement Groups
📒https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html

Not possible to modify placement group after creation

### Cluster Placement Groups
- exist in a **single AZ**
- High speed, low latency
- reserve physical capacity keeping instances in physical vicinity
- higher chance of capacity related failures
- not every instance type supports cluster PG
- deploy in advance

### Partition Placement Groups
- isolated blocks of infrastructure in isolated fault-domains
- used when you need to ensure HA, but need physical location control
- instances in one partition do not share the underlying hardware with groups of instances in different partitions
- typically used by large distributed and replicated workloads like Hadoop, Cassandra, Kafka

### Spread Placement Groups
- designed for a **small** number of critical components where you **need** to ensure separation
- can be single- or multi-AZ
- up to 7 instances per AZ per group
- highest level of availability
- strictly places a small group of instances across distinct underlying hardware

## Custom Logging to CloudWatch
📒https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-EC2-Instance-fleet.html  
📒https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-Configuration-File-Details.html  
📒https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-cloudwatch-agent-configuration-file.html  

Roles needed for EC2:
`AmazonEC2RoleforSSM` - if SSM is used
`CloudWatchAgentServerPolicy`

Install CloudWatch Agent on the instance
Create a CloudWatch agent configuration file (JSON)
Start agent

# VMware
:tv:https://www.youtube.com/watch?v=RStQrGmHqy0

# Containers

## ECS Architecture
📒https://linuxacademy.com/cp/modules/view/id/261  
📒https://docs.aws.amazon.com/AmazonECS/latest/developerguide/example_task_definitions.html  

Container > Task > Service > Cluster

# ECS Security
📒https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_awslogs.html  
📒https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html  

Fargate-type:
- Each task is associated with ENI in VPC
- ENI is associated with a SG - you can use the SG to restrict traffic to the container

EC2-type:
- **hosts** have EC2 role attached (`AmazonEC2ContainerServiceEC2Role`):
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeTags",
                "ecs:CreateCluster",
                "ecs:DeregisterContainerInstance",
                "ecs:DiscoverPollEndpoint",
                "ecs:Poll",
                "ecs:RegisterContainerInstance",
                "ecs:StartTelemetrySession",
                "ecs:UpdateContainerInstancesState",
                "ecs:Submit*",
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
```
- **Tasks** have IAM role attached - allows fine-graned permission on the task-level (and only way to manage permissions with Fargate)

❗Task execution role - used by ECS on your behalf to interact with CloudWatch, ECR etc - given to ECS container agent

# EKS
:tv:https://www.youtube.com/watch?v=EDaGpxZ6Qi0


# Serverless

# Lambda
:tv:https://www.youtube.com/watch?v=QdzV04T_kec

- max timeout 15m
- priced per # of requests and duration
- can be used cross-region

Soft Limits:
  - default *1000* concurrent execution across all functions per region per account (`TooManyRequestsException` if exceeded)
  - 75GB function and layer storage
  - 250 ENI per VPC  

Hard Limits:
  - 6MB sync and 256KB async Invocation payload (request and response)  
  - Deployment package size: 50MB (zipped, direct upload), 250MB (unzipped, including layers), 3MB (console editor)  
  - 512MB /tmp directory storage
  - 1024 file descriptors
  - 1024 execution processes/threads

Version Control:
  Each Lambda version has a unique ARN  
  After publishing, the version is immutable (you can edit only `$LATEST`)  
  `$LATEST` - maintains the latest code  
    _Qualified ARN_ - function ARN + version suffix  
    _Unqualified ARN_ - only function ARN, use `$LATEST`  
  Aliases: use to point specific ARN (application use alias incl. `$LATEST`)  
    aliases have static ARN but can point to any version of the same function  
    you can use weighted alias to shift traffic between versions  
    rollback as easy as updating the version in the alias  

Invocation can be synchronous/asynchronous

Function configuration:
- Basic settings: Runtime, Handler (filename.functionname), Memory, Timeout  
- Monitoring (CloudWatch, X-Ray)  
- Permissions  
- Enviroment Variables: max 4KB, by default encrypted at rest using KMS, can be encrypted in transit  
- VPC - function can access VPC resources in specified VPC (e.g RDS)  
    - Also provide Subnet ID and Security Group ID  
    - Lambda sets up ENIs using an available IP address from your private subnet  
    - in CLI: `--vpc-config SubnetIds=subnet-XXXX, SecurityGroupIds=sg-YYYYYY`  
    - Lambda execution role should have the following EC2 permissions:  
      `ec2::CreateNetworkInterface`  
      `ec2::DeleteNetworkInterface`  
      `ec2::DescribeNetworkInterface`  

    - File System - connect to EFS  
    - Asynchronous invocation settings + DQL - SQS or SNS  
    - Concurrency, _provisioned concurrency_ (e.g. for weighted alias)  
    - Database proxies (manages pool of connections)  

Lambda API Actions:  
    `AddPermission`: add permission to the resource policy to invoke Lambda   
    `CreateFunction`:  
    `Invoke`: synchronous  
    `InvokeAsync`   
    `CreateEventSourceMapping`: identifies a stream as an event source for Lambda
      can be DynamoDB stream or Kinesis

Lambda metrics:  
    Invocation, Performace, Concurrency

## Lambda Layers
https://docs.aws.amazon.com/lambda/latest/dg/runtimes-custom.html
https://docs.aws.amazon.com/lambda/latest/dg/runtimes-walkthrough.html
https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html#configuration-layers-path
https://github.com/awslabs/aws-lambda-cpp

- ZIP archive that contains libraries, custom runtime or other dependencies
- You can use libraries in your function without needing to include them in your deployment package
- Up to 5 layers per function
- `update-function-configuration` to add layers to the function, include all layers every time  
- Layers are versioned and **immutable**
- Layers are extracted to the `/opt` directory in the function execution environment  
- You can move runtime dependencies out of your function code by placing them in a layer  

# API Gateway
:tv:https://www.youtube.com/watch?v=tIfqpM3o55s
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/getting-started-http-integrations.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/getting-started-aws-proxy.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-api-integration-types.html  
https://aws.amazon.com/about-aws/whats-new/2017/11/amazon-api-gateway-supports-canary-release-deployments/
https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-cors.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html
https://aws.amazon.com/blogs/aws/new-usage-plans-for-amazon-api-gateway/
https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html
https://aws.amazon.com/appsync/

- Resource URL
- Stage is added to default API endpoint
- Respond to request: Lambda, HTTP Endpoints, other AWS services
- Deployments -> snapshot of API's resources and methods
- Stages -> dev, prod, beta.
  Specific settings: caching, request throttling, logging, stage variables
- Caching -> set capacity, encryption, TTL, per-key invalidation
- Dashboard -> API calls, Latency, Integration Latency, 4xx and 5xx Errors
- Can import API from Swagger 2.0
- Throttling -> by default limits the steady-state request rate to 10000 rps
    Max 5000 concurrent requests accross all APIs within AWS account
    429 Too Many Requests error
- Can configure SOAP Webservice Passthrough (doesn't convert XML!)
- Canary releases - create/promote canary
- Stage variables:
  - name-value pairs you can define as configuration attributes associated with a deployment stage
  - use as environment variables in API setup and mapping templates (`${stageVariables.Name}`)
- To support CORS, API resource needs to implement an OPTIONS method that can respond to the OPTIONS request with following header:
  `Access-Control-Allow-Headers`
  `Access-Control-Allow-Origin`
  `Access-Control-Allow-Methods`  

Integration types:
  - `AWS`: AWS Service actions (integration with AWS services)  
      you must configure both integration request and response  
  - `AWS_PROXY`: Lambda proxy integration, API Gateway passes the incoming request direct to Lambda  
      you do not set integration request or response  
  - `HTTP`: HTTP endpoints in the backend  
      you must configure both integration request and response  
  - `HTTP_PROXY`: access to HTTP backend with a streamlined integration setup on single API method  
      you do not set integration request or response   
  - `MOCK`: API Gateway return a response without sending the request to the backend  

[Access Control](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-to-api.html) - supported mechanisms:
  - **Resource policies**: resource-based policies to allow or deny access to APIs and methods from specified source IP or VPC endpoints (e.g. AWS accounts whitelist, IP range blacklist, Source VPC whitelist). These resource policies are attached to resources
  - **Standard IAM roles and policies**: can be applied to entire API or individual methods (e.g. who can create, managed and invoke APIs).  
    `authorizationType=AWS_IAM`  
  - **IAM tags**: can be used together with IAM policies to control access - using `aws:ResourceTag` in IAM policies  
  - **Endpoint policies for interface VPC endpoints**: allow you to attach IAM resource policies to interface VPC endpoints to improve the security of private APIs.  
  - **Lambda authorizers**: _Lambda functions_ that control access to API methods using bearer token authentication (OAuth or SAML)- as well as information described by headers, paths, query strings, stage variables, or context variables request parameters to determine the caller's identity.  
    - _token-based_: `TOKEN` authorizer: based on JWT or OAuth token (not supported for WebSocket APIs)  
    - _request parameter-based_: `REQUEST` autorizer: receives the caller's identity in a combination of headers, query string parameters, stageVariables and $context variables  
  - **Amazon Cognito user pools**: user pools are used to control who can invoke API methods.
    `autorizationType=COGNITO_USER_POOLS`  

# Scaling Architectures

## AWS Service Resilience
- IAM: global, same data globally, accessible from all region
- EC2 and EBS-volumes are in specific AZ (failure of AZ will cause failure of EC2 and volumes)
- EBS snapshots are replicated across AZs (optionally copied cross-region)
- S3 replication is over 3 AZ, S3 is regional service
- R53 operates its name servers from edge locations globally (resistant even to issues in AWS region)
- ELB: regional service, ELB node is placed in specific AZ, ELB place ENI per configured AZ
- VPC: regional service
- NAT gateways operates in specific subnet (single AZ) - place NAT GW in each AZ
- ASG: operates in VPC across subnets in AZs
- VPN - provision 2 IPSEC endpoints (in different AZs)

## Stateless Architectures

## Deciding between Spot, OnDemand and Reserved Instances
🔸**OnDemand**: ad-hoc usage, where you can't calculate future usage patterns

🔸**Reserved**: usage is known and steady-state (base consistent load 24x7), Reserved model can also reserve capacity, providing high-priority startup (e.g for fail-over AZ)

🔸**Spot**: sporadic workloads where you can tolerate interruptions. Have the lowest startup priority

## Auto Scaling Groups
https://linuxacademy.com/cp/courses/lesson/course/2856/lesson/4/module/245

:question:https://aws.amazon.com/ec2/autoscaling/faqs/
https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroupLifecycle.html
https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-simple-step.html
https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-alarms-trigger-actions/
https://aws.amazon.com/premiumsupport/knowledge-center/auto-scaling-troubleshooting/
https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html
https://docs.aws.amazon.com/autoscaling/ec2/userguide/Cooldown.html
https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-suspend-resume-processes.html
https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-instance-termination.html


## ELB
:tv:https://www.youtube.com/watch?v=VIgAT7vjol8
:question:https://aws.amazon.com/elasticloadbalancing/faqs/
https://docs.aws.amazon.com/elasticloadbalancing/latest/network/introduction.html
https://docs.aws.amazon.com/elasticloadbalancing/latest/application/listener-authenticate-users.html
https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html
https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html


# Elastic Beanstalk
:question:https://aws.amazon.com/elasticbeanstalk/faqs/

# S3 & Glacier
:tv:https://www.youtube.com/watch?v=rHeTn9pHNKo
:tv:https://www.youtube.com/watch?v=gidUa4lJd9Y

# Databases

## RDS
:tv:https://www.youtube.com/watch?v=HuvUD7-RyoU

## DynamoDB
:tv:https://www.youtube.com/watch?v=HaEPXoXVf2k
:tv:https://www.youtube.com/watch?v=eTbBdXJq8ss

## Aurora
:tv:https://www.youtube.com/watch?v=2WG01wJIGSQ

# Networking

## VPC
:tv:https://www.youtube.com/watch?v=fnxXNZdf6ew

VPC tenancy modes - Default or Dedicated
Can have only one DHCP Option set

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

## AWS Resource Access Manager (RAM)
📒https://docs.aws.amazon.com/ram/latest/userguide/what-is.html  
📒https://console.aws.amazon.com/ram/home#Setting  
📒https://docs.aws.amazon.com/ram/latest/userguide/working-with-az-ids.html  
📒https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html  

Allows sharing of AWS resources between accounts

AZ-ID - **consistent** between accounts

## VPC Routing
Every VPC comes with a VPC Router, virtual device - CORE network entity responsible for routing traffic between a VPC and other networks.
The VPC router places an interface in the `Network+1` address of every subnet

Every route table starts with one rule - local route (VPC CIDR as destination and Target `local`)

- VPC endpoints use the VPC router
- VPC peering connections use the VPC router

- More specific routes take priority (/32 > /16)

## NACLs
https://en.wikipedia.org/wiki/Ephemeral_port

Stateless security filtering
Can explicitly DENY
Rules processed in order
A SN can have **one** associated NACL
VPCs have a default NACL associated with any subnets in VPC by default
NACL does not impact traffic **inside** SN
Use NACL to limit traffic to services/resources which do not support Security Groups

## Security Groups
Attached to network interfaces
Multiple SG can be associated with an interface and a single SG cna be associated with many interfaces
**All** rules are evaluated at once, no order
There is a **default implicit DENY**
SG cannot **explicitly** deny traffic
SG can reference AWS logical resources

## Egress-Only Gateways
[IPv6 Subnetting - Overview and Case Study](https://community.cisco.com/t5/networking-documents/ipv6-subnetting-overview-and-case-study/ta-p/3125702)

Allows **outbound** only public internet and AWS Public Zone routing

## DNS in a VPC
📒https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resolver.html  

DNS service (R53 Resolver) operates on `Network+2` IP address inside VPC - operates only inside VPC
Resolver can resolve internal DNS names to internal IP addresses (R53 private hosting zone)

**Route 53 Resolver** - regional service that lets you route DNS queries between your VPCs and your network
- Define Inbound- and Outbound-Endpoints
- Endpoints created in 2 AZ (you need 2 SG) for HA

## VPC Flog Logs
📒https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html#flow-log-records  
📒https://docs.aws.amazon.com/athena/latest/ug/vpc-flow-logs.html  

Flow Logs capture network flow **metadata**, not actual data flowing

```
<version> <account-id> <interface-id> <srcaddr> <dstaddr> <srcport> <dstport> <protocol> <packets> <bytes> <start> <end> <action> <log-status>
```
Protocol:
id | Protocol
---|---
1 | ICMP
6 | TCP
17 | UDP
27 | RDP

❗Not logged:
- DHCP
- AWS DNS
- Metadata
- License Activation Requests

Stored in CloudWatch logs or S3 bucket
Each network interface has a unique log stream
Can be on the level:
  - VPC
  - Subnet
  - Network interface
**Not real-time** - delay of several min

FlogLogs can capture _Accepted_, _Rejected_ or _All_ types of traffic

You need to create a new IAM Role for FlogLogs

Ability to create multiple flog logs per interface (e.g. accepted, rejected or all traffic)
Launching EC2 _after_ creating flow logs will automatically create logs for each new NI

Log schema:
```sql
CREATE EXTERNAL TABLE IF NOT EXISTS vpc_flow_logs (
  version int,
  account string,
  interfaceid string,
  sourceaddress string,
  destinationaddress string,
  sourceport int,
  destinationport int,
  protocol int,
  numpackets int,
  numbytes bigint,
  starttime int,
  endtime int,
  action string,
  logstatus string
)
PARTITIONED BY (`date` date)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ' '
LOCATION 's3://your_log_bucket/prefix/AWSLogs/{account_id}/vpcflowlogs/{region_code}/'
TBLPROPERTIES ("skip.header.line.count"="1");
```

https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-troubleshooting.html

`LogDestinationNotFoundException` or `Access Denied for LogDestination` error
- Ensure that you have specified the ARN for an existing S3 bucket, and that the ARN is in the correct format
- If you do not own the S3 bucket, verify that the bucket policy has sufficient permissions to publish logs to it. In the bucket policy, verify the account ID and bucket name

`LogDestinationPermissionIssueException`:  
Amazon S3 bucket policies are limited to 20 KB in size.
Each time that you create a flow log that publishes to an Amazon S3 bucket, we automatically add the specified bucket ARN, which includes the folder path, to the Resource element in the bucket's policy.
Creating multiple flow logs that publish to the same bucket could cause you to exceed the bucket policy limit.

- Clean up the bucket's policy by removing the flow log entries that are no longer needed.
- Grant permissions to the entire bucket by replacing the individual flow log entries with the following:
```
arn:aws:s3:::bucket_name/*
```

## Advanced and Hybrid VPC Networking

### VPC Endpoints
allow access to AWS public services in AWS Public Zone without giving resources public Internet access
Two types of VPC endpoings:
- 🔸**Gateway endpoints**
- 🔸**Interface endpoints**

Attach VPC endpoint policy

You can create multiple endpoints in a single VPC, for example, to multiple services. You can also create multiple endpoints for a single service, and use different route tables to enforce different access policies from different subnets to the same service.

#### Gateway endpoints
Prefix list `pl-xxxxx` ([prefix list](https://docs.aws.amazon.com/vpc/latest/userguide/managed-prefix-lists.html) is representation of CIDR) added to the **route table**
Highly Available
**Not associated with SN or SG**

- S3
- DynamoDB

#### Interface endpoints
**Do not use route tables**  
- Privatelinks
- use ENI created within a **subnet and AZ** - with **a SG associated**
- (Optionally) Enable Private DNS for the endpoint to make it default for that service

Enable Private DNS name can be utilized to change the default service DNS name to use the endpoint inside VPC - removing the need for code changes

Gateway Endpoints | Interface endpoints
---|---
Do not use SG | Use SG
virtual devices | physical devices
do not use ENI | Use ENI
HA | not HA, need to be associated with SN/AZ
do not associated with SN/AZ | associated with SN/AZ
use route table | do not use route table
Do not use DNS | Use DNS. Create unique DNS names for AZ and region. Public service DNS can be resolved to private IP (private DNS must enabled)

### VPC Peering
VPC can be in another account and another region
VPC peering connections are created as logical objects and require route table entries on both sides
SG's can reference SG's in the **same** region
Process to create:
- create connection, accept on the receiving VPC side
- update RTs for requesting and receiving VPC
- update SGs/NACL if needed

All VPC peering connections are fully encrypted

🔹By default, DNS resolves the other VPC names to public IP. You can enable DNS resolution from accepter VPC and from requester VPC - in this case names will be resolved to private IP.

### AWS Site-to-Site VPN
3 components:
1. VGW (attached to a single VPC)
2. CGW
3. VPC connection(s)


VPN connections link the VGW and CGW. They provision tunnel endpoints(2 for HA in different AZ), set authentication and define dynamic BGP or static routing. Dynamic learn remote CIDRs - static VPN must have them set

Virtual private gateways (VGW) are **logical** devices inside VPC - can be used as route targets
Customer gateways (CGW) are **physical** customer routers AND a record inside AWS for there devices. IP address and optionally ASN are provided (Allowing BGP

Different architectures:
(all have the single VGW object, HA by design)

#### No HA
Single tunnel
Single CGW

#### AWS HA
2 tunnel endpoint (in different AZs) - dual endpoint design - you need to provision two IP addresses on the customer side
Requires BGP (dynamic routing)

#### Full HA
2 VPN connections to different CGW (2), each with 2 tunnels in different AZs

### Direct Connect Architecture
📒https://docs.aws.amazon.com/directconnect/latest/UserGuide/getting_started.html  
📒https://aws.amazon.com/answers/networking/aws-multiple-data-center-ha-network-connectivity  

For dedicated connections DX requires **single-mode fiber**: 1Gbps (1000BASE-LX) or 10Gbps
The network device on-prem must support BGP

1. Submit App for DX port (on DX location)
2. Connect your customer router to port on DX location
3. Create **Public** or **Private VIF** associated with VGW and
  - Public VIF allows only to contact the AWS public services **globally**
  - Private VIF is associated with VPC via VGW - **same** region

Connection over DX is **unencrypted**

❗You can use Direct Connect Gateway (DXGW) to connect CGW to multiple VGW in different VPC in different regions

### AWS Transit Gateway
📒https://aws.amazon.com/about-aws/whats-new/2019/12/aws-transit-gateway-supports-inter-region-peering/
📒https://docs.aws.amazon.com/vpc/latest/tgw/what-is-transit-gateway.html

Transit Gateways have multiple route tables allowing complex routing hierarchies to be defined from true hub-and-spoke to edge consolidation and anywhere in between

Compatible with RAM (can be shared between accounts)

# Account and Service Security

## KMS
📒https://en.wikipedia.org/wiki/FIPS_140-2  
📒https://docs.aws.amazon.com/kms/latest/developerguide/importing-keys.html  
📒https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping  
📒https://aws.amazon.com/blogs/security/how-to-protect-the-integrity-of-your-encrypted-data-by-using-aws-key-management-service-and-encryptioncontext/  
📒https://docs.aws.amazon.com/kms/latest/developerguide/grants.html  
📒https://aws.amazon.com/kms/details/#compliance  

KMS is compliant with FIPS 140-2

CMK with imported material:
- Not portable (cannot decrypt a ciphertext encrypted under a CMK by using raw key material outside of AWS KMS)
- Only symmetric CMK are supported  
- No automatic key rotation (only manual)

CMK never leave KMS and never leave a region
Access to CMK is governed by key policies

`GenerateDataKey` creates a encrypted and plaintext data key. The plaintext version is used to encrypt and then discarded. It's never stored in plaintext. The encrypted version is stored along with the encrypted data (envelop encryption).
KMS is used to decrypt the encrypted key, returning plaintext, and the data is decrypted. Encrypt and decrypt perform those functions and are handled by KMS

Key rotation can be automatic (every 3y for AWS managed CMK or once per year for customer managed) where a new backing key is added. Or they can be manual, where a new CMK is generated and an alias is changed.

Role separation with Key Policies

## AWS CloudHSM
📒https://docs.aws.amazon.com/cloudhsm/latest/userguide/initialize-cluster.html

FIPS 140-3

Dedicated HSM which runs within your VPC (via dedicated ENI) (or on-prem connected via DX, Peered or VPN), accessible only to you in a single tenant architecture.
AWS manages and maintains the hardware but has **no access** to the cryptographic component.
Interaction via _industry standards_, **no normal AWS APIs**

**No HA** unless multiple HSM's are provisioned
On-prem - if you **really need to control own physical hardware**

## AWS Certificate Manager
📒https://docs.aws.amazon.com/acm/latest/userguide/acm-concepts.html  

managed service providing **X509 v4 SSL/TLS certificates**. The certificates are asymmetric. One half is private and stored on resources (Servers, Load Balancers) and the other half is public.

ACM is **regional**
KMS is used - certificates are **never** stored unencrypted

Only supported on ELB, CloudFront, Elastic Beanstalk, API Gateway

## AWS Directory Service
Group of products:
- Simple AD
- Microsoft Active Directory (AD)
- AD Connector
- Amazon Cognito
- Amazon Cloud Directory (Graph based store of information)

![Directory Service](../media/directory-service.png)

# Network Security

## AWS WAF and Shield
📒https://docs.aws.amazon.com/waf/latest/developerguide/getting-started-ddos.html
📒https://aws.amazon.com/shield/features/
📒https://docs.aws.amazon.com/waf/latest/developerguide/aws-shield-use-case.html

Ingress traffic > AWS Shield > AWS WAF > Resources (CloudFront, API GW, ELB)

WebACL - rules for **allow/block/count** any traffic which matches them
**regular** or **rate-based** rules

three layers in rules:
conditions > rules > webACL

🔹webACLs contains rules each with their own action, and a default action for any traffic not explicitly matching a rule
🔹webACLs are processed in order and processing stops when a rule is triggered in the ACL

### AWS Shield Standard
- Always on
- Network flow monitoring
- Layer 3 and 4 DDOS attacks
- CloudFront and R53 (automatic protection)
- No cost protection

### AWS Shield Advanced
- Layer 3,4,7
- Application traffic monitoring (layer 7)
- Layer 7 anomaly detection
- CloudFront
- R53 hosted zones
- Global Accelerator accelerators
- ALB
- ELB
- EC2 Elastic IP
- WAF and Firewall Manager is included
- Cost protection
- 24x7 access to DRT (DDoS Response Team)

## AWS GuardDuty
📒https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html

GuardDuty monitors event sources (VPC Flow  Logs, R53 DNS Query logs, Cloudtrail events)
Publish events to Guard Duty console or CloudWatch events
Multiple AWS accounts (via AWS Organisation) can be added to Guard Duty
Requires role permission


# EFS
- Multiple AZs
- Storage classes (Lifecycle Management):
  - Infrequent Access
  - Standard
- can apply lifecycle policies
- throughput for parallel workloads
- for on-prem servers, use **AWS DX or VPN**  
- Linux only
- 2 Performance modes (should be selected at _creation_ time):
  - **General Purpose**: ideal for latency-sensitive use cases
  - **Max I/O**: high level of aggregate throughput, but higher latency. Use for highly parallelized apps
  - monitor `PercentIOLimit`
- 2 throughput modes:
  - **Bursting**: scales as size grows
  - **Provisioned throughput**: independent of the amount of data
  - < 1TB - burst to 100MB/s
  - > 1TB - burst to 100MB/s per 1TB of data stored

❗EFS Mount Targets can be accessed **only on following systems**:
- Amazon EC2 instance in local VPC
- Lambda via VPC
- EC2 instance in VPC having VPC peering with other VPC
- On-prem servers having **AWS DX** or **VPN to Amazon VPC**

❗Security groups attached to mount target should allow inbound connection on NFS port

# EFS Monitoring
https://docs.aws.amazon.com/efs/latest/ug/monitoring-cloudwatch.html

- metric sent at 1m intervals and retained for 15months

## Transit Gateway
:tv:https://www.youtube.com/watch?v=yQGxPEGt%5C_-w
:tv:https://www.youtube.com/watch?v=ar6sLmJ45xs

## VPN
:tv:https://www.youtube.com/watch?v=qmKkbuS9gRs

## DNS
:tv:https://www.youtube.com/watch?v=D1n5kDTWidQ

# Analytics, Streaming, IOT

## Redshift
:tv:https://www.youtube.com/watch?v=TJDtQom7SAA
