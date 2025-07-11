---
title: "CDK fundamentals"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

To begin with CDK, we need to understand the following fundamentals:

- The sub commands of CDK toolkit (aka AWS CDK CLI or `cdk`):

  - `cdk init`: Create a new AWS CDK TypeScript project
  - `cdk bootstrap`: Deploys the CDK toolkit stack into an AWS environment
  - `cdk synth`: Synthesizes and prints the CloudFormation template for this stack
  - `cdk deploy`: Deploys the CDK stack(s) into your AWS account

- The two main components of a CDK project:

  - The `bin` directory: contains the CDK code for the CDK app.
  - The `lib` directory: contains the CDK code for the stacks and used constructs.

- The main workflow when you works with a CDK project:

  **You update CDK code** -> **You run `cdk deploy`** -> `cdk synth` is run -> CloudFormation template is "synthesized" -> Resources are deployed to AWS.

Next, we will deploy an example CDK project to understand how everything works.
