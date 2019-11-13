# Elastic Compute Cloud (EC2)

## Background

### Instance Types

The various [EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/) are listed on the AWS website.

I tend to use t3.micro (2 vCPU / 1GiB RAM) and t3.small (2 vCPU / 2GiB RAM).

Where available, I utilise the [Burstable Performance Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html).

### Costs

The costs of EC2 instances will vary over time but I recorded some examples of reserved instance pricing from [March 2019](Compute_Costs.md).

Latest EC2 prices can be found for [Reserved Instances](https://aws.amazon.com/ec2/pricing/reserved-instances/pricing/) and [On Demand Instances](https://aws.amazon.com/ec2/pricing/on-demand/).



## Configuration

### Configure System

#### Apply System Updates

```sh
sudo yum update
```

#### Enable Multi-factor Authentication (MFA)

Securing instances with MFA has been described in an [AWS Startups Blog](https://aws.amazon.com/blogs/startups/securing-ssh-to-amazon-ec2-linux-hosts/) and should be considered if an instance is accessible via the internet.

Prior to installing google-authenticator you need to enable [EPEL](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-enable-epel/):

```sh
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

Once EPEL is enabled it is possible to install google-authenticator as described in the blog:

```sh
sudo yum install google-authenticator -y
google-authenticator
  y y y y n y
sudo vi /etc/pam.d/sshd
  #auth       substack     password-auth
  auth       required     pam_google_authenticator.so
sudo vi /etc/ssh/sshd_config
  ChallengeResponseAuthentication yes
  #ChallengeResponseAuthentication no
  AuthenticationMethods publickey,keyboard-interactive
sudo service sshd restart
```

#### Enable Time Sync

The [Amazon Time Sync Service](https://aws.amazon.com/about-aws/whats-new/2017/11/introducing-the-amazon-time-sync-service/) was originally described in a [blog article](https://aws.amazon.com/blogs/aws/keeping-time-with-amazon-time-sync-service/).

It is now included in Amazon Linux 2 so there is no need to set it up specifically.




### Install Common Tools

#### Install HTOP

```sh
sudo yum install htop
```

#### Install Git

```sh
sudo yum install git
```

Change the git colours using the following commands:

```sh
git config --global color.diff.old "cyan"
git config --global color.status.untracked "cyan"
git config --global color.status.changed "yellow bold"
```

Note: This was copied from the page describing [git](../../Development/Version_Control/Git.md).



### Install Services

#### Install Docker

```sh
sudo yum install docker
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```

Note: This was copied from the page describing [installation and configuration](../../Environments/Containers/Docker/Installation.md) of Docker.

#### Install Docker Compose

```sh
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Note: This was copied from the page describing [installation and configuration](../../Environments/Containers/Docker/Installation.md) of Docker.



## Usage Tips

### Connecting via SSH

#### Bastion Host

If connecting to an EC2 instance in the private subnet via a Bastion host in the public subnet it is advisable to use SSH agent forwarding. Connect to the Bastion host using the -A flag of the SSH command:

```
ssh -A ec2-user@....
```



