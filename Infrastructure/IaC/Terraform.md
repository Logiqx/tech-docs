# Terraform

## Background

[Terraform](https://www.terraform.io/) - Official home page

[Terraform vs. Custom Solutions](https://www.terraform.io/intro/vs/custom.html) - Benefits of Terraform vs custom solution



## Cheat Sheet

### Installation

Download package:

```sh
mkdir terraform
cd terraform/
wget https://releases.hashicorp.com/terraform/0.12.6/terraform_0.12.6_linux_amd64.zip
unzip terraform_0.12.6_linux_amd64.zip
```

Add to path:

```sh
vi .bashrc
PATH=$PATH:$HOME/terraform
```

Edit ~/.vimrc

```
syntax on

colorscheme desert
```



### Usage

Initialisation:

```sh
terraform init
```

Edit script:

```sh
vi test.tf
```

Basic commands:

```sh
terraform plan
terraform apply
terraform show
terraform destroy
```



## AWS

### Introduction
[Introduction to AWS With Terraform](https://hackernoon.com/introduction-to-aws-with-terraform-7a8daf261dc0) - Hacknoon blog

[Elastic IP Example](https://github.com/terraform-providers/terraform-provider-aws/blob/master/examples/eip/main.tf) - AWS example from Terraform

### Best Practices

[terraform-best-practices](https://github.com/ozbillwang/terraform-best-practices) - Nice project on GitHub

### VPC

[VPC](https://www.terraform.io/docs/providers/aws/r/vpc.html#default_security_group_id) - Provides a VPC resource

### EC2

[EC2 Instance](https://www.terraform.io/docs/providers/aws/r/instance.html) - Provides an EC2 instance resource



## TODO

https://www.terraform.io/docs/providers/aws/r/instance.html

- Name and tag network interface - NOT POSSIBLE
- Do not auto-start - NOT POSSIBLE



## Reference

### Miscellaneous

[Output Variables](https://learn.hashicorp.com/terraform/getting-started/outputs) - Outputs are a way to tell Terraform what data is important

[Local Values](https://www.terraform.io/docs/configuration/locals.html) - Avoid repeated values in script