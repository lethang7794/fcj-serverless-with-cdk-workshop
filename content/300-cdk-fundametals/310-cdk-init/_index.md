---
title: "cdk init"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

In this step we will create a new CDK project from the `sample-app` template:

- Make a new directory named `cdk-workshop` and cd to it:

  ```shell
  mkdir cdk-workshop
  cd cdk-workshop
  ```

  > [!INFO]
  > Remember this directory name `cdk-workshop`. cdk uses this directory name to hard-code information about the CDK app/stack.

- [Optional] Check the available template that CDK init supports:

  ```shell
  cdk init --list
  ```

  ![alt text](/images/workshop-4/cdk--init--list-templates.png)

- Use `cdk init` to create a new CDK project with the `sample-app` template in TypeScript:

  ```shell
  cdk init sample-app --language=typescript
  ```

  ![alt text](/images/workshop-4/cdk--init--sample-app.png)
