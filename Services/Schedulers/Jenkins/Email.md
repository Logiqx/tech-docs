# Email Notification

## Configuring Email Notification

Manage Jenkins -> Configure System

- Scroll down to the bottom where you will find "E-mail Notification".
- Specify the STMP server - e.g. "email-smtp.us-east-1.amazonaws.com"
- Click "Advanced..."
  - Tick "Use SMTP Authentication"
  - Specify the SMTP username
  - Specify the SMTP password
  - Tick "Use SSL"
  - Specify the SMTP port - e.g. 465
  - Specify the Reply-To address
- Tick "Test configuration by sending test e-mail"

Note: To configure SES (Simple Email Service) on AWS follow check my [notes](../../../Infrastructure/AWS/SES.md).



## Pipeline Configuration

To send an email in the event of failure:

```
pipeline {
    agent ...
    stages ...
    post {
        failure {
            mail to: 'mickey.mouse@disney.com',
                subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                body: "Something is wrong with ${env.BUILD_URL}"
        }
    }
}
```

