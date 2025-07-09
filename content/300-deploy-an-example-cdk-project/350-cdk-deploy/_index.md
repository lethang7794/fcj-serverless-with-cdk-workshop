---
title: "cdk deploy: Deploy CDK stacks in your AWS account"
weight: 2
chapter: false
pre: " <b> 3.5. </b> "
---

### `cdk deploy`: Deploy CDK stacks in your AWS account

- Now you will deploy your `cdk-workshop` app

  ```shell
  cdk deploy
  ```

  ![alt text](/images/workshop-4/cdk--deploy--iam-permissions-notice.png)

  - You will first be informed of security-related changes that the CDK is going to perform on your behalf, if there are any security-related changes.

  - To confirm, type in `y` then press `Enter`.

    ![alt text](/images/workshop-4/cdk--deploy--confirm.png)

  - A new CloudFormation stack which corresponding to your CDK stack are deployed in your AWS account.

    ![alt text](/images/workshop-4/cdk--deploy--cfn-stack.png)
