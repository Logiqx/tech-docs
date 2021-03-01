# Compute

## EC2

[Elastic Compute Cloud (Amazon EC2)](EC2.md) is a web service that provides secure, resizable compute capacity in the cloud. It is designed to make web-scale cloud computing easier for developers.



## Containers

There are several options when it comes to running Docker containers in AWS:

[Elastic Container Registry (ECR)](ECR.md) - Easily store, manage, and deploy container images

[Elastic Container Service (ECS)](ECS.md) - Run containerized applications in production

[Elastic Kubernetes Service (EKS)](https://aws.amazon.com/eks/) - Highly available, scalable, and secure Kubernetes service

[Fargate](https://aws.amazon.com/fargate/) - Run containers without managing servers or clusters

Note: It is also possible to run Docker (via Compose, Swarm or Kubernetes) on bare EC2 instances.



## Lightsail

Virtual servers, storage, databases, and networking for a low, predictable price.

This is likely to be simpler than setting everything up yourself; VPC, Route 53, Elastic IP, EC2, EBS, RDS, etc.

Details about [Lightsail](https://aws.amazon.com/lightsail/) can be found at the AWS web page.



## Lambda

Run code without thinking about servers. Pay only for the compute time you consume.

Details about [Lambda](https://aws.amazon.com/lambda/) can be found at the AWS web page.

