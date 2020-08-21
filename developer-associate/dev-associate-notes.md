# Developer Associate



https://aws.amazon.com/certification/certification-prep

# ELB
https://aws.amazon.com/blogs/aws/new-tls-termination-for-network-load-balancers/
https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-https-listener.html

# Cognito
https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-sync.html


# EC2
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-creating-snapshot.html#ebs-create-snapshot-multi-volume
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-recovery.html
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/capacity-reservation-sharing.html
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ri-market-concepts-buying.html#ri-queued-purchase
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dh-sharing.html
https://aws.amazon.com/blogs/aws/new-trigger-a-kernel-panic-to-diagnose-unresponsive-ec2-instances/
https://aws.amazon.com/blogs/aws/automate-os-image-build-pipelines-with-ec2-image-builder/
https://aws.amazon.com/blogs/aws/new-amazon-ebs-fast-snapshot-restore-fsr/
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-connect-set-up.html


# KMS
https://docs.aws.amazon.com/kms/latest/developerguide/symm-asymm-concepts.html#symmetric-cmks
https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping
https://aws.amazon.com/blogs/security/aws-encryption-sdk-how-to-decide-if-data-key-caching-is-right-for-your-application/

# VPC
https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports
https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/vpc-vpce.html
https://docs.amazonaws.cn/en_us/elasticbeanstalk/latest/dg/vpc-rds.html

# S3
https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock.html
https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-inventory.html
https://docs.aws.amazon.com/AmazonS3/latest/dev/using-access-points.html

# Lambda
https://docs.aws.amazon.com/lambda/latest/dg/configuration-database.html?icmpid=docs_lambda_help
https://docs.aws.amazon.com/lambda/latest/dg/lambda-environment-variables.html
https://docs.aws.amazon.com/lambda/latest/dg/configuration-concurrency.html
https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/automating-updates-to-serverless-apps.html
https://docs.aws.amazon.com/lambda/latest/dg/with-kinesis.html
https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics.html
https://aws.amazon.com/premiumsupport/knowledge-center/lambda-iterator-age/
https://aws.amazon.com/premiumsupport/knowledge-center/lambda-iterator-age/
https://aws.amazon.com/lambda/faqs
https://docs.aws.amazon.com/lambda/latest/dg/API_PublishVersion.html
https://docs.aws.amazon.com/lambda/latest/dg/services-rds-tutorial.html

# API Gateway
https://aws.amazon.com/about-aws/whats-new/2017/11/amazon-api-gateway-supports-canary-release-deployments/
https://docs.aws.amazon.com/apigateway/latest/developerguide/canary-release.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-cors.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-caching.html
https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html
https://aws.amazon.com/blogs/aws/new-usage-plans-for-amazon-api-gateway/

# DynamoDB
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Limits.html#limits-api
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/specifying-conditions.html
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/howitworks-ttl.html
https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchGetItem.html



*** AWS organisations



Whitepapers:
-> Blue/Green Deployments on AWS

******************
EC2
- You can create multi-volume snapshots for ALL EBS volumes attached to an
  EC2 instance (select resource type as Instance in create snapshot dialog),
  up to 40 volumes per instance
- Capacity reservation can be shared with other AWS accounts or within AWS Org
- Automate AMI build pipelines with EC Image Builder
- Fast Snapshot Restore (FSR): can be enabled for new and existing snapshots per AZ


Instance-store backup


AWS compute solutions:
https://aws.amazon.com/products/compute/
Main:
- EC2
- ECS
- Lambda
- Elastic Beanstalk

IAM Policy required fields:
- Effect (Deny, Allow)
- Action or NotAction
- Resource or NotResource

******************
# IAM access reports:
- Credential Report: lists the users and usage of their access keys
- Access Analyzer: monitor access to resources
  (you define zone of trust -> analyser finds access outside ZoT to your resources)
- Organisation activity: service access report for OU or account

******************
# STS
STS API Call returns: Security Token, Access Key ID, Secret Access Key
STS API calls:
- AssumeRole
  -> You can pass a *session policy* parameter to limit the total permissions granted
     by the assumed IAM Role
- AssumeRoleWithWebIdentity
  -> returns temp security credentials for users authenticated using Web identity provider
  -> workflow:    1) signin in web identity provider, get JWT token,
                  2) call AssumeRoleWithWebIdentity, get temp credentials
                    Response: AssumedRoleUser ARN + Set of Temp credentials
                  3) access AWS resources with STS Token

- AssumeRoleWithSAML
- GetFederationToken
- GetSessionToken

When to use STS
- Identity Federation
- Roles for cross-account access
- Roles for EC2

******************
IAM API Keys
- In order for API credentials to work, they must be associated with a USER
- Roles do not have API credentials!!!

******************
KMS
- CMK used to encrypt/decrypt up to 4KB of data
- CMK is the primary resource in KMS
- CMKs generate, encrypt and decrypt the data keys (DEKs)
- CMK: [alias, creation data, desc, state, key material]
- CMK is ALWAYS stays inside KMS, can be NEVER exported, never leaves KMS unencrypted
- Setup: Alias->Desc->Key Material (KMS, Own, CloudHSM)->Admin Permissions->Usage Permissions
- Two types of CMK - AWS-managed (aws/) and Customer-managed
- KMS does not manage or store DATA KEYS
- KMS cannot use DATA KEYS to encrypt data for you
- You must call KMS API to use CMK
- Key deletion is not performed immedeately -> you can set a deletion date
- Envelop Encryption
- AWS Services that integrates with KMS do not support asymmetric keys
- KMS symmectric CMK - 256-bit key
- KMS assymmetric CMK - mathematically related public and private key pair
- KMS supports RSA and ECC assymmetric CMKs

******************
KMS API actions:
- encrypt: aws kms encrypt
- generate data key/envelop key (GenerateDataKey API)
- decrypt
- re-encrypt (without having an original key, e.g. for key rotation)
- key rotation (enable-key-rotation)

******************
AWS Inspector
- automated security assessment service
- network reachability
- EC2 instance security

******************
Cognito
- sign in directly
- web federation
- Components - User pool - user directory in Cognito
- Components - Identity pool - user can obtain temporary AWS credentials
push notifications to sync

******************
API Gateway
- Resource URL
- Stage is added to default API endpoint
- Respond to request: Lambda, HTTP Endpoints, other AWS services
- Deployments -> snapshot of API's resources and methods
- Stages -> dev, prod, beta.
  Specific settings: caching, request throttling, logging, stage variables
- Caching -> set capacity, encryption, TTL, per-key invalidation
- Dashboard -> API calls, Latency, Integraiton Latency, 4xx and 5xx Errors
- Can import API from Swagger 2.0
- Throttling -> by default limits the steady-state request rate to 10000 rps
    Max 5000 concurrent requests accross all APIs within AWS account
    429 Too Many Requests error
- Can configure SOAP Webservice Passthrough (doesn't convert XML!)
- Canary releases - create/promote canary

******************
Lambda
- max timeout 15m
- priced per # of requests and duration
- can be used cross-region
- Version Control:
  Each Lambda version has a unique ARN
  After publishing, the version is immutable (you can edit only $LATEST)
  $LATEST - maintains the latest code, the only you can edit
    Qualified ARN - function ARN + version suffix
    Unqualified ARN - only function ARN, use $LATEST
  Aliases: use to point specific ARN (application use alias incl. $LATEST)
    aliases have static ARN but can point to any version of the same function
    you can use weighted alias to shift traffic between versions
    rollback as easy as updating the version in the alias
- Invocation can be synchronous/asynchronous
- Function configuration:
    Basic settings: Runtime, Handler (filename.functionname), Memory, Timeout
    Monitoring (CloudWatch, X-Ray)
    Permissions
    Enviroment Variables (by default encrypted at rest using KMS, can be encrypted in transit)
    VPC - function can access VPC resources in specifed VPC
    File System - connect to EFS
    Asynchrounous invocation settings + DQL - SQS or SNS
    Concurrency, _provisioned concurrency_ (e.g. for weighted alias)
    Database proxies (manages pool of connections)
- Lambda API Actions:
    AddPermission: add permission to the resource policy to invoke Lambda
    CreateFunction:
    Invoke: synchronous
    InvokeAsync
    CreateEventSourceMapping: identifies a stream as an event source for Lambda
      can be DynamoDB stream or Kinesis
- Lambda metrics:
    Invocation, Performace, Concurrency

SAM Templates


******************
Step Functions
-

******************
X-Ray
- Integrates with ELB, Lambda, API GTW, EC2, Beanstalk

******************
DynamoDB
- 1 WCU: 1 write of 1 item of 1KB or less (round up to nearest KB)
- Writes are applied in the order received
- There are "conditional writes" - only succeed if the attributes meet
- 1 RCU: 1 read of 1 item of 4KB or less (strongly consistent) or 2 reads 4KB per sec for eventually consistent
- Eventual consistent read - 0.5 RCU
- IAM condition parameter: fine-graned access per-item or per-attribute
    dynamodb:LeadingKeys - allow users to access only the items where partition key = user_id
    dynamodb:Attributes - limits access to the specified attributes
    dynamodb:Select
    dynamodb:ReturnValues
    dynamodb:ReturnConsumedCapacity

- Read operations:
    GetItem, BatchGetItem (up to 100 items and max 16MB),
    Use ProjectionExpression to return only selected attributes
    Query - max 1MB per call,
        results are always sorted by Sort Key. Reverse by setting ScanIndexForward
    Scan - max 1MB per, can scan only one partition at a time
        can configure parallel scans by logically dividing a table or index
- No strongly consistent reads on GSI
- Can delete GSI any time, cannot delete LSI

-> TTL
    - Any attribute of your choice
    - In epoch time format

-> DynamoDB Transactions:
    - ACID
    - across multiple tables

-> Atomic Counters:
    writes applied in the order received - can be used to increment existing value
-> Conditional writes
    only succeed if condition is met (ConditionalCheckFailedException if not met
-> CommonErrors:
    ThrottlingError
    ProvisionedThroughputExceededException (number of requests is too high)
    ResourceNotFoundException (e.g. table does not exist/in CREATING)
-> DAX
    write-through
    Eventually consistent only
-> Throttling Issues and Fixes:
    Hot partitions
    Capacity limitations
    Fixes:
      - slowly increase provisioned capacity
      - review capacity on GSI (throttle on index double-counted)
      - implement error retries and exponential backoff (built-in in AWS SDK)
      - distribute operations evently across partitions
      - implement caching solution (ElastiCache or DAX)
      - use TTL on items in the table
    Partitions:
      - Can accomodate only 3000 RCU or 1000 WCU
      - Max 10GB of data
      - never deleted
-> DDB Streams
    - Time-ordered change log for the table, stored for 24 hours
    - Encrypted by default
    - Can trigger Lambda


******************
CloudWatch
- By default EC2 monintoring is 5m, detailed - 1m
- By default store indefinitely (can change the retention period)
- Host-level metrics: CPU, Network, Disk, StatusCheck
- Min granularity for custom metric - 1m
- Alarm can trigger: SNS, Auto scaling action, EC2 action (stop, terminate, reboot)
- Rule can trigger: SNS, SQS, EC2, Lambda

******************
VPC
- VPC endpoints - from CSAA course

******************
ELB
- X-Forwarded-For header to have the client IP on the backend server (via ELB)
- NLB: Layer 4 (Transport), TCP, UDP, TLS, supports STATIC IP, can be assigned Elastic IP per subnet
- ALB: Layer 7 (Application), HTTP/S
- Dynamic Ports (ALB and NLB):
  Single EC2 instance can run multiple containers
  Each of container assigned a random port
  ELB+Target Groups can handle that

******************
S3
- S3 permissions:
A) S3 IAM Policies: attached to Users, Groups, Roles
  Not attached to S3 buckets or objects
  Cannot grant access to anonymous users
  JSON
B) Resource Based:
  ->Bucket policies: attached to S3 bucket (not the objects/user/role)
    Can grant access to anonymous users
    Can restric access based on IP address
    Applied to all bucket-owner objects
    JSON
  ->ACLs: can be used with buckets and objects
    Cannot deny permissions or grant additional permissions
    Manage access to the objects not owned by the bucket owner
    Manage permissions at the object level
    Allow external accounts to manage policies on objects
    XML
- S3 Encryption
 -> In-Transit Encryption (client-side):
  Using a Client-side Master Key:
    CSMK provided by a client, never sent to AWS (only to client-side S3 encryption client)
    S3 client encrypts the data using the DEK (randomly generated by S3 client)
    Material description uploaded as part of the metadata (x-amz-meta-x-amz-key)
  Using a AWS KMS CMK:
    On upload, KMS returns a DEK (plaintext and chipertext to keep with the data)
    Ciphertext uploaded as metadata

 -> At-rest encryption
  SSE-S3: uses S3-managed key
    add x-amz-server-side-encryption request header to upload request
    bucket policies can require all objects to use SSE by requiring that header
    *x-amz-server-side-encryption: AES256*
  SSE-KMS: uses KMS-managed keys
    allows to separate roles
    better auditing of access to data
    *x-amz-server-side-encryption: ams:kms*
  SSE-C: customer manages the keys
- S3 Versioning:
  Versions are full versions of new objects - NOT incremental
- S3 Events:
  can trigger: SNS, SQS and Lambda as destination

- Object locks (WORM):
  "Retention period" or "legal hold" (same as ret. period, but w/o expire)
  "Governance" or "compliance" mode
  only for versioned buckets (versioning enabled automatically)
  can be enabled only for new buckets
  cannot disable object lock or suspend versioning after object lock enabled
  apply to an individual version of an object in a versioned bucket

******************
CloudFront
- caching based off the object name
- in order to serve a new version of the object, create a new obj with new name
- you can set TTL

*******************
ECS
- Use cases:
  Microservices and docker applications
  Batch and ETL Jobs
  CI/CD
- Container Registry - ECR (AWS) or 3rd party
- Fargate - "serverless", manages EC2 instances

Container Def->Task Def->Service->Cluster (Fargate)

-> Task Definition: JSON, blueprint for application:
    Task Role and Task Execution IAM Role
    Network Mode
    Task Memory/CPU
    Volumes
    Containers ->
        -> Container Definition
            Name, image link (repository link)
            Port mappings
            Volume mappings
            Memory/CPU
            Healthcheck
            Environment
-> Task - instance of Task Definition
-> ECS Agent: run on each EC2 node in ECS cluster, reports on running task & resource utilisation
      start/stop
-> Service:
    Launch type (EC2, Fargate)
    # of tasks (desired) ELB (optional)
    Deployment type (rolling update or Blue/Green development)
    Placement type (AZ balanced spread, AZ balanced binpack, BinPack, One task per host, custom)
    Network (VPC), Subnets, Security Groups, PublicIP
    Auto Scaling (min-desired-max)
    Auto Scaling Policy: Target tracking or Step scaling
-> Cluster:
    Templates: Networking only, EC2 Linux + Networking, EC2 Windows + Networking

-> Dockers:
    EC2 Cluster-*EC2 instances-*Task-*Container

-> IAM Roles for ECS Tasks

******************
Elastic Beanstalk
- Manages everything required for less complex application
- Platform as a Service
- Automated provisioning, auto scaling, load balancing, software updates

- Application - logical container
- Environments inside application (environments are transitory)
- Application versions are deployed  to environments
- Environment tiers:
  - Web server environment
  - Worker environment

-> Deployment options:
  - All at once
  - Rolling
  - Rolling with addition batch
  - Blue/Green

*******************
OpsWorks
- Infrastructure management platform
based on Chef configuration management platform

...
# SNS
https://docs.aws.amazon.com/sns/latest/api/CommonErrors.html
https://docs.aws.amazon.com/sns/latest/dg/mobile-push-send-register.html

## SNS
- Public service - needs NAT or IGW or VPC endpoint
- Fully resilient within a region
- Messages up to 256KB

-> Topics: max 256 char, alphanumeric + "-" and "_
    Encryption (optional), AWS KMS
    Access Policy (default access for topic owner)
    Delivery retry policy (to HTTP/S endpoints)
    Delivery status logging (successful/failed deliveries) for Lambda, SQS, HTTP/S, APP

-> Subscriptions:
    Endpoints:
      HTTP/S
      Email, Email JSON
      SQS (Fun-out)
      Lambda
      Platform application endpoint (mobile application)
      SMS
    Filters
    DLQ settings
- SNS Message attributes:
    Name, Value, Type (String, String array, number, binary)

-> Access to SNS resources:
  SNS access control policies:
    - Grant access to SNS topic to another AWS service or account
      API: AddPermission
    - Grant right to some AWS services to publish to SNS topic (many services will use IAM Role instead)
    - Can use IAM Policies and Access Control Policies at the same time

-> Message Data:
    - Message Body:
        Message, MessageID, Signature, SigningCertURL, Subject, Timestamp, TopicARN, Type, UnsubscribeURL
    - Message Attributes:
        Name, Type, Value (useful for SQS and mobile push)

-> Mobile Push with SNS:
    - MPNS Process
    Create a platform application->Add endpoints->Publish messages
    - Can publish additionally a notification to SNS topics (endpoint created, deleted, updated, failure)

-> SNS API and Errors:
  - CreateTopic: Name
  - Publish: Send message: Message
  - Subscribe
  - Unsubscribe

...
# SQS
https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/FIFO-queues.html
https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html

## SQS
- HA
- can be PCI compliant and encrypt messages using KMS
- Polling types:
    - short polling (0s receive message wait time), queries only subset of the SQS servers
    - long polling (1-20s receive message wait time)

- Queue types (has to be selected BEFORE creating queue):
  -> Standard (nearly unlimited throughput, at least once semantic)
    120K  in-flight messages
    Multiple producers and multiple consumers

  -> FIFO (exactly once, soft limits 3000 mps with batching or 300 without by default)
    Multiple producers, btu multiple consumers only through Group IDs
    20K in-flight messages
    The name of FIFO queue must end with .fifo
    Supports message groups - allow multiple ordered message groups within a queue

- Message up to 256KB (but can link S3)
- Up to 10 metadata attributes (outside of message body)
-> Message Components:
    - Body
    - ReceiptHandle - allows to delete message after processing
    - Metadata attributes (up to 10)
    - VisibilityTimeout
    - DelaySeconds

- Can have attached resource policy (access policy)

-> Queue configuration:
    - Visibility timeout (0s-12h)
    - Delivery delay (0s-15m): delay each message coming to the queue -> Creates **Delay Queue**
    - Receive message wait time (0-20s): max time polling will wait for messages
    - Message retention period (1m-14d)
    - Max message size (1-256KB)
    - Access policy -> defines who can send and who can receive
    - DLQ
- Use URL to access

- ApproximateNumberOfMessages -> metric for ASG

- Managing Access to SQS
  - same as with SNS









Cognito Lab
https://ilyiny.auth.us-east-1.amazoncognito.com/login?response_type=token&client_id=2jouo3k0r6plg6it4n5fg8qilv&redirect_uri=https://example.com
London123&

https://example.com


Inline Policies vs Managed Policies vs Custom Policies

- Managed Policies: provided by AWS, cannot be changed, common use cases based on job functions
- Custom Policies: you create and adminster, recommended to create on basis of Managed Policy
- Inline Policies: _embedded_ within the user, group, or role

Maintain Sessionstate with ELBs:
- Option 1: ELB generated cookie stickiness
- Option 2: Application generated cookie stickiness
- Option 3: Non-ELB (not recommeded):
            using caching services like ElastiCache to store session state
