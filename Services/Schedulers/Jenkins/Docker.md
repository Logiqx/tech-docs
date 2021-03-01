# Docker in Jenkins

There are many blogs which describe how to run Docker containers from Jenkins.

Here are just a small selection:

- [Using Docker in Jenkins](https://medium.com/faun/using-docker-in-jenkins-cba6b8070756) on medium.com

- [The simple way to run Docker-in-Docker for CI](https://getintodevops.com/blog/the-simple-way-to-run-docker-in-docker-for-ci) on Get into DevOps.com

- [Using docker in a dockerized Jenkins container](https://forums.docker.com/t/using-docker-in-a-dockerized-jenkins-container/322) on the Docker forums


Mounting `/var/run/docker.sock` does indeed work but it is NOT recommended for security reasons.

Although this method is described within this document, extreme caution should be exercised. If an attacker should gain access to Jenkins via it's GUI or APIs or the container itself you will be in big trouble!



## Setup

### Installing the Docker Client

The best way to install the Docker client into Jenkins is via a Dockerfile.

```dockerfile
ARG JENKINS_VERSION
FROM jenkins/jenkins:${JENKINS_VERSION:-lts-alpine}

# Switch to the root user
USER root

# Add the Docker group
RUN addgroup --gid 993 docker && \
    adduser jenkins docker

# Install Docker client
RUN apk add --no-cache docker

# Back to the Jenkins user
USER jenkins
```

Note how a "docker" group needs to be created in the image and assigned to the Jenkins user.

The gid must match the gid of the docker group on the host machine.

### Socket

Bind mounting the Docker socket from the host into the container can be done from the command line or via Docker Compose:

`/var/run/docker.sock:/var/run/docker.sock:ro`

This allows the Jenkins container to communicate directly with the Docker daemon on the host.

As described earlier, extreme caution should be exercised when using this approach. If an attacker should gain access to Jenkins via it's GUI or APIs or the container itself you will be in big trouble!



## Usage

### Image Builds

[Building your first Docker image with Jenkins 2: Guide for developers](https://getintodevops.com/blog/building-your-first-docker-image-with-jenkins-2-guide-for-developers) - build and publish automatically

