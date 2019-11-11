# Jenkins

## Installation

Pull from Docker Hub:

```
docker pull jenkins/jenkins:lts-alpine
```

Run from command line:

```
docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts-alpine
```

Access Jenkins

```
http://localhost:8080/
```

Unlock Jenkins

```
docker logs wca_jenkins_1
```





## Simple Tasks

### Hello World

https://code-maven.com/jenkins-pipeline-hello-world

```
pipeline {
    agent { label 'master' }
    stages {
        stage('build') {
            steps {
                echo "Hello World!"
            }
        }
    }
}
```

Other examples:

https://jenkins.io/doc/pipeline/tour/hello-world/



## Docker in Jenkins

https://medium.com/faun/using-docker-in-jenkins-cba6b8070756
https://getintodevops.com/blog/the-simple-way-to-run-docker-in-docker-for-ci



## Reverse Proxy

<https://wiki.jenkins.io/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy>



## AWS CLI

https://github.com/aws/aws-cli/issues/3553 - background

### Reference #1

https://hub.docker.com/r/boycey/alpine-aws-cli/

```
FROM alpine:3.10.0

RUN apk --no-cache update && \
    apk --no-cache add bash  python py-pip py-setuptools ca-certificates curl less && \
    pip --no-cache-dir install awscli && \
    rm -rf /var/cache/apk/*
```

https://docs.docker.com/engine/reference/commandline/build/ - command line reference

My approach:

```
# Install AWS CLI
RUN apk add --no-cache python && \
    apk add --no-cache --virtual .build-deps py-pip && \
    pip install --no-cache-dir awscli && \
	apk del .build-deps
```

<https://stackoverflow.com/questions/49118579/alpine-dockerfile-advantages-of-no-cache-vs-rm-var-cache-apk>

### Reference #2

```
FROM docker:18.06

RUN apk update && \
apk -Uuv add python py-pip && \
pip install awscli && \
apk --purge -v del py-pip && \
rm /var/cache/apk/*
```

### Trial Install

```
docker exec -it --user root stoic_yalow sh
apk --no-cache update
apk --no-cache add python py-pip
pip --no-cache-dir install awscli
```

### Testing Install

```
pipeline {
    agent { label 'master' }
    stages {
        stage('build') {
            steps {
                sh script: 'aws --version'
            }
        }
    }
}
```

### Bundle Install?

https://docs.aws.amazon.com/cli/latest/userguide/install-bundle.html#install-bundle-user

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

Note: Requires Python