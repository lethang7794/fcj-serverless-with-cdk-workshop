---
title: "Deploy a DynamoDB table"
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Deploy a DynamoDB table

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below `super(scope, id, props);`, add the following code:

  ```ts
  const table = new Table(this, "Users", {
    partitionKey: { name: "id", type: AttributeType.STRING },
  });
  ```

  Don't forget to import `Table` and `AttributeType`:

  ```ts
  import { AttributeType, Table } from "aws-cdk-lib/aws-dynamodb";
  ```

- Next, pass the DynamoDB table name to `UsersListHandler` via environments:

  ```ts
  environment: {
    USERS_TABLE: table.tableName,
  },
  ```

- The code should looks like this:

  ![alt text](/images/workshop-4/dynamodb-table--stack-code.png)

- Run `cdk diff` to compare the local stack with the deploy stack:

  ```
  cdk diff
  ```

  ![alt text](/images/workshop-4/dynamodb-table--cdk-diff.png)

- Run `cdk deploy` to deploy your CDK stack with the DynamoDB table:

  ```shell
  cdk deploy
  ```

  ![alt text](/images/workshop-4/dynamodb-table--cdk-deploy.png)

- Test the `UsersListHandler` Lambda function again, the function should be invoked succeeded. But now the error is a little difference.

  ![alt text](/images/workshop-4/crud--list-users-with-dynamodb-table--test-invoke.png)

  ```json
  {
    "statusCode": 500,
    "headers": {
      "Content-Type": "application/json",
      "Access-Control-Allow-Origin": "*"
    },
    "body": "{\"error\": \"An error occurred (AccessDeniedException) when calling the Scan operation: User: arn:aws:sts::924932512913:assumed-role/CdkWorkshopStack-UsersListHandlerServiceRole364F720-3mtpfexadETu/CdkWorkshopStack-UsersListHandler873A31F9-rzGpbFUQ6Ma1 is not authorized to perform: dynamodb:Scan on resource: arn:aws:dynamodb:ap-southeast-1:924932512913:table/CdkWorkshopStack-Users0A0EEA89-8SX5ZQ8XOH1G because no identity-based policy allows the dynamodb:Scan action\"}"
  }
  ```

  It's still an `AccessDeniedException`, but the ARN of the DynamoDB table is now `arn:aws:dynamodb:ap-southeast-1:924932512913:table/CdkWorkshopStack-Users0A0EEA89-8SX5ZQ8XOH1G`.

> [!NOTE]
> If you use IAM Console to manage the IAM role, policy to allow the Lambda function permission to interact with the DynamoDB table, there will be a lot of work.
