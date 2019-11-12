# Examples

## Starting + Stopping EC2 Instances

The following script starts + stops an EC2 instance running MySQL.

It uses logiqx/aws-cli but it could equally have used aws commands installed into a custom Jenkins image.

```
pipeline {
    agent {
        label 'master'
    }
    parameters {
        string(
            name: 'AWS_REGION',
            defaultValue: 'us-east-1',
            description: 'Region of the database server')
        string(
            name: 'AWS_INSTANCE',
            defaultValue: 'i-xxxxxxxxxxxxxxxxx',
            description: 'Instance ID of the database server')
        string(
            name: 'MYSQL_HOSTNAME',
            defaultValue: 'something.internal',
            description: 'Hostname of the database server')
    }
    stages {
        stage('Start Database') {
            steps {
                sh """
                    docker run --rm some-user/aws-cli ec2 start-instances \
                        --region ${AWS_REGION} --instance-ids ${AWS_INSTANCE}

                    docker run --mount type=bind,src=/home/ec2-user/.my.cnf,dst=/home/jovyan/.my.cnf \
                        --rm wca-db mysqladmin ping \
                        --host=${MYSQL_HOSTNAME} --user=${MYSQL_USER} --connect_timeout=60
            }
        }
        ...
    }
    post {
        always {
            sh """
                docker run --rm some-user/aws-cli ec2 stop-instances \
/aws-cli ec2 stop-instances \
                    --region ${AWS_REGION} --instance-ids ${AWS_INSTANCE}
            """
        }
        failure {
            mail to: 'someone@somewhere.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
```



## Running Docker Containers

The following script runs a batch job inside a Docker container.

```
pipeline {
    agent {
        docker {
            image 'wca-ipy'
            args '''--mount type=volume,src=wca_ipy_data,dst=/home/jovyan/work/wca-ipy/data
                    --mount type=volume,src=wca_ipy_docs,dst=/home/jovyan/work/wca-ipy/docs
                '''
        }
    }
    environment {
        IMAGE = 'wca-ipy'
        PY_SCRIPT_DIR = "/home/jovyan/work/${IMAGE}/python"
    }
    stages {
        stage('Future Competitions') {
            steps {
                sh 'cd ${PY_SCRIPT_DIR}; ./Future_Competitions.py'
            }
        }
    }
    post {
        failure {
            mail to: 'someone@somewhere.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
```

NOTE: For very short batch jobs using the docker agent is faster than running the docker command directly.



## Pushing Code to Git

The following job copies batch output (.md files) into a Git project and pushes it back to Git.

```
pipeline {
    agent {
        label 'master'
    }
    environment {
        REPO = 'some-repo'
    }
    stages {
        stage('Git Clone') {
            steps {
                sh """
                    rm -fr ${REPO}
                    git clone --depth 1 https://github.com/some-user/${REPO}.git
                """
            }
        }
        stage('Git Diff') {
            steps {
                sh """
                    cd ${REPO}
                    cp /home/jovyan/work/${REPO}/docs/*.md docs/
                    git diff --stat
                """
            }
        }
        stage('Git Commit') {
            steps {
                sh """
                    cd ${REPO}
                    git add docs/*.md
                    git diff-index --quiet HEAD || git commit -m "Refresh Reports"
                """
            }
        }
        stage('Git Push') {
            steps {
                sh """
                    cd ${REPO}
                    git push
                """
            }
        }
    }
    post {
        always {
            deleteDir()
        }
        failure {
            mail to: 'someone@somewhere.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
```

The trick `git diff-index --quiet HEAD || git commit` was found on [Stack Exchange](https://devops.stackexchange.com/questions/1325/jenkins-shows-the-job-as-failed-if-there-is-nothing-to-commit-to-gitlab).



## Building Docker Images

A project can be pulled from GitHub and built using Docker using the following script:

```
properties([pipelineTriggers([githubPush()])])

pipeline {
    agent any
    stages {
       stage('Git Clone') {
            steps {
                git url: 'https://github.com/some-user/some-repo.git'
            }
        }
       stage('Docker Build') {
            steps {
                sh "docker build . -t some-image:${env.BUILD_ID}"
            }
        }
    }
}
```


