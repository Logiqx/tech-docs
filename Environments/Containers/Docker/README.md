# Docker

## Background

Docker - https://www.docker.com/

Docker Hub - https://hub.docker.com/



## Boot Configuration

To switch between Docker and VirtualBox it is necessary to enable / disable Hyper-V.

I've made some notes on how to create suitable [boot configurations](../../Boot.md).



## Getting Started

I started off by working through "Get started with Docker for Windows" and "Get Started with Docker".

A summary of the commands used is below but the full output has also been thoroughly [documented](Get%20Started.md).



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



## Persistent Storage

### Volumes

Useful for things like MySQL and MariaDB databases.

### Bind Mounts

Useful for files that need to be accessible on host machine during development.

Can initially be a little tricky to get working on Windows.

The section below provides some basic pointers as to what is required in the way of setup.

#### Setup

##### Docker For Windows

Settings -> Shared Drives -> C

##### Share C: Drive

Right-click C: -> Properties -> Sharing -> Share...

##### Anti-Virus Exclusion

Norton -> Settings -> Firewall -> Configure Public Network Exceptions -> File and Printer Sharing



## Useful Commands

The command line help for Docker is very good but a cheat sheet is still useful to a beginner.

I've listed a number of my most frequently used [commands](Commands.md).



## Multi-stage Builds

Using a [multi-stage build](https://docs.docker.com/develop/develop-images/multistage-build/) to convert a Notebook to a simple Python script is advantageous.

I've used this approach in a number of my projects on GitHub:

- [wca-db](https://github.com/Logiqx/wca-db/blob/master/Dockerfile) - World Cube Association - Scripts to download and optimise the database
- [wca-ipy](https://github.com/Logiqx/wca-ipy/blob/master/Dockerfile) - iPython notebooks to produce (un)official rankings for the senior cubing community

For example:

```dockerfile
# Base image versions
ARG NOTEBOOK_VERSION=c39518a3252f
ARG PYTHON_VERSION=3.8
ARG ALPINE_VERSION=3.10

# Jupyter notebook image is used as the builder
FROM jupyter/base-notebook:${NOTEBOOK_VERSION} AS builder

# Copy the required project files
WORKDIR /home/jovyan/work/wca-db
COPY --chown=jovyan:users python/*.*py* ./python/
COPY --chown=jovyan:users sql/*.sql ./sql/

# Convert Jupyter notebooks to regular Python scripts
RUN jupyter nbconvert --to python python/*.ipynb && \
    rm python/*.ipynb

# Ensure project file permissions are correct
RUN chmod 755 python/*.py && \
    chmod 644 sql/*.sql

# Create final image from Python 3 + Beautiful Soup 4 on Alpine Linux
FROM logiqx/python-bs4:${PYTHON_VERSION}-alpine${ALPINE_VERSION}

# Install MySQL client
RUN apk add --no-cache mysql-client=~10.3

# Note: Jovian is a fictional native inhabitant of the planet Jupiter
ARG PY_USER=jovyan
ARG PY_GROUP=jovyan
ARG PY_UID=1000
ARG PY_GID=1000

# Create the Python user and work directory
RUN addgroup -g ${PY_GID} -S ${PY_GROUP} && \
    adduser -u ${PY_UID} -S ${PY_USER} -G ${PY_USER} && \
    mkdir -p /home/${PY_USER}/work && \
    chown ${PY_USER} /home/${PY_USER}/work

# Environment variables used by the Python scripts
ENV MYSQL_HOSTNAME=mariadb
ENV MYSQL_DATABASE=wca
ENV MYSQL_USER=wca

# Copy project files from the builder
USER ${PY_USER}
WORKDIR /home/${PY_USER}/work
COPY --from=builder --chown=jovyan:jovyan /home/jovyan/work/ ./

# Define the command / entrypoint
CMD ["python3"]
```



## My Images

This section lists some of the images that I have created using in Docker.

### Python+ Beautiful Soup

The base image for my Python deployments is on DockerHub:

- [python-bs4](https://hub.docker.com/repository/docker/logiqx/python-bs4) - Python 3 + Beautiful Soup 4 on Alpine Linux

The hardest bit of the python-bs4 build relates to [lxml](https://lxml.de/installation.html) but it is fully documented on [GitHub](https://github.com/Logiqx/python-bs4).



### Jupyter

I use Jupyter notebooks for ad hoc projects written in Python.

I have documented the way I have set up [Jupyter](../../../Development/Languages/Python/README.md) using Docker Compose.



### MySQL

I have used MySQL for WCA data analysis and as part of an AMP / EMP stack.

I have documented the way I have set up [MySQL](../../../Services/Databases/MySQL/README.md) using Docker Compose.



### MariaDB

I have used MariaDB for WCA data analysis and as part of an AMP / EMP stack.

I have documented the way I have set up [MariaDB](../../../Services/Databases/MariaDB/README.md) using Docker Compose.



### NGINX

TODO



### PHP-FPM

TODO



### WordPress

TODO - [Dockerizing Wordpress with Nginx and PHP-FPM](https://www.google.com/amp/s/www.howtoforge.com/tutorial/dockerizing-wordpress-with-nginx-and-php-fpm/amp/)



## Compose vs Swarm

My Docker configurations have been created for development purposes and make use of Docker Compose.

They typically specify the version numbers of Docker images in .env which means that they cannot be started directly using Docker Swarm.

The workaround is to use Docker Compose as a pre-processor for Docker Swarm.

```
$ docker stack deploy -c <(docker-compose config) STACK_NAME
```



## Linux Containers on Windows

Linux Containers on Windows (LCOW) is an experimental feature in Docker for Windows.

Until some of the issues with it have been fixed, I will continue to run traditional Linux containers.

I've made some notes on my experiences with [LCOW](LCOW.md).



## Tips and Tricks

I've have collated random [Tips and Tricks](Tips%20and%20Tricks.md).



## Health Checks

I have yet to implement health checks but they are an essential component in production swarms.



## Advice and Guidance

### Best Practice

Docker Docs - [Docker development best practices](https://docs.docker.com/develop/dev-best-practices/)

Docker Docs - [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

Docker Docs - [Isolate containers with a user namespace](https://docs.docker.com/engine/security/userns-remap/)

Red Hat Developer - [10 things to avoid in docker containers](https://developers.redhat.com/blog/2016/02/24/10-things-to-avoid-in-docker-containers/)

Red Hat Developer - [Keep it small: a closer look at Docker image sizing](https://developers.redhat.com/blog/2016/03/09/more-about-docker-images-size/)

Project Atomic - [Guidance for Docker Image Authors](http://www.projectatomic.io/docs/docker-image-author-guidance/)

Ivan Krizsan - [Time in Docker Containers](https://www.ivankrizsan.se/2015/10/31/time-in-docker-containers/)



### Small Images

A few nice articles relating to Alpine Linux:

- [The 3 Biggest Wins When Using Alpine as a Base Docker Image](https://nickjanetakis.com/blog/the-3-biggest-wins-when-using-alpine-as-a-base-docker-image)
- [Reduce Docker image sizes using Alpine](https://www.sandtable.com/reduce-docker-image-sizes-using-alpine/)
- [Tips & Tricks with Alpine + Docker](http://blog.zot24.com/tips-tricks-with-alpine-docker/)
- [Alpine Dockerfile Advantages of --no-cache Vs. rm /var/cache/apk/*](https://stackoverflow.com/questions/49118579/alpine-dockerfile-advantages-of-no-cache-vs-rm-var-cache-apk)

Note: Image size is not the primary consideration when choosing a base OS but it is still quite cool.



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

### Moby

Docker Blog - [Introducing Moby Project](https://blog.docker.com/2017/04/introducing-the-moby-project/)

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

