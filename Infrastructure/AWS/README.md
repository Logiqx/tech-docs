# AWS - WIP

## Background

The full range of [AWS Products](https://aws.amazon.com/products) is listed on the AWS website.

The London [AWS Summit](https://aws.amazon.com/events/summits/london/) is held in May every year.



## Core Products

### Virtual Private Cloud (VPC)

Provision a logically isolated section of the Amazon Web Services (AWS) Cloud where you can launch AWS resources in a virtual network that you define.

I script the creation of my VPC resources using [Terraform](../IaC/Terraform.md).



### Compute Capacity

I have a separate page for Amazon [Compute](Compute.md) products - [EC2](EC2.md), [ECR](ECR.md), [ECS](ECS.md), EKS, Fargate, Lightsail, etc.

Using the [Instance Scheduler](https://aws.amazon.com/solutions/instance-scheduler/) solution can keep costs to [Reduce Amazon EC2 or RDS costs](https://medium.com/@ahmeeddhon/using-aws-instance-scheduler-to-reduce-amazon-ec2-or-rds-cost-56c9eb374344).



### Network Services

#### NAT

Private subnets require [Network Address Translation (NAT)](Nat.md) for internet access.

This can be achieve with a NAT Gateway or NAT Instance.

#### Elastic IP

An [Elastic IP Address](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-eips.html) can be used as a fixed IP address for an EC2 instance (or other AWS resource).

They are free when attached to a running instance but cost money while the instance is shut down.

#### Route 53

The [Route 53](Route_53.md) is Amazon's Domain Name System (DNS).

For my personal domains, I use it to route website traffic to AWS and e-mail traffic to eUKhost.

#### VPC Endpoints

The use of Amazon services such as S3 and ECR requires internet access but this can be avoided with VPC Endpoints. They are described in the [user guide](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html) and [developer guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/vpc-endpoints.html).

#### Elastic Load Balancing (ELB)

[Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/) automatically distributes incoming application traffic across multiple targets, such as Amazon EC2 instances, containers, IP addresses, and Lambda functions.



### Storage

I have a separate page for Amazon [Storage](Storage.md) products - primarily EBS and EFS.

My EC2 instances use encrypted EBS volumes.



### Database Services

#### Relational Database Service (RDS)

Amazon Relational Database Service ([RDS](https://aws.amazon.com/rds/)) makes it easy to set up, operate, and scale a relational database in the cloud. Examples include Aurora, Postgres, MySQL, MariaDB, Oracle and SQL Server.

#### DynamoDB

Amazon DynamoDB is a key-value and document database that delivers single-digit millisecond performance at any scale.

#### ElastiCache

Amazon ElastiCache offers fully managed [Redis](https://aws.amazon.com/redis/) and [Memcached](https://aws.amazon.com/memcached/). Seamlessly deploy, run, and scale popular open source compatible in-memory data stores.



### General Services

#### Simple Email Service (SES)

You can use the SMTP interface to integrate Amazon [SES](SES.md) directly into your existing applications. You can also integrate the email sending capabilities of Amazon SES into the software you already use, such as ticketing systems and email clients.

I uses SES to send the e-mail notifications from Jenkins in the event of job failures.

#### Cognito

Amazon [Cognito](https://aws.amazon.com/cognito/) lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily.

#### Certificate Manager

AWS [Certificate Manager](https://aws.amazon.com/certificate-manager/) is a service that lets you easily provision, manage, and deploy public and private Secure Sockets Layer/Transport Layer Security (SSL/TLS) certificates for use with AWS services and your internal connected resources.

A cheaper alternative is to use [Let’s Encrypt](https://letsencrypt.org/) which is a global Certificate Authority (CA). They let people and organizations around the world obtain, renew, and manage SSL/TLS certificates which can be used by websites to enable secure HTTPS connections.



### Security

#### Simple Firewalls

The first line of defence in security are firewalls - [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) + [Access Control Lists](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html).

I endeavour to make both as restrictive as possible for my VPNs - "belt and braces" so to speak!

#### Web Application Firewall (WAF)

AWS [WAF](https://aws.amazon.com/waf/) gives control over which traffic to allow or block to web applications by defining customizable web security rules. You can deploy AWS WAF in many different ways.

#### Encryption

Data should be encrypted when in transit and at rest.

Ensuring that data is encrypted at rest is extremely easy on AWS - e.g. [EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html) + [S3 Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/bucket-encryption.html).

Note: For my personal projects, I just use the default AWS encryption rather than using KMS (see below).

#### Access Controls

Identity and Access Management ([IAM](https://aws.amazon.com/iam/)) enables management of access to AWS services and resources securely. Using IAM, you can create and manage AWS users and groups, and use permissions to allow and deny their access to AWS resources.

Permissions can also be granted to specific machines, thus avoiding the need for secrets to be stored on disk. I have documented some [example](IAM.md) profiles on a separate page.

#### Key Management Service (KMS)

AWS Key Management Service ([KMS](https://aws.amazon.com/kms/)) makes it easy to create and manage keys and control the use of encryption across a wide range of AWS services and in your applications. These keys can be used with facilities such as EBS encryption and S3 encryption.




### Integration

#### MQ

Amazon [MQ](https://aws.amazon.com/amazon-mq/?amazon-mq.sort-by=item.additionalFields.postDateTime&amazon-mq.sort-order=desc) is a managed message broker service for Apache ActiveMQ that makes it easy to set up and operate message brokers in the cloud.

#### Simple Queue Service (SQS)

Amazon Simple Queue Service ([SQS](https://aws.amazon.com/sqs/)) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications.

#### Simple Notification Service (SNS)

Amazon Simple Notification Service ([SNS](https://aws.amazon.com/sns/)) is a highly available, durable, secure, fully managed pub/sub messaging service that enables you to decouple microservices, distributed systems, and serverless applications.

#### Kinesis

Amazon [Kinesis](https://aws.amazon.com/kinesis/) makes it easy to collect, process, and analyze real-time, streaming data so you can get timely insights and react quickly to new information.




### Systems Administration

#### AWS CLI

I have made some notes on the [AWS CLI](CLI.md), including an approach to building Docker images containing the "aws" command.

#### CloudWatch

Amazon [CloudWatch](https://aws.amazon.com/cloudwatch/) is a monitoring and observability service built for DevOps engineers, developers, site reliability engineers (SREs), and IT managers.



### Account Management

#### Billing

[AWS Cost Management](https://aws.amazon.com/aws-cost-management/) is a set of tools to help you to access, organize, understand, control, and optimize your AWS costs and usage.

These tools include:

- AWS [Cost Explorer](https://aws.amazon.com/aws-cost-management/aws-cost-explorer/) has an easy-to-use interface that lets you visualize, understand, and manage your AWS costs and usage over time.

- The [AWS Cost & Usage Report](https://aws.amazon.com/aws-cost-management/aws-cost-and-usage-reporting/) contains the most comprehensive set of AWS cost and usage data available, including additional metadata about AWS services, pricing, and reservations.

#### CloudTrail

AWS [CloudTrail](https://aws.amazon.com/cloudtrail/) is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account.

