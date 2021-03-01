# AWS CLI

## Installation

The AWS CLI can be installed into the Jenkins itself or via a standalone Docker image.

I have made some notes on building Docker images containing the [AWS CLI](../../../\Infrastructure/AWS/CLI.md).



### Testing

If the AWS CLI has been installed into Jenkins itself:

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

If running from a standalone Docker image:

```
pipeline {
    agent { label 'master' }
    stages {
        stage('AWS version') {
            steps {
                sh "docker run --rm logiqx/aws-cli --version"
            }
        }
    }
}
```

Note: This example relies upon /var/run/docker.sock being shared with the host and is NOT recommended.