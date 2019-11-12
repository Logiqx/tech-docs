# GitHub

Triggering a Jenkins build when code is pushed to GitHub is extremely useful and just involves some simple configuration on GitHub + Jenkins.



## Network Security

Firstly, it is necessary to determine the IP address ranges of the GitHub servers.

This is described in the [GitHub Help](About GitHub's IP addresses) but is just a case of using the [API](https://api.github.com/meta) and checking the "hooks" section.

```
  "hooks": [
    "192.30.252.0/22",
    "185.199.108.0/22",
    "140.82.112.0/20"
  ],
```

The appropriate CIDR ranges just need to be given access to port 8080 of the Jenkins server.

In the case of AWS this means suitable ACL and Security Group entries. Don't forget to create an outbound rule for the ephemeral ports in the ACL (e.g. ports 1024 - 65535 for 0.0.0.0/0).



## GitHub User

It is advisable to create a dedicated user on GitHub for Jenkins. This user can then be added to the appropriate projects as a contributor to limit the scope of what they can modify.



## GitHub Project

Using the GitHub repository use "Settings" -> "Webhooks" -> "Add webhook" to enter the Jenkins URL.

```
http://jenkins-server:8080/github-webhook/
```

Note: The trailing slash is essential and if it is missing you will get error 302.

It is worth mentioning that the "Recent Deliveries" can be redelivered for testing purposes. Just select the appropriate recent delivery (even if it resulted in an error) and click "Redeliver". This is a useful way to test Jenkins jobs if they are not responding or working correctly, rather than repeatedly pushing changes to GitHub.



## Jenkins Environment

To gain access to the Jenkins container you can use `docker exec -it wca_jenkins_1 sh` which will give you shell access. Any changes to git configuration will be safely persisted in the Docker volume.

Under some circumstances it may be useful to store GitHub credentials with the jenkins user. This can be achieved by issuing the following command inside the Docker container:

```
git config --global credential.helper store
```

Note: An alternative approach is to bind mount ~/.git-credentials from the Docker host but only if it is the same GitHub user.

To set the username and email address for future commits use the following commands:

```
git config --global user.name "Someone"
git config --global user.email "someone@somewhere.com"
```

Note: An alternative approach is to bind mount ~/.gitconfig from the Docker host but only if it is the same GitHub user.



## Jenkins Project

Most tutorials describe GitHub hooks in the context of a freestyle project. These tutorials are all pretty straightforward but it is a little trickier for a Pipeline job.

In reality it is just a case of selecting "GitHub hook trigger for GITScm polling" under "Build Triggers" and having suitable entries in the pipeline script. There is no real need to be setting up SCM credentials elsewhere within the job.

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
       stage('Blah') {
            ...
        }
    }
}
```

I found out about the need for the pipeline trigger property on the [Jenkins issue log](https://issues.jenkins-ci.org/browse/JENKINS-35132?focusedCommentId=294758&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-294758). Prior to this discovery, I could see the GitHub call in the Jenkins log but the Jenkins pipeline was not being started.




## API

Although not related to GitHub integration itself, I also looked at the Jenkins API and how to generate crumbs. I found some helpful details on a [GitHub gist](https://gist.github.com/dasgoll/455522f09cb963872f64e23bb58804b2).

```
http://jenkins-server:8080/crumbIssuer/api/xml
```

