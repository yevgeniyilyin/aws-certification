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
**Main limitation** for ACL - all objects created by the account B in the bucket owned by account A, owned by the account B and the account A doesn't have permission to these objects

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
https://aws.amazon.com/ec2/faqs/


# ELB
https://aws.amazon.com/elasticloadbalancing/faqs/
https://www.youtube.com/watch?v=VIgAT7vjol8

# Elastic Beanstalk
https://aws.amazon.com/elasticbeanstalk/faqs/

# Lambda
https://www.youtube.com/watch?v=QdzV04T%5C_kec

# EKS
https://www.youtube.com/watch?v=EDaGpxZ6Qi0

# VMware
https://www.youtube.com/watch?v=RStQrGmHqy0

# S3 & Glacier
https://www.youtube.com/watch?v=rHeTn9pHNKo
https://www.youtube.com/watch?v=gidUa4lJd9Y

# Databases

## RDS
https://www.youtube.com/watch?v=HuvUD7-RyoU

## DynamoDB
https://www.youtube.com/watch?v=HaEPXoXVf2k
https://www.youtube.com/watch?v=eTbBdXJq8ss

## Aurora
https://www.youtube.com/watch?v=2WG01wJIGSQ

# Networking

## VPC
https://www.youtube.com/watch?v=fnxXNZdf6ew

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
https://www.youtube.com/watch?v=yQGxPEGt%5C_-w
https://www.youtube.com/watch?v=ar6sLmJ45xs

## VPN
https://www.youtube.com/watch?v=qmKkbuS9gRs

## DNS
https://www.youtube.com/watch?v=D1n5kDTWidQ

# Analytics, Streaming, IOT

## Redshift
https://www.youtube.com/watch?v=TJDtQom7SAA
