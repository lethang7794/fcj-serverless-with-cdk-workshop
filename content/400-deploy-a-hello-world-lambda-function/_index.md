---
title: "Deploy a hello world Lambda function"
weight: 4
chapter: false
pre: " <b> 4. </b> "
---

{{% toc %}}

### Add function code for Lambda function

- Create a `lambda` directory in root of your CDK project.
- In the `lambda` directory, create a `hello.mjs` file with the following content:

  ```mjs
  export async function handler(event) {
    console.log("request:", JSON.stringify(event, undefined, 2));
    return {
      statusCode: 200,
      headers: { "Content-Type": "text/plain" },
      body: `Hi, CDK! You've hit ${event.path}\n`,
    };
  }
  ```

### Add CDK code for Lambda function

- Update the main stack at `lib/cdk-workshop-stack.ts`:

  ```ts
  import { Stack, StackProps } from "aws-cdk-lib";
  import { Construct } from "constructs";
  import { Code, Function, Runtime } from "aws-cdk-lib/aws-lambda";

  export class CdkWorkshopStack extends Stack {
    constructor(scope: Construct, id: string, props?: StackProps) {
      super(scope, id, props);

      const hello = new Function(this, "HelloHandler", {
        runtime: Runtime.NODEJS_22_X,
        code: Code.fromAsset("lambda"),
        handler: "hello.handler",
      });
    }
  }
  ```

  Your CDK stack now contains a `HelloHandler` Lambda function that:

  - use the code from `lambda` directory.
  - the file is `hello`.
  - the function name is `handler`.

- Use `cdk diff` to compare the difference between the stack in your CDK app with the deployed CDK stack (in your AWS account)

  ```shell
  cdk diff
  ```

  ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff.png)

  - The IAM statement and resources in red will be deleted.

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--resources-to-be-deleted.png)

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--IAM-statement-to-be-deleted.png)

  - The IAM statement, IAM policy and resources in green will be created.

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--IAM-statement-to-be-created.png)

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--resources-to-be-created.png)

### Deploy CDK stack to update your CDK stack

- Run `cdk deploy` to deploy your CDK stack with the hello world lambda function.

  ![alt text](/images/workshop-4/hello-world-lambda--cdk-deploy.png)

> [!NOTE]
> If you have deployed the CDK stack (by running `cdk deploy`), `cdk deploy` will update the deployed stack to match the local stack. In other words, CDK will: 1. compare the local stack with the deployed stack (to get a change set); 2. apply these change set to your deployed stack

- To confirm, type `y` and press `Enter`.

  ![alt text](/images/workshop-4/hello-world-lambda--cdk-deploy-confirm.png)

### Verify Lambda function is deployed and test it

- Check the `CdkWorkshopStack` in CloudFormation Console to verify that `HelloHandler` and a `HelloHandlerServiceRole` is created.

  ![alt text](/images/workshop-4/hello-world-lambda--deloyed-cfn-stack.png)

- Click on the `HelloHandler` Physical Id to open the detail page of the Lambda function.

  ![alt text](/images/workshop-4/hello-world-lambda--detail.png)

- Open the `Test` tab, click `Test` button to invoke your `HelloHandler`.

  ![alt text](/images/workshop-4/hello-world-lambda--test-invoke.png)

- Check the detail to verify `HelloHandler` is invoked succeeded.

  ![alt text](/images/workshop-4/hello-world-lambda--test-executing-detail.png)

- Now, you have used CDK to deploy
  - a Lambda function
  - a SQS queue
  - a SNS topic
- In the next step, you will deploy your `list-users` handler that interact with a DynamoDB.
