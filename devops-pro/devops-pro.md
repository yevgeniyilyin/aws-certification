# DevOps Engineer Professional

![CDOE badge](../media/cdoe-badge.png)

# Sample Questions
📙https://d1.awsstatic.com/training-and-certification/docs-devops-pro/AWS-Certified-DevOps-Engineer-Professional_Sample-Questions.pdf

# Exam Guide
📙https://d1.awsstatic.com/training-and-certification/docs-devops-pro/AWS-Certified-DevOps-Engineer-Professional_Exam-Guide.pdf

Training course notes

# Whitepapers
📗https://d1.awsstatic.com/whitepapers/AWS_DevOps.pdf
📗https://d1.awsstatic.com/training-and-certification/docs-devops-pro/running-containerized-microservices-on-aws.pdf  
📗https://d1.awsstatic.com/training-and-certification/docs-devops-pro/microservices-on-aws.pdf  
📗https://d1.awsstatic.com/training-and-certification/docs-devops-pro/infrastructure-as-code.pdf  
📗https://d0.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf  
📗https://d1.awsstatic.com/whitepapers/DevOps/import-windows-server-to-amazon-ec2.pdf  
📗https://d1.awsstatic.com/whitepapers/AWS_Blue_Green_Deployments.pdf  
📗https://d1.awsstatic.com/whitepapers/AWS_DevOps.pdf  
📗https://d1.awsstatic.com/whitepapers/aws-development-test-environments.pdf  


# LinuxAcademy Courses
📒https://linuxacademy.com/cp/modules/view/id/494  


## Jenkins on AWS
https://aws.amazon.com/blogs/devops/setting-up-a-ci-cd-pipeline-by-integrating-jenkins-with-aws-codebuild-and-aws-codedeploy/


# Whiteboard Sessions (LinuxAcademy)
https://linuxacademy.com/cp/modules/view/id/494

## OpsWorks Scenarios

Ops Stack with layers
🔹Latest AMI deploy with minimal or no downtime:
  - create a parallel new stack (blue/green deployment)

🔹Variable traffic:
  - Automatic scaling:
    - Time based
    - Load-based instances (CPU utilization) for unpredictable load
  - 24/7 instances for base load

# Elastic Beanstalk
📒https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environment-configuration-methods-before.html  
📒https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.CNAMESwap.html  
📒https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.managing.as.html  
📒https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.managing.db.html  

Manages everything required for less complex application
Platform as a Service
Automated provisioning, auto scaling, load balancing, software updates

Application - logical container
Environments inside application (environments are transitory). Environment has only one version running
Application versions are deployed  to environments
Two types of Beanstalk environments:
  - _Web server environment_: serve web applications on the Internet
  - _Worker environment_: use in background SQS processing for decoupling applications
Environments are deployed via CloudFormation stack (behind the scenes)

[Deployment options](https://blog.shikisoft.com/which_elastic_beanstalk_deployment_should_you_use/):   
  **All at once**  
  starts deployment on all instances. Downtimes are possible. Suitable for dev or test environments

  **Rolling**  
  deploy one by one. If deployment fails, only failed instance will be effected

  **Rolling with additional batch**  
  launch new instances (batch) and first deploy on them - maintains full capacity during deployments

  **Immutable**  
  launch a completely new set of instances, deploy the new version and terminate old instances.
  The new set is launched in a separate temporary ASG first and then transferred to the original ASG and terminates the temporary ASG

  **Traffic-splitting**  
  canary testing as part of application deployment. Full set of new instances (like immutable deployment). Forward a specified percentage of client traffic to the new version.

  **Blue/Green**  (not on Elastic Beanstalk deployment type list)  
  replicates the current environment (including ELB and ASG) and redirect the traffic to the new environment
  1. Clone current environment (or launch a new environment)
  2. Deploy the new application version to the new environment
  3. Test
  4. In **Environment actions** choose **Swap environment URLs**  
  Elastic Beanstalk swaps the CNAME records

![Deployment options](../media/deployment-methods.jpg)  

Elastic Beanstalk Supports two methods of saving configuration option settings:
  - config files in YAML or JSON in `.ebextensions` folder
  - saved configurations created from a running environment or JSON option file

Elastic Beanstalk creates Auto Scaling Group to manage EC2 instances. You can modifiy the **launch configuration** to change the instance type, key pair, EBS, and other settings.  
You can include a YAML [environment manifest](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environment-cfg-manifest.html) in the root of the application source bundle to configure the environment name, solution stack and [environment links](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/environment-cfg-links.html) to use.  

You can use Packer to create a custom platform  

# AWS Landing Zone
📒https://aws.amazon.com/solutions/implementations/aws-landing-zone/  

Help to quickly setup a secure, multi-account AWS

Components:

- _Multi-account structure_ - four accounts are deployed:
  - _AWS Organisations_ account:
    - S3 bucket and pipeline
    - account configuration StackSets
    - SCPs
    - AWS SSO
  - _Shared Services_ account
    - infrastructure shared services like AD and SSO integration in shared VPC
    - VPC can be automatically peered with new AWS accounts
  - _Log Archive_ account
    - a central S3 bucket for storing copies of all AWS CloudTrail and AWS Config files
  - _Security_ account
    - auditor (RO) role
    - administrator (full access) role
    - Amazon GuardDuty + Security Hub (master)
    - SNS security notifications
- _Account Vending Machine (AVM)_
  provided as **AWS Service Catalog** product which allows customers to create new AWS accounts in OUs preconfigured with account security baseline, and a predefined network baseline
- _Security Baseline_
  - CloudTrail
  - AWS Config
  - AWS Config Rules
  - IAM (password policy)
  - Cross-Account Access (admin access to the accounts from the security account)
  - VPC
  - AWS Landing Zone Notifications (e.g. on root account login, console sign-in failures, API authentication failures)
  - Amazon GuardDuty
- _User Access_
  - AWS SSO (with AWS SSO Directory) or
  - Federated Access to AWS Accounts
- _Notifications_
  - Aggregated security notifications (GuardDuty master)
  - Local Security Notifications


# AWS Control Tower
📒https://aws.amazon.com/controltower/  
:question:https://aws.amazon.com/controltower/faqs/  

- Multi-account AWS environment implementing best practices
- All created accounts aligned with centrally established, company-wide polices
- AWS Control Tower automates the creation of a landing zone with best-practices blueprints

![aws-control-tower](../media/aws-control-tower.png)

- You can use your existing AWS Organisations mater account with AWS Control Tower and enroll existing linked accounts


# AWS CDK

# VPC Network Infrastructure

## VPC to VPC connectivity
📗https://d1.awsstatic.com/whitepapers/building-a-scalable-and-secure-multi-vpc-aws-network-infrastructure.pdf  

### VPC Peering
No transitive routing
Max 125 peering connections per VPC
No bandwidth limits
Supports placement groups
Lowest latency
_Layer 3 IP connectivity between VPCs_  

❗If you are using VPC peering, on-premises connectivity (VPN and/or Direct Connect) must be made to each VPC. Resources in a VPC cannot reach on-premises using the hybrid connectivity of a peered VPC

VPC peering is best used when resources in one VPC must communicate with resources in another VPC, the environment of both VPCs is controlled and secured, and the number of VPCs to be connected is less than 10 (to allow for the individual management of each connection). VPC peering offers the **lowest overall cost** when compared to other options for inter-VPC connectivity

### Transit VPC Solutions
Hub and spoke design
limited throughput per VPC (1.25 Gbps per VPN tunnel)
3rd party routing/networking solutions (e.g. Cisco) is running in the Transit VPC (on EC2 instances)
- Need to ensure HA/DR/FT for EC2 instances with the solution


### Transit Gateway
You can attach all hybrid connectivity (VPN or DX connections) to a single Transit Gateway
Gateway controls traffic using route tables
Regional resource, can be multiple Transit Gateway per Region
_Layer 3 IP connectivity between VPCs_  

- place your organisation's Transit Gateway in its Network Account
- Use AWS Resource Access Manager (RAM) to share a Transit Gateway for connecting VPC across multiple accounts
- **50 Gbps** bandwidth per AZ
- Transit Gateway has an hourly charge per attachment + data transfer fees
- Supports both BGP and static VPN connections
- Supports VPN consolidation on a single Transit Gateway. 1.25 Gbps single-VPN tunnel bandwidth can be aggregated with ECMP

### AWS PrivatLink

![aws-privatelink](../media/aws-privatelink.png)

**Use AWS PrivateLink** when you have a client/server set up where you want:
- to allow one or more consumer VPCs _unidirectional_ access to a specific service or set of instances in the service provider VPC. Only the clients in the consumer VPC can initiate a connection to the service in the service provider VPC.
- when client and servers in the two VPCs have overlapping IP addresses as AWS PrivateLink leverages **ENIs** within the client VPC such that there are no IP conflicts with the service provider.

You can access AWS PrivateLink endpoints over VPC Peering, VPN, and AWS Direct Connect.

### Amazon VPC Sharing
📒https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html
Sharing VPCs is useful when network isolation between teams does not need to be strictly managed by the VPC owner, but the account level users and permissions must be.
With Shared VPC, multiple AWS accounts create their application resources (such as Amazon EC2 instances) in shared, centrally managed Amazon VPCs.

## Hybrid Connectivity

Approaches:
1. **One-to-one Connectivity** - a VPN connection and/or Direct Connect private VIF is created for every VPC  
2. **Edge Consolidation** -

### VPN

![vpn-termination-options](../media/vpn-termination-options.png)

❗VPN throughput to a VPC via VGW is limited to 1.25 Gbps and ECMP load balancing is not supported

### Direct Connect
Consistent, low latency, high-bandwidth dedicated fiber connectivity between customer data centers and AWS

![direct-connect-termination](../media/direct-connect-termination.png)

Connection options:
1. **Private VIFs to VGW**:  
  - up to 50 VIFs per DX connection, connecting up to 50 VPCs
  - one BGP peering per VPC
  - connectivity is restricted to the Region of DX location
2. **Private VIF to Direct Connect Gateway associated with multiple VGWs**:  
  - each VGW is attached to VPC
  - up to 10 VGWs per Direct Connect Gateway per account
  - global access
  - one BGP peering per Direct Connect Gateway per Direct Connect connection
  - not transitive via Direct Connect Gateway (north/south traffic flow only)
3. **Transit VIF to a Direct Connect Gateway associated with Transit Gateway**:  
  - up to 3 Transit Gateways over one VIF and BGP peering
4. **VPN connection to Transit Gateway over Direct Connect public VIF**:  
  - access all AWS public services and endpoints using the public IP addresses

For HA you should have at least two connections at two different Direct Connect locations (total 4 connections)
You also create VPN as a backup connectivity

Hybrid connectivity sample reference architecture:
![hybrid-connectivity-sample](../media/hybrid-connectivity-sample.png)


## Centralized egress to internet
As you deploy applications in your Landing Zone, many apps will require outbound only internet access (for example, downloading libraries/patches/OS updates). You can achieve this preferably by using a network address translation (NAT) gateway, or alternatively an EC2 instance (configured with Source NAT(SNAT)) as the next hop for all egress internet access. Internal applications reside in private subnets, while NAT Gateway/EC2 NAT instances reside in a public subnet.

![centralized-egress-vpc](../media/centralized-egress-vpc.png)

## Centralized network security for VPC-to-VPC and on-premises to VPC traffic

AWS provides **security groups** and **NACLs** to implement network security  
Both are **Layer 4** firewalls  

## DNS

![hybrid-dns](../media/hybrid-dns.png)

![hybrid-dns-centralized](../media/hybrid-dns-centralized.png)

## Centralized access to VPC private endpoints
VPCE:
- Interface Endpoints (powered by AWS PrivateLink)
- Gateway Endpoints

### Interface VPC endpoints
- one or more ENI with private IP (serves as entry point for traffic to a supported AWS service)
- billed by an hour the endpoint running
- create an interface endpoint in every VPC from which you want to access the AWS service (small number of VPC) **or**  
- host the interface endpoints in one centralized VPC
- can enable Private DNS (creates R53 private hosted zone PHZ) - enables resolution of public AWS service endpoint to private IP of the interface endpoint. **managed PHZ only works within the VPC with the interface endpoint**  

![vpc-endpoints-centralized](../media/vpc-endpoints-centralized.png)




























------
