# Docker

## Background

Docker is a set of platform as a service (PaaS) products that use OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels. All containers are run by a single operating-system kernel and are thus more lightweight than virtual machines.

Docker - https://www.docker.com/

Docker Hub - https://hub.docker.com/



## Installing Docker

I've documented a number of topics relating to [installation](Installation.md) in a separate page.

I've also made some notes relating to [Windows](Windows.md) which is my laptop OS at home.



## Getting Started

I started off by working through "Get started with Docker for Windows" and "Get Started with Docker".

A summary of the commands used is below but the full output has also been thoroughly [documented](Get_Started.md).



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



## Long-Running or One-Off?

Docker is perfectly suited to short-lived tasks as well as long-running services.

[Docker Tip #42: Using Docker for Long Running and One off Tasks](https://nickjanetakis.com/blog/docker-tip-42-using-docker-for-long-running-and-one-off-tasks)



## Registries

The obvious container registry options when running Docker container on AWS are [DockerHub](https://hub.docker.com/) and [Elastic Container Registry (ECR)](../../../Infrastructure/AWS/ECR.md).

[Docker Hub vs Creating a Local Docker Registry](https://code-maze.com/docker-hub-vs-creating-docker-registry/)

[Comparing Container Image Registries- DockerHub, Amazon EC2, and JFrog Artifactory](https://www.nirmata.com/2017/03/14/comparing-container-image-registries-dockerhub-amazon-ec2-and-jfrog-artifactory/)



## Orchestration

### Compose vs Swarm

My Docker configurations have been created for development purposes and make use of Docker Compose.

They typically specify the version numbers of Docker images in .env which means that they cannot be started directly using Docker Swarm.

The workaround is to use Docker Compose as a pre-processor for Docker Swarm.

```
$ docker stack deploy -c <(docker-compose config) STACK_NAME
```



## Persistent Storage

### Volumes

Useful for things like MySQL and MariaDB databases.

### Bind Mounts

Useful for files that need to be accessible on host machine during development.

### Reference

[Manage data in Docker](https://docs.docker.com/storage/) - Choose the right type of mount for containers to store files in the host machine

[Use Volumes](https://docs.docker.com/storage/volumes/#differences-between--v-and---mount-behavior) - Volumes are the preferred mechanism for persisting data used by Docker containers

[Cloudstor (AWS)](https://docs.docker.com/docker-for-aws/persistent-data-volumes/) - Cloudstor is a modern volume plugin for Docker swarms deployed on AWS



## Building Docker Images

I've documented a number of topics relating to image [builds](Builds.md) in a separate page.



## Security Considerations

I am in the process of documenting [security](Security.md) considerations relating to Docker in a separate page.



## Container Health Checks

I have yet to implement health checks but they are an essential component in production swarms.



## Useful Docker Commands

The command line help for Docker is very good but a cheat sheet is still useful to a beginner.

I've listed a number of my most frequently used [commands](Commands.md).



## Tips and Tricks

I've have collated random [Tips and Tricks](Tips_and_Tricks.md) from the Docker Captains.



## Articles

### Moby

Docker Blog - [Introducing Moby Project](https://blog.docker.com/2017/04/introducing-the-moby-project/)


