# Simple Email Service (SES)

The simple email service is perfect for sending e-mails from EC2 instances.

For example, Jenkins pipelines can use SES to send failure messages to a chosen e-mail address.



## Configuration

### Verifying Your E-mail Address

The first activity is to verify your e-mail address:

This can be achieved from the [SES Management Console](https://console.aws.amazon.com/ses) in the AWS console, under "E-mail Addresses".

You will receive a test e-mail containing a link to confirm that the e-mail has been received.

See [Verifying an Email Address](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-email-addresses-procedure.html) for further details.



### Creating SMTP User

This can be achieved from the [SES Management Console](https://console.aws.amazon.com/ses) in the AWS console, under "SMTP Settings".

Simply click "Create My SMTP Credentials", choose the name press "Create".

You can now click "Show User SMTP Security Credentials" to show the SMTP username and SMTP password.

Save these details somewhere safe as they cannot be obtained at a later date.

The SMTP user can be found under "Identity and Access Management (IAM)" should you wish to delete it.



### Getting SMTP Details

This can be achieved from the [SES Management Console](https://console.aws.amazon.com/ses) in the AWS console, under "SMTP Settings".

The server name and ports are clearly shown on this page. For example:

```
Server Name:   email-smtp.us-east-1.amazonaws.com
Port:          25, 465 or 587
```



### Sending a Test Email

I went straight to Jenkins and tested it from a pipeline as described in my [notes](../../Services/Schedulers/Jenkins/Email.md).



## Reference

[Setting up Email with Amazon SES](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/setting-up-email.html)

[Verifying Identities in Amazon SES](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/verify-addresses-and-domains.html)

[Testing Email Sending Using the Command Line](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/send-email-smtp-client-command-line.html)

[Connecting to the Amazon SES SMTP Endpoint](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/smtp-connect.html)

