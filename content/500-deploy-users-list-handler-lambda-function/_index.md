---
title: "Deploy UsersListHandler Lambda function"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

Now we will deploy another Lambda function - the `UsersListHanlder`.

{{% toc %}}

### Add function code for Lambda function

- Copy the source code of our lambda functions from this link, and extract to root of your CDK app repo. <!-- TODO: add link to source code -->

  Your repo should looks like this:

  ![alt text](/images/workshop-4/crud--source-code.png)

### Add CDK code for Lambda function

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below the code for `HelloHandler`, add the following code:

  ```ts
  const usersList = new Function(this, "UsersListHandler", {
    runtime: Runtime.PYTHON_3_13,
    code: Code.fromAsset("lambda/users/list"),
    handler: "lambda_function.lambda_handler",
  });
  ```

  The code should looks like this:

  ![alt text](/images/workshop-4/crud--list-users--code.png)

### Check the diff to your local stack

- Run `cdk diff` to see the different between your local stack and the deployed stack.

  ![alt text](/images/workshop-4/crud--list-users--cdk-diff.png)

### Deploy CDK stack to update your CDK stack

- Run `cdk deploy` to deploy the local stack into your AWS account.

  ![alt text](/images/workshop-4/crud--list-users--cdk-deploy.png)

- Type in `y` and press `Enter` to confirm.

- You should see something like this:

  ![alt text](/images/workshop-4/crud--list-users--cdk-deploy--result.png)

### Verify Lambda function is deploy and test it

- Go to CloudFormation Console, check `CdkWorkshopStack`, in the `Resources` tab, you will see your `UsersListHandler`.

- Click on the Physical Id of `UsersListHandlerXXXXXXX` resource to go to the detail of your `UsersListHandler` Lambda function.

  ![alt text](/images/workshop-4/crud--list-users--cfn-stack.png)

- Open the `Test` tab, click `Test` to invoke your Lambda function.

  ![alt text](/images/workshop-4/crud--list-users--test-invoke.png)

- Click `Details` to see the detail of the executing.

  Although the function executes succeeded, the status is `500`.

  The full response is:

  ```json
  {
    "statusCode": 500,
    "headers": {
      "Content-Type": "application/json",
      "Access-Control-Allow-Origin": "*"
    },
    "body": "{\"error\": \"An error occurred (AccessDeniedException) when calling the Scan operation: User: arn:aws:sts::924932512913:assumed-role/CdkWorkshopStack-UsersListHandlerServiceRole364F720-3mtpfexadETu/CdkWorkshopStack-UsersListHandler873A31F9-rzGpbFUQ6Ma1 is not authorized to perform: dynamodb:Scan on resource: arn:aws:dynamodb:ap-southeast-1:924932512913:table/UsersTable because no identity-based policy allows the dynamodb:Scan action\"}"
  }
  ```

  Let's look at the `error` in `body`:

  - An error occurred (`AccessDeniedException`) when calling the Scan operation:
  - `User: arn:aws:sts::924932512913:assumed-role/CdkWorkshopStack-UsersListHandlerServiceRole364F720-3mtpfexadETu/CdkWorkshopStack-UsersListHandler873A31F9-rzGpbFUQ6Ma1`
  - is not authorized to perform: `dynamodb:Scan`
  - on resource: `arn:aws:dynamodb:ap-southeast-1:924932512913:table/UsersTable`
  - because no identity-based policy allows the dynamodb:Scan action

  The Lambda function don't have permissions to interact with the DynamoDB table.

- But your CDK stack doesn't have any DynamoDB table, let deploy it first.
