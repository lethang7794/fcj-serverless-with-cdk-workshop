---
title: "Allow Lambda function to interact with DynamoDB"
weight: 7
chapter: false
pre: " <b> 7. </b> "
---

{{% toc %}}

> [!NOTE]
> To allow a Lambda function the permissions to interact with a DynamoDB table, you only needs a single line of code.

### Add CDK code to grant Lambda function the permissions to interact with DynamoDB table

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below code for `UsersListHandler` Lambda function, add the following code:

  ```ts
  table.grantReadData(usersList);
  ```

  Your code should looks like this:

  ![alt text](/images/workshop-4/dynamodb-table--grant-permissions--stack-code.png)

### [Optional] Run cdk diff to check the difference of your stack

- Run `cdk diff` to see the change set

  ```shell
  cdk diff
  ```

  ![alt text](/images/workshop-4/dynamodb-table--grant-permissions--cdk-diff.png)

### Run cdk deploy to deploy your CDK stack that grant Lambda function permissions

- Run `cdk deploy` to deploy your CDK stack with the IAM permissions.

  ```shell
  cdk deploy
  ```

  ![alt text](/images/workshop-4/dynamodb-table--grant-permissions--cdk-deploy.png)

- Test the `UsersListHandler` Lambda function again, the function should be invoked succeeded with a `200` status.

  ![alt text](/images/workshop-4/crud--list-users-with-dynamodb-table-and-permissions--test-invoke.png)
