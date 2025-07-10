---
title: "cdk init: Create a new AWS CDK project"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

- Make a new directory and cd to it:

  ```shell
  mkdir cdk-workshop
  cd cdk-workshop
  ```

  > [!IMPORTANT]
  > Remember this directory name `cdk-workshop`. cdk uses this directory name to hard-code information about the CDK app/stack.

- Check the available template that CDK init supports:

  ```shell
  cdk init --list
  ```

  ![alt text](/images/workshop-4/cdk--init--list-templates.png)

- Use `cdk init` to create a new CDK project with the `sample-app` template in TypeScript:

  ```shell
  cdk init sample-app --language=typescript
  ```

  ![alt text](/images/workshop-4/cdk--init--sample-app.png)
