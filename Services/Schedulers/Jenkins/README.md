# Jenkins

## Installation

### Docker Run

Pull from Docker Hub:

```
docker pull jenkins/jenkins:lts-alpine
```

Run from command line:

```
docker run -d -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 jenkins/jenkins:lts-alpine
```

Access Jenkins via a web browser:

```
http://localhost:8080/
```

Unlock Jenkins by checking for the key in the logs:

```
docker logs [container]
```



### Docker Compose

Another way to run Jenkins is using Docker Compose.

.env

```
# Prefix for image names, container names and volume names
COMPOSE_PROJECT_NAME=custom

# Desired version of Jenkins (Official Docker image)
JENKINS_VERSION=lts-alpine

# Tag for the custom Jenkins image
PROJECT_JENKINS_VERSION=1.0
```

docker-compose.yml

```
version: '3.1'
services:
  jenkins:
    build:
      context: ./jenkins
      args:
        JENKINS_VERSION: lts-alpine
    image: ${COMPOSE_PROJECT_NAME:-custom}_jenkins:${PROJECT_JENKINS_VERSION:-latest}-${JENKINS_VERSION:-latest}
    restart: always
    security_opt:
      - no-new-privileges:true
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock:ro
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
volumes:
  jenkins_home:
```

Note: The bind mount of /var/run/docker.sock allows Docker commands to be used from Jenkins but it is NOT a recommended practice. This is explained in a separate document.



## Setting up a Reverse Proxy

A reverse proxy might be required, certainly in the case of an HTTPS requirement.

The following links might prove useful:

- [Jenkins behind an NGinX reverse proxy](https://wiki.jenkins.io/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy)



## Running Hello World

Once Jenkins is installed it is time to create the simplest possible job!

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



## Running Docker Containers

I have made some notes on running [Docker containers](Docker.md) from inside Jenkins via the Docker socket.



## Running AWS CLI Commands

I have made some notes on using the [AWS CLI](AWS.md) from inside Jenkins, optionally using a custom Docker image.



## Job Triggers

### Build Periodically

Include the relevant code in the pipeline script.

```
pipeline {
    agent ...
    triggers {
        cron('10 4 * * *')
    }
    ...
}
```

### Build Dependencies

Include the relevant code in the pipeline script.

```
pipeline {
    agent ...
    triggers {
        upstream(upstreamProjects: 'job1, job2', threshold: hudson.model.Result.SUCCESS)
    }
    ...
}
```

### GitHub Triggers

I have made some notes on [GitHub hooks](GitHub.md) so that builds can be triggered when changes are pushed to GitHub.



## Email Notifications

I have made some notes on configuring [email notification](Email.md) from inside Jenkins.



## Pipeline Examples

I've created some [examples](Examples.md) which include the use of Docker and Git.



## Reference

A few useful links about Jenkins Pipelines:

- [How to Use the Jenkins Declarative Pipeline](https://www.blazemeter.com/blog/how-to-use-the-jenkins-declarative-pipeline/)