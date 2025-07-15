---
title: "cdk deploy"
weight: 6
chapter: false
pre: " <b> 3.6. </b> "
---

1. Now you will deploy your `cdk-workshop` app

   ```shell
   cdk deploy
   ```

   ![alt text](/images/workshop-4/cdk--deploy--iam-permissions-notice.png)

2. You will first be informed of security-related changes that the CDK is going to perform on your behalf, if there are any security-related changes.

3. To confirm, type in `y` then press `Enter`.

   ![alt text](/images/workshop-4/cdk--deploy--confirm.png)

4. A new CloudFormation stack which corresponding to your CDK stack are deployed in your AWS account.

   ![alt text](/images/workshop-4/cdk--deploy--cfn-stack.png)
