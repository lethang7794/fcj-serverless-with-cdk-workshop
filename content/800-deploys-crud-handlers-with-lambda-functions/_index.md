---
title: "Deploys CRUD handlers with Lambda functions"
weight: 8
chapter: false
pre: " <b> 8. </b> "
---

In previous step, you have deployed the `UsersListHandler`, in this step you will complete the CRUD operations for `users` resource by deploying other Lambda functions:

- `UserGetHandler`
- `UserCreateHandler`
- `UserUpdateHandler`
- `UserDeleteHandler`

---

{{% toc %}}

### Add CDK code for the CRUD handlers

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor:

  - Below code for `UsersListHandler` Lambda function, add the following code:

    ```ts
    const userGet = new Function(this, "UserGetHandler", {
      runtime: Runtime.PYTHON_3_13,
      code: Code.fromAsset("lambda/users/get"),
      handler: "lambda_function.lambda_handler",
      environment: {
        USERS_TABLE: table.tableName,
      },
    });
    const userCreate = new Function(this, "UserCreateHandler", {
      runtime: Runtime.PYTHON_3_13,
      code: Code.fromAsset("lambda/users/create"),
      handler: "lambda_function.lambda_handler",
      environment: {
        USERS_TABLE: table.tableName,
      },
    });
    const userUpdate = new Function(this, "UserUpdateHandler", {
      runtime: Runtime.PYTHON_3_13,
      code: Code.fromAsset("lambda/users/update"),
      handler: "lambda_function.lambda_handler",
      environment: {
        USERS_TABLE: table.tableName,
      },
    });
    const userDelete = new Function(this, "UserDeleteHandler", {
      runtime: Runtime.PYTHON_3_13,
      code: Code.fromAsset("lambda/users/delete"),
      handler: "lambda_function.lambda_handler",
      environment: {
        USERS_TABLE: table.tableName,
      },
    });
    ```

  - Below line of `table.grantReadData(usersList);`, add the following code:

    ```ts
    table.grantReadData(userGet);
    table.grantReadWriteData(userCreate);
    table.grantReadWriteData(userUpdate);
    table.grantReadWriteData(userDelete);
    ```

### [Optional] Run `cdk diff` to check the difference

- Run `cdk diff` to see the change set

  ```shell
  cdk diff
  ```

![alt text](/images/workshop-4/crud--all-operations--cdk-diff-1.png)

![alt text](/images/workshop-4/crud--all-operations--cdk-diff-2.png)

### Run `cdk deploy` to deploy your CDK stack

- Run `cdk deploy` to deploy your CDK stack with lambda functions and the IAM permissions for other CRUD operations.

  ```shell
  cdk deploy
  ```

![alt text](/images/workshop-4/crud--all-operations--cdk-deploy-1.png)

![alt text](/images/workshop-4/crud--all-operations--cdk-deploy-2.png)

- Type in `y` and press `Enter`.

![alt text](/images/workshop-4/crud--all-operations--cdk-deploy-confirm.png)

### Verify the handlers are deployed

- Go to CloudFormation Console, check `CdkWorkshopStack`, in the `Resources` tab, you will see `UsersListHandler` and other Lambda functions.

  ![alt text](/images/workshop-4/crud--all-operations--cfn-stack.png)
