---
title: "Summary"
weight: 12
chapter: false
pre: " <b> 12. </b> "
---

In this workshop, you've learned about:

- AWS CDK: The IaC solution from AWS that model application infrastructure using programming languages (TypeScript, Python, Java, and .NET).

- The main concepts of CDK: _app_, _stack_, _construct_.

- The structure of an CDK app: `bin`, `lib` directories...

- AWS Toolkit CLI's commands:
  - `cdk init`
  - `cdk bootstrap`
  - `cdk synth`
  - `cdk diff`
  - `cdk deploy`
  - `cdk destroy`

- Using CDK with TypeScript to deploy:
  - A hello world Lambda function.
  - A DynamoDB table.
  - Grant permission to interact with DynamoDB table for a Lambda function.
  - Lambda functions for CRUD operations for `users`.
  - A REST API Gateway and integrate with Lambda functions.

> [!TIP]
> Now you have the infrastructure for the whole workshop as code (it's infrastructure as code), you can quickly re-deploy it to your AWS account.
>
> - Try running `cdk diff` and `cdk deploy` to see the power of CDK (and infrastructure as code).
