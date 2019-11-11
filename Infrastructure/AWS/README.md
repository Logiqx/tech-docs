# AWS

## Background

[AWS Products](https://aws.amazon.com/products)

[AWS Summit](https://aws.amazon.com/events/summits/london/)



## AWS CLI

I have made some notes on building Docker images containing the [AWS CLI](CLI.md).



## Various

**Database** - Aurora, RDS (MySQL, MariaDB, Postgres), DynamoDB, ElastiCache

**Networking** - VPC, Route 53, ELB

**Management** - CloudWatch, Auto Scaling, CloudTrail

**Security** - IAM, Cognito, Certificate Manager, KMS, WAF

**Integration** - MQ, SQS, SNS, Kinesis

**Costs** - Cost Explorer, Cost + Usage Reports



## Notes

[Compute](Compute.md) - Instance Types, Costs, etc.

[Storage](Storage.md) - EBS, EFS, Persistence, etc.

[NAT](Nat.md) - NAT instances

[Terraform](../IaC/Terraform.md) - Links, etc.



## Thoughts

EC2 - Burstable

NAT instance - YES

Encryption - EBS + S3, not using KMS

SSL - Nginx

Networking - Elastic IP + Route 53

Mail - SES / MailCow



## TODO

Instance Scheduler

​	[AWS Instance Scheduler](https://aws.amazon.com/solutions/instance-scheduler/)

​	[Using AWS Instance Scheduler to Reduce Amazon EC2 or RDS cost](https://medium.com/@ahmeeddhon/using-aws-instance-scheduler-to-reduce-amazon-ec2-or-rds-cost-56c9eb374344)

Security

​	SSH agent forwarding - ssh -A ec2-user@....

​	[Secure your instances with multi-factor authentication](https://aws.amazon.com/blogs/startups/securing-ssh-to-amazon-ec2-linux-hosts/)

Time sync

​	<https://aws.amazon.com/blogs/aws/keeping-time-with-amazon-time-sync-service/> - Installed by default

VPC endpoints - S3, ECR

​	<https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html>

Docker

​	<https://docs.docker.com/config/containers/start-containers-automatically/>



## Technical Reference

### Docker

[Docker Tip #42: Using Docker for Long Running and One off Tasks](https://nickjanetakis.com/blog/docker-tip-42-using-docker-for-long-running-and-one-off-tasks) - e.g. one off tasks that exit

[Docker Hub: Configure Automated Builds from GitHub and BitBucket](https://docs.docker.com/docker-hub/builds/link-source/) - Docker Hub or ECR?

[Docker Hub vs Creating a Local Docker Registry](https://code-maze.com/docker-hub-vs-creating-docker-registry/) - maybe I'll use ECR?

​	[Comparing Container Image Registries- DockerHub, Amazon EC2, and JFrog Artifactory](https://www.nirmata.com/2017/03/14/comparing-container-image-registries-dockerhub-amazon-ec2-and-jfrog-artifactory/)

### Jenkins

[Building your first Docker image with Jenkins 2: Guide for developers](https://getintodevops.com/blog/building-your-first-docker-image-with-jenkins-2-guide-for-developers) - build and publish automatically

[Using docker in a dockerized Jenkins container](https://forums.docker.com/t/using-docker-in-a-dockerized-jenkins-container/322) - mounting Docker socket within Jenkins container

[URLTrigger Plugin](https://wiki.jenkins.io/display/JENKINS/URLTrigger+Plugin) - monitor changes of the response got from an URL invocation

### Python

[Docker Official Image](https://hub.docker.com/_/python) - Use for deployment rather than Jupyter Notebook

