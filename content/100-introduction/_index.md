---
title: "Introduction"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

### Infrastructure as Code

_Infrastructure as code_ means applying the same rigor of application code development to infrastructure provisioning.

- All configurations should be defined in a declarative way and stored in a source control system, the same as application code.
- Infrastructure provisioning, orchestration, and deployment should also support the use of the infrastructure as code.

> [!TIP]
> Traditionally, infrastructure was/is provisioned using a combination of **scripts** and **manual processes**.
>
> - Sometimes these scripts were stored in version control systems or documented step by step in text files or run-books.
> - Often the person writing the run books is not the same person executing these scripts or following through the run-books.
> - If these scripts or run-books are not updated frequently, they can potentially become a show-stopper in deployments. This results in the creation of new environments not always being repeatable, reliable, or consistent.

### IaC solutions

AWS has many solutions to define infrastructure as code:

- **AWS CloudFormation**: The IaC service from AWS that declaratively define infrastructure in JSON/YAML.
- **AWS SAM**: The IaC solution from AWS that help you develop serverless application.
  - With SAM, the infrastructure is defined in SAM template (a superset of CloudFormation template in YAML)
- **AWS CDK**: The IaC solution from AWS that model application infrastructure using programming languages (TypeScript, Python, Java, and .NET).

> [!NOTE]
> Both AWS SAM and AWS CDK are not a service in AWS.
>
> - These two don't existed if you search for them in the AWS Management Console.
>
>   ![alt text](/images/workshop-4/iac--sam.png)
>
>   ![alt text](/images/workshop-4/iac--cdk.png)

> [!TIP]
> Some other cloud-agnostic big players in the market are:
>
> - **Pulumi**: An open-source infrastructure as code solution that define infrastructure in any programming languages.
> - **Terraform**: An open-source infrastructure as code solution that define infrastructure using a declarative configuration language known as HashiCorp Configuration Language (HCL).

### AWS CDK

The AWS Cloud Development Kit (AWS CDK) is an open-source software development framework for defining cloud infrastructure in code and provisioning it through AWS CloudFormation.

The AWS CDK consists of two primary parts:

- **[AWS CDK Construct Library](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html)** – A collection of pre-written modular and reusable pieces of code, called constructs, that you can use, modify, and integrate to develop your infrastructure quickly. The goal of the AWS CDK Construct Library is to reduce the complexity required to define and integrate AWS services together when building applications on AWS.
- **[AWS CDK Command Line Interface (AWS CDK CLI)](https://docs.aws.amazon.com/cdk/v2/guide/cli.html)** – A command line tool for interacting with CDK apps. Use the CDK CLI to create, manage, and deploy your AWS CDK projects. The CDK CLI is also referred to as the CDK Toolkit.

The AWS CDK supports TypeScript, JavaScript, Python, Java, C#/.Net, and Go.

- You can use any of these supported programming languages to define reusable cloud components known as [constructs](https://docs.aws.amazon.com/cdk/v2/guide/constructs.html).
- You compose these together into [stacks](https://docs.aws.amazon.com/cdk/v2/guide/stacks.html) and [apps](https://docs.aws.amazon.com/cdk/v2/guide/apps.html).
- Then, you deploy your CDK applications to AWS CloudFormation to provision or update your resources.

  ![CDK app and process overview](https://docs.aws.amazon.com/images/cdk/v2/guide/images/AppStacks.png)

#### How a CDK deployment works?

![alt text](/images/workshop-4/how-a-cdk-deployment-works.png)
