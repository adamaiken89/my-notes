# AWS Cloud Practitioner

## Account

### Free vs Paid

- Both require credit card input
- Free (6-month) - $200 credits
- Paid (deducts usage immediately)

## Cloud Resources Overview

- **Server**: CPU (Processing), RAM (Store Stuff)
  - Elastic Cloud Compute (EC2)

- **Storage**: Data (Store permanently)
  - Elastic Block Storage (EBS)

- **Database** (More structured)
  - Amazon RDS - Relational Database Service

- **Network**: Cables, Routers, Switches (Get access)
  - ELB, Route 53

## Cloud Computing Fundamentals

### NIST Characteristics

- **Resource Pooling** - shared resources
- **On-demand self service**
- **Measured Service**
- **Rapid Elasticity**
- **Broad network access**

### Deployment Models

- **Private Cloud**: Single cloud - private organization / security
- **Public Cloud**: Azure / Google Cloud / AWS (shared hardware resource)
- **Hybrid Cloud**: In between

### Advantages

- CAPEX to OPEX
- Economies of scale
- Stop guessing capacity at first
- Increase speed and agility
- Stop spending money running and maintaining data center
- Go global in minutes

## Types of Cloud Computing

- **IaaS** (Infrastructure as a Service): Rent the hardware - EC2
- **PaaS** (Platform as a Service): Control hardware through UI - Elastic Beanstalk
- **SaaS** (Software as a Service): Client application - Rekognition, Gmail, Dropbox

## Pricing

- **Compute** -> running time
- **Storage** -> stored
- **Network** -> leave the cloud (same AZ)

## AWS Architecture

**Hierarchy:**

- **Regions** (eu-west-3) - usually 3, 3~6 zones
- **Availability Zones** (1 or more DCs) - eu-west-3a
- **Data Centers**

**Edge Locations / Points of Presence** - CDN cache / S3

### Choose a Region

- **Compliance** - govt
- **Proximity** - client distance
- **Available services** - you need
- **Pricing** - cheaper?

### Global Services

- IAM (Identify and Access Management)
- Route 53 (DNS Service)
- CloudFront (CDN Cloud Delivery Network)
- WAF (Web Application Firewall)

### Region-Scoped Services

- EC2
- Beanstalk
- Lambda
- Rekognition

## Shared Responsibility Model

**AWS**: Security of the Cloud, something you can't configure
**Customer**: Security in the Cloud, your configuration

### Acceptable Use Policy

> Illegal, no misuse, no spamming

## IAM (Identity and Access Management)

**Root Account** - create users & groups

**Group Structure:**

- No groups of groups
- Only groups of users
- Users can be not in any groups, or multiple groups

**JSON Policies:**

```json
{
  "Version": "2025-01-01",
  "Id": "ID", // Optional
  "Statement": [{
    "Sid": "SID", // Optional
    "Effect": "Allow", // Deny
    "Principals": {
      "AWS": ["aaaaaa"]
    },
    "Action": [
      "elasticloadbalancing:Describe"
    ], // or "*"
    "Resource": "*" // *
  }]
}
```

### Access Methods

#### Sign in

- With account alias
- Root account does not have IAM id on the top right menu
- Multi-session support - impersonation

#### Management Console

- CloudShell
- CLI - access key
- SDK - access key

#### AWS Configure

```bash
aws configure
Access Key ID
Key
Region
```

#### Roles

- Temporary access

## Databases

### SQL Databases

**RDS:**

- No SSH
- Read Replica / Multi-AZ

**Aurora:**

- From AWS - claim better (more expensive than RDS but claim efficient)
- Cloud-native
- Aurora Serverless (alternative) - with proxy fleet

### NoSQL Database

**DynamoDB:**

- NoSQL
- Serverless
- Partition Key / Sort Key (optional)
- Column data (schemaless attributes)
- DynamoDB Accelerator - DAX (Cache)
- Global Tables - low latency for multiple regions
- Active-Active replication

### Data Warehousing

**Redshift:**

- PG for OLAP
- Columnar DB
- Massively Parallel Processing (MPP)
- SQL
- BI tool integration
- Redshift Serverless

**EMR (Elastic Map Reduce):**

- Hadoop
- Auto-scaling with spot instance

### Other Data Services

- **Athena** - search S3 with SQL
- **QuickSight** - Serverless dashboard
- **DocumentDB** - Document database (MongoDB compatible)
- **Neptune** - graph db
- **Timestream** - time-series db
- **Management Blockchain** - blockchain
  - Hyperledge fabric & ETH
- **Glue** - serverless ETL, Glue data catalogue
- **DMS** - Database Migration Service - migrate database (online)
  - Homogeneous / heterogeneous -> same db vendor / not

### Caching

**ElastiCache** - Redis / Memcached

- Reduce DB load

## Containers

**ECS - Fargate - ECR:**

- ECS (Need EC2)
- Fargate (no need)

**EKS:**

- Horizontal scaling on pool instance

**Lambda Container Image:**

- ECS / Fargate - customise image

## Serverless

**AWS Lambda** - Serverless Function as a Service

### API Gateway

- Connect to AWS services and trigger Lambda functions

**AWS Batch (Container):**

- ECS / spot instance (EBS) - Managed service
- Alternative to Lambda for batch processing workloads

**LightSail** - simplified cloud computing for users with minimal cloud knowledge

## Infrastructure as Code

**AWS CloudFormation** - IaC

**CDK** - programming language way to create infra

**Beanstalk** - PaaS

## Web Architecture

**Web App 3-tier:**

```text
ELB -> ASG (EC2) -> RDS / ElastiCache
```

## CI/CD

- **CodeDeploy** - Continuous deployment
- **CodeCommit** - Git-based version control (no longer available)
- **CodeBuild** - Continuous integration
- **CodePipeline** - Link steps in CI/CD workflows
- **Code Artifact** - Software package repository

## Management

**Systems Manager (SSM):**

- Manage EC2 & On-Premise
- Patching services & run commands & store configurations
- **SSM Session Manager** -> Secure Shell to instance
- **SSM Parameter Store** -> Store configurations

**Budgets** - spend too much money / too little money - alerts

## EC2

**Instance Configuration:**

- CPU / Memory
- Storage
- Security Group
- Network card
- Bootstrap script

**Instance Type Family:**

- Compute / memory / general purpose / networking / storage
- Example: M5.x2large
  - M = instance class
  - 5 = generation
  - 2xlarge = size

**Security Group:**

- In & out
- Allow only IP or another security group
- SSH access for one security group
- Blocked all inbound by default
- Allow all outbound by default

**SSH Access:**

- Mac / Linux / Windows 10
- PuTTY - all Windows versions
- Session Manager - all platforms

### Pricing Models

- **On-demand**
- **Reserved:** 1 or 3 years / flexible instance type, upfront (all / partial / no)
- **Convertible**
- **Sellable / buyable** in market place
- **Saving:** 1 or 3 years

**Spot instance** - for cost savings
**Dedicated hosts** - servers
**Dedicated instances**
**Capacity reservations**

## Storage

### EBS (Elastic Block Storage)

**Characteristics:**

- Network drive
- Bounded to AZ
- Can mount to only one EC2 while EC2 can have several EBSs

**EBS Snapshot:**

- Backup
- Archive - slow restore 24 to 72 hours
- Recycle bin

### AMI (Amazon Machine Image)

**Types:**

- Vendor / customised / amazon
- Specific region

### Image Builder

### EC2 Instance Store

Local temporary storage

### EFS (Elastic File System)

- 100s x EC2
- Linux and AZ

### FSx

- Support windows

## Key Concepts

**HA** - High Availability
**Scalability** - handle more load
**Elasticity** - adjust capacity

## VPC (Virtual Private Cloud)

**Components:**

- VPC
- Subnet
- Internet Gateway
- NAT Gateways

**Security & Monitoring:**

- Security Groups
- NACL (Network Access Control List)
- VPC Flow Logs
- VPC Peering, VPC Endpoints
- Site to Site VPN, Direct Connect
- Transit Gateway

### IP Addresses

**IPv4:**

- Public IPv4 - internet use
- Private IPv4 - private networks
- Elastic IP -> for public IPv4, to EC2

**IPv6:**

- All public and free

### VPC Architecture

**VPC** - Virtual Private Cloud

- Private network to deploy your resources (regional)
- Subnets (AZ)

**Subnet Types:**

- Public subnet <-> access from internet
- Private subnet <-> no access from internet

### Connectivity

**Internet Gateway** <-> public subnet (route)

**NAT:**

- NAT Gateways (AWS) <-> private subnet to internet
- NAT instance (self) <-> private subnet to internet

### Security

**NACL:**

- Subnet firewall
- Stateless
- IP Address for allow & deny

**Security Groups:**

- Instance firewall
- Stateful
- Allow out by default
- IP Address or Security Group for allow

**VPC Flow Logs:**

- VPC Flow Logs
- Subnet Flow Logs
- Elastic Network Interface Flow Logs

### Inter-VPC Connectivity

**VPC Peering:**

- Connect TWO VPCs
- Treat as the same network
- Not transitive (must be established for each VPC)
- `A <-> B & B <-> C DOES NOT imply A <-> C`

**VPC Endpoints:**

- Connect to AWS Services through a private network
- VPC Endpoint Gateway: S3 & DynamoDB
- VPC Endpoint Interface: Most Services

**AWS Private Link:**

- Scale & More Secure VPC Peering

### Hybrid Cloud Connectivity

**Site-to-Site VPN & Direct Connect:**

- S2S VPN -> encrypted & goes over public internet & bandwidth
- Customer Gateway (On Prem) & Virtual Private Gateway (AWS subnet)
- DX -> physical connection & private & private network & expensive & at least a month to establish

**Client VPN:**

- Computer to (OpenVPN) private network and on premises
- To EC2 with private IP
- Public Network

**Transit Gateway:**

- Share thousands network

## AWS Organization

**Benefits:**

- Consolidated billing single payment
- Pricing benefit from aggregated usage
- Share reserved instances

**Governance:**

- Restrict account using service control policies

**Multi account vs one acct with VPC:**

- Tag resources

**Control Tower:**

- On AWS organisation
- Set up env on AWS

**AWS Resource Access Manager:**

- Share resources across accounts

**Service catalogue** - Pre-approved IT services catalog

### Compute Optimize

- EC2
- Lambda
- EBS
- ASG

## Billing & Cost Management

**Pricing Calculator** - cost estimate before starting

**Billing dashboard:**

Cost Allocation Tags:

- AWS-generated tags (start with "aws:")
- User-defined tags (start with "user:")

Reports:

- Cost and Usage Reports - comprehensive detailed reports
- Cost Explorer - high-level visual reports

Billing metrics on US-EAST-1

**Budgets** - set spending thresholds and alerts

## S3 (Simple Storage Service)

**Basics:**

- Unique name on AWS
- Object based & key

**Limits:**

- Max is 5TB
- \> 5GB, need to use multi-part upload

### Access Control

**Types:**

- User-based - IAM
- Resource Based - Bucket Policies
- Object Access Control List
- Bucket Access Control List

**Bucket Policy Example:**

```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "PublicRead",
    "Effect": "Allow",
    "Principal": "*",
    "Action": ["s3:GetObject"],
    "Resource": ["arn:aws:s3:::example-bucket/*"]
  }]
}
```

### Versioning & Replication

**Versioning:**

- Bucket level versioning - unintended deletes - rollback

**Replication:**

- CRR - Cross Region Replication
- SRR - Same Region Replication

### Storage Classes

- Standard - General Purpose
- Standard Infrequent Access
- One Zone Infrequent Access
- Glacier Instant Retrieval
- Glacier Flexible Retrieval
- Intelligent Tiering
- S3 Express One Zone

### Encryption

- Server Side Encryption (default)
- Client Side Encryption (SDK)

### Storage Gateway

**AWS Snowball** - Migration S3

**Hybrid Storage with Storage Gateway:**

- Block - EBS / Instance Store
- File - EFS
- Object - S3 / Glacier

**Types:**

- File Gateway
- Volume Gateway
- Tape Gateway

## Content Delivery & Networking

### Route 53

DNS Service

`A Record` -> hostname to IP

**Routing Policies:**

- Simple Routing Policy
- Latency Routing Policy
- Failover Routing Policy
- Weighted Routing Policy

### CloudFront

**CDN:**

- Cache content to improve READ performance at the EDGE
- Prevent DDoS with WAF and Shield

**Origin Types:**

- **S3 Bucket** - Secure by Origin Access Control
- **VPC Origin** - In VPC private subnets (ALB / NLB / EC2)
- **Custom Origin (HTTP)** - S3 Website, any public HTTP backend

### S3 Transfer Acceleration

- For upload / download
- Upload to Edge Location uses AWS's internal network to reach the target region

### AWS Global Accelerator

**AWS Global Accelerator:**

- Available & Performance by routing to Edge location

**VS CloudFront:**

- Both use Edge Locations
- Support DDoS protection with Shield

**CloudFront:**

- Served at the edge
- Cacheable content

**Global Accelerator:**

- No caching & proxying only
- Improve TCP or UDP

### AWS Outposts

- Hybrid cloud -> physical security from AWS
- Server racks

**Supports:**

- EC2
- EBS
- S3
- EKS
- ECS
- RDS
- EMR

### Wavelength

- Edge of the 5G network
- Carrier Gateway
- Low-latency
- wavelength zone from Available Zone

**Local Zones:**

- Extension of an Region
- With VPC

## Cloud Integration

**Services:**

- **SQS** - queue
- **SNS** - pub-sub
- **Kinesis** - data streams (big stream)
- **Amazon MQ** - Message Broker

## Security & Protection

**Shared Responsibility:**

- AWS - Security of the Cloud
- Customer - Security in the Cloud

**Shared:**

- Patch Management / Configuration Management / Awareness and Training
- WAF & Shield

### DDoS Protection

**Shield:**

- Protect from DDoS
- Shield Standard - provides
- Shield Advanced

**AWS WAF:**

- Filter specific requests based on rules

**CloudFront & Route 53:**

- Protect using global edge network

### Firewall

**WAF - Layer 7:**

- ALB, API gateway, CloudFront

**Define Web ACL rules for:**

- IP
- SQL inject, XSS
- Country block
- Rate based block

**AWS Network Firewall:**

- Layer 3 to 7
- VPC to VPC
- Outbound to internet
- Inbound from internet
- To / from Direct Connect / Site-to-Site VPN

**AWS Firewall Manager:**

- Manage security rules in Organisation

### Penetration Testing

- Without prior approval
- < 8 services

**Services:**

- EC2, ELB, NAT Gateway
- RDS
- CloudFront
- Aurora
- API Gateways
- Lambda / Lambda Edge functions
- Lightsail
- Beanstalk

## Encryption & Key Management

**Data at rest vs Data at transit:**

- At rest <- store
- Transit

### CloudHSM

- Encryption hardware
- Dedicated hardware

### KMS (Key Management Service)

- Manage the key for us

**Opt-in:**

- EBS
- S3
- Redshift
- RDS
- EFS

**Automatically:**

- CloudTrail Logs
- S3 Glacier
- Storage Gateway

**KMS Keys:**

- Customer managed key
- AWS Managed key
- AWS Owned Key
- CloudHSM Keys

### AWS Certificate Manager (ACM)

- Manages SSL/TLS Certificates

### Secrets Manager

- Store and manage secrets securely
- Force rotation of credentials
- Automate secret generation
- Primarily designed for RDS database credentials

## Compliance & Monitoring

**Artifacts:**

- Compliance documentation and reports from AWS

### GuardDuty

- For AWS Account
- Intelligent threat discovery with Machine Learning

### Amazon Inspector

- Security Assessment
- EC2
- Container Image security, package vulnerabilities CVE

### AWS Config

- Trace configuration change

### Amazon Macie

- S3 security, PII

## Machine Learning

### AI Services

- **Rekognition** - image and video analysis with machine learning
- **Transcribe** - speech to text
- **Polly** - text to speech
- **Translate** - text translation between languages
- **Lex & Connect** - AI-powered chatbots
- **Comprehend** - serverless NLP for text analysis and summary
- **SageMaker** - build and deploy machine learning models
- **Kendra** - intelligent document search with ML
- **Personalize** - build real-time recommendation systems
- **Textract** - extract text and handwriting from documents

## Security & Identity Services

**AWS STS** - Security Token Service

- Creates temporary credentials

**Cognito** - Single sign-on for application users

**Directory Service** - Managed Microsoft Active Directory

**IAM Identity Center** - AWS Single Sign-On (formerly known as  AWS SSO)

- SSO for AWS and cloud applications

## Desktop & End User Computing

**Amazon WorkSpaces** - Desktop as a Service (DaaS)

- Virtual Desktop Infrastructure, requires client app

**AppStream 2.0** - Web-based streaming workspace

## IoT & Mobile

**AWS IoT Core** - connect IoT devices to AWS Cloud

**AppSync** - GraphQL service for mobile & web apps with real-time data sync

**Amplify** - development platform similar to Google Firebase

## Development Tools

**AWS Application Composer** - Drag and drop IaC tool

- Works with CloudFormation

**Device Farm** - test apps on real browsers and mobile devices

**AWS Backup** - centralized backup management for AWS services

**Amazon CodeGuru** - automated code review and profiling

## Security Tools

**AWS Security Hub:**

- Central security tool across AWS accounts

**Amazon Detective** - deeper security analysis integrating Macie, GuardDuty, and Security Hub

**AWS Abuse Team** - report abusive activities

### Root User Privileges

Root account has complete access to all AWS services and resources

## Monitoring & Logging

- **CloudTrail** - Logs AWS account API activity
- **CloudWatch** - Logs, metrics, and alarms
- **EventBridge** - Event-driven automation and serverless event bus
- **AWS Health Dashboard** - Account health and service issues

## AWS Well-Architected Framework

**Six Pillars:**

1. Operational Excellence
2. Security
3. Reliability
4. Performance Efficiency
5. Cost Optimisation
6. Sustainability

**OSR PCS** (mnemonic)

## Cloud Adoption Framework

**Perspectives:**

1. Business Perspective
2. People Perspective
3. Governance Perspective
4. Platform Perspective
5. Security Perspective
6. Operations Perspective

### Domain Focus

- Technology
- Process
- Organization
- Product
