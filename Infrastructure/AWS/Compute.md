# Compute

## EC2

### Instance Types

The various [EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/) are listed on the AWS website.



### Costs

The costs of EC2 instances will vary over time but I recorded some examples of reserved instance pricing from [March 2019](Compute_Costs.md).

Latest EC2 prices:

- [Reserved Instances](https://aws.amazon.com/ec2/pricing/reserved-instances/pricing/)
- [On Demand Instances](https://aws.amazon.com/ec2/pricing/on-demand/)



### Common Setup

**Apply System Updates**

```
sudo yum update
```

**Install HTOP**

```
sudo yum install htop
```

**Install Git**

```
sudo yum install git
```

Change the git colours using the following commands:

```sh
git config --global color.diff.old "cyan"
git config --global color.status.untracked "cyan"
git config --global color.status.changed "yellow bold"
```

Note: This was copied from the page describing [git](../../Development/Version_Control/Git.md).

**Install Docker**

```
sudo yum install docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

Note: This was copied from the page describing [installation and configuration](../../Environments/Containers/Docker/Installation.md) of Docker.

**Install Docker Compose**

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Note: This was copied from the page describing [installation and configuration](../../Environments/Containers/Docker/Installation.md) of Docker.




## Services

Interesting AWS services:

Lightsale, ECR, ECS, EKS, Fargate, Lambda, ELB

[../../Development/Version_Control/Git.md)]: 