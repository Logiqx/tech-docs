# AWS CLI

## Official Docker Image

There is no official Docker image containing the AWS CLI so it must be built from scratch.

A thread exists on [GitHub](https://github.com/aws/aws-cli/issues/3553) discussing the absence of such an image on DockerHub.



## Custom Docker Image

I have created my own Docker image and which is available in [GitHub](https://github.com/Logiqx/aws-cli) and [DockerHub](https://hub.docker.com/repository/docker/logiqx/aws-cli).

The Dockerfile uses an official release of Python:

```dockerfile
ARG PYTHON_VERSION=3.8
ARG ALPINE_VERSION=3.10

FROM python:${PYTHON_VERSION}-alpine${ALPINE_VERSION}

ARG AWSCLI_VERSION=1.16.274

ARG AWS_USER=aws
ARG AWS_GROUP=aws
ARG AWS_UID=1000
ARG AWS_GID=1000

RUN apk add --no-cache groff && \
    pip install --no-cache-dir awscli==${AWSCLI_VERSION} && \
    addgroup -g ${AWS_GID} -S ${AWS_GROUP} && \
    adduser -u ${AWS_UID} -S ${AWS_USER} -G ${AWS_GROUP} && \
    mkdir /home/${AWS_USER}/work && \
    chown ${AWS_USER}:${AWS_GROUP} /home/${AWS_USER}/work

USER ${AWS_USER}
WORKDIR /home/${AWS_USER}/work

ENTRYPOINT ["aws"]
CMD ["help"]
```



## Bundle Install

On Linux, macOS, or Unix, you can use the bundled installer to install version 1 of the AWS Command Line Interface (AWS CLI). The bundled installer includes all dependencies and can be used offline.

The bundle install might be useful on some operating systems.

User only install:

```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
./awscli-bundle/install -b ~/bin/aws
```

System install:

```
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```




## References

### AWS

Useful pages provided by AWS:

- [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
- [Install the AWS CLI version 1 Using the Bundled Installer (Linux, macOS, or Unix)](https://docs.aws.amazon.com/cli/latest/userguide/install-bundle.html)

### Docker Images

A few of the most popular images on DockerHub were a useful reference:

- [Minimal image with aws-cli and jq](https://hub.docker.com/r/mikesir87/aws-cli/dockerfile)
- [Alpine Base image with AWS CLI](https://hub.docker.com/r/boycey/alpine-aws-cli/)
- [Containerized AWS CLI on alpine](https://hub.docker.com/r/mesosphere/aws-cli/dockerfile)
- [aws-cli for Amazon Web Services](https://hub.docker.com/r/nbrown/aws-cli/dockerfile)

This project is an absolutely shocking example with AWS keys in environment variables - OMG!

- [Easing the use of the AWS CLI](https://lostechies.com/gabrielschenker/2016/09/21/easing-the-use-of-the-aws-cli/)

