---
title: "TODO: Section 3.1 - title"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

### `cdk init`: Create a new AWS CDK TypeScript project

- Make a new directory and cd to it:

  ```shell
  mkdir cdk-workshop
  cd cdk-workshop
  ```

- Check the available template that CDK init supports:

  ```shell
  cdk init --list
  ```

  ![alt text](/images/workshop-4/cdk--init--list-templates.png)

- Use cdk init to create a new `sample-app` CDK project in TypeScript:

  ```shell
  cdk init sample-app --language=typescript
  ```

  ![alt text](/images/workshop-4/cdk--init--sample-app.png)
