# Docker

## Background

Docker - https://www.docker.com/
Docker Hub - https://hub.docker.com/



## Boot Configuration

To switch between Docker and VirtualBox it is necessary to enable / disable Hyper-V.

I've made some notes on how to create suitable [boot configurations](../../Boot.md).



## Getting Started

I started off by working through "Get started with Docker for Windows" and "Get Started with Docker".

I've listed the commands below but the full output has also been thoroughly [documented](GetStarted.md).



### Get Started With Docker for Windows

Checking docker version:

```sh
docker --version
docker version
```

Listing images and containers:

```sh
docker image ls -a
docker container ls -a
docker ps -a
```

Running bash within Ubuntu:

```sh
docker run --interactive --tty ubuntu bash
docker run -it ubuntu bash
docker container rm amazing_brattain musing_boyd
```

Running webserver within nginx:

```sh
docker run --detach --publish 80:80 --name webserver nginx
docker container stop webserver
docker container rm webserver
```

Checking environment in Alpine:

```sh
docker run alpine env
docker container rm silly_kowalevski
```



### Get Started with Docker

Building Containers:

```sh
docker build -t friendlyhello .
docker run -p 4000:80 friendlyhello
docker run -d -p 4000:80 friendlyhello
```

Pushing Containers to Repository:

```sh
docker login
docker tag friendlyhello logiqx/get-started:part2
docker push logiqx/get-started:part2
```

Services:

```sh
docker swarm init
docker stack deploy -c docker-compose.yml getstartedlab
docker service ls
docker stack services getstartedlab
docker service ps getstartedlab_web
docker stack ps getstartedlab
docker stack rm getstartedlab
docker swarm leave --force
```



## Useful Commands

The command line help for Docker is very good but a cheat sheet is still useful to a beginner.

I've listed a number of my most frequently used and most useful [commands](Commands.md).



## My Containers

This section lists some of the containers that I have been using in Docker.

### Jupyter

I use Jupyter notebooks for ad hoc projects written in Python.

I have documented the way I have set up [Jupyter](../../../Development/Languages/Python/README.md) using Docker Compose.



### MySQL

I use MySQL for ad hoc analysis and as part of an AMP / EMP stack.

I have documented the way I have set up [MySQL](../../../Services/Databases/MySQL/README.md) using Docker Compose.



### NGINX

TODO



### PHP-FPM

TODO



## Advice and Guidance

### Best Practice

Docker Docs - [Docker development best practices](https://docs.docker.com/develop/dev-best-practices/)
Docker Docs - [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
Docker Docs - [Isolate containers with a user namespace](https://docs.docker.com/engine/security/userns-remap/)

Red Hat Developer - [10 things to avoid in docker containers](https://developers.redhat.com/blog/2016/02/24/10-things-to-avoid-in-docker-containers/)
Red Hat Developer - [Keep it small: a closer look at Docker image sizing](https://developers.redhat.com/blog/2016/03/09/more-about-docker-images-size/)
Project Atomic - [Guidance for Docker Image Authors](http://www.projectatomic.io/docs/docker-image-author-guidance/)

Ivan Krizsan - [Time in Docker Containers](https://www.ivankrizsan.se/2015/10/31/time-in-docker-containers/)



### Storage

[Manage data in Docker](https://docs.docker.com/storage/) - Choose the right type of mount for containers to store files in the host machine
[Use Volumes](https://docs.docker.com/storage/volumes/#differences-between--v-and---mount-behavior) - Volumes are the preferred mechanism for persisting data used by Docker containers
[Cloudstor (AWS)](https://docs.docker.com/docker-for-aws/persistent-data-volumes/) - Cloudstor is a modern volume plugin for Docker swarms deployed on AWS



### Sensitive Data / Secrets

[Manage sensitive data with Docker secrets](https://docs.docker.com/engine/swarm/secrets/)
[AWS Launches Secrets Support for Amazon Elastic Container Service](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-launches-secrets-support-for-amazon-elastic-container-servic/)



### Docker Toolbox (Legacy)

Docker Toolbox - [Docker Toolbox overview](https://docs.docker.com/toolbox/overview/)
Docker Machine - [Get started with Docker Machine and a local VM](https://docs.docker.com/machine/get-started/)



## Articles

### General

Docker Blog - [Introducing Moby Project](https://blog.docker.com/2017/04/introducing-the-moby-project/)
Nick Janetakis - [The 3 Biggest Wins When Using Alpine as a Base Docker Image](https://nickjanetakis.com/blog/the-3-biggest-wins-when-using-alpine-as-a-base-docker-image)

### Microsoft

Microsoft - [Containers on Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)

Microsoft Azure (Mark Russinovich) - [Containers: Docker, Windows and Trends](https://azure.microsoft.com/en-us/blog/containers-docker-windows-and-trends/)

Microsoft Azure - [Nested Virtualization in Azure](https://azure.microsoft.com/en-gb/blog/nested-virtualization-in-azure/)

### Windows

InfoWorld - [What you need to know about Docker in Windows](https://www.infoworld.com/article/3163257/application-development/what-you-need-to-know-about-docker-in-windows.html) - e.g. Server Core vs Windows Nano

TechTarget - [Differences between Windows Server Containers, Hyper-V Containers and VMs](http://searchservervirtualization.techtarget.com/tip/Differences-between-Windows-Server-Containers-Hyper-V-Containers-and-VMs)

ITPro Today - [Differences between Windows Containers and Hyper-V Containers in Windows Server 2016](http://www.itprotoday.com/windows-8/differences-between-windows-containers-and-hyper-v-containers-windows-server-2016)

Cloudstacking - [Running Hyper-V VMware or Xen on an AWS EC2 Instance?](http://cloudstacking.com/posts/running-hyper-v-vmware-or-xen-on-an-aws-ec2-instance.html)



## Examples

### LAMP

Cloudreach - [Containerize This: PHP/Apache/MySQL](https://www.mysqlcloudreach.com/blog/containerize-this-php-apache-mysql-within-docker-containers/) - Code on [GitHub](https://github.com/mzazon/php-apache-mysql-containerized/)



### LEMP

Stack Overflow - [Alpine variants of PHP and Apache/httpd in Docker](https://stackoverflow.com/questions/41303775/alpine-variants-of-php-and-apache-httpd-in-docker) - Apache/NGINX and PHP with FCGI

New Media Campaigns - [Docker for PHP Developers](https://www.newmediacampaigns.com/blog/docker-for-php-developers) - Nginx, PHP and MySQL

 

### Java Spring

Cloudreach - [Containerize This! How to Dockerize Your Java Spring Application](https://www.cloudreach.com/blog/dockerize-java-spring-application/) - Uses [multi-stage](https://docs.docker.com/develop/develop-images/multistage-build/) builds



### GoLang

Cloudreach - [Containerize This! How to build Golang Dockerfiles](https://www.cloudreach.com/blog/containerize-this-golang-dockerfiles/) - Uses [multi-stage](https://docs.docker.com/develop/develop-images/multistage-build/) builds

