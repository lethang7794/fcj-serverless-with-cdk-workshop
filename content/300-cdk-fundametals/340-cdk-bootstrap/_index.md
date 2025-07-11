---
title: "cdk bootstrap: Bootstrap the CDK Toolkit stack"
weight: 4
chapter: false
pre: " <b> 3.4. </b> "
---

## Why `cdk bootstrap`?

The first time you deploy an AWS CDK app into an environment (account/region), you need to deploy a "bootstrap stack".

This stack includes resources that are used in the toolkit's operation:

1. An S3 bucket that is used to store templates and assets during the deployment process.
2. IAM roles, policies for CDK to deploy the CDK app on your be half.

## Bootstrap CDK Toolkit stack

- Run `cdk bootstrap` to bootstrap CDK Toolkit stack

  ```shell
  cdk bootstrap
  ```

  ```shell
  # Output
  ⏳  Bootstrapping environment aws://924932512913/ap-southeast-1...
  Trusted accounts for deployment: (none)
  Trusted accounts for lookup: (none)
  Using default execution policy of 'arn:aws:iam::aws:policy/AdministratorAccess'. Pass '--cloudformation-execution-policies' to customize.
  CDKToolkit: creating CloudFormation changeset...
   ✅  Environment aws://924932512913/ap-southeast-1 bootstrapped.
  ```

  ![alt text](/images/workshop-4/cdk--boostrap.png)

- A CloudFormation stack (`CDKToolkit`) has been created in your AWS account.

  ![alt text](/images/workshop-4/cdk--bootstrap--cfn-stack.png)
