# Elastic Container Registry (ECR)

## Background

Take a look at the AWS website - [Elastic Container Registry (ECR)](https://aws.amazon.com/ecr/).



## Experimentation

Some command line history:

```sh
...
docker push 919019415475.dkr.ecr.us-east-1.amazonaws.com/hello-repository
aws ecr delete-repository --repository-name hello-repository --force
```

