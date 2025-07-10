---
title: "Deploy an API Gateway"
weight: 9
chapter: false
pre: " <b> 9. </b> "
---

{{% toc %}}

### Preparation

Before introduce the API Gateway, we will update our stack code to distinguish between the Lambda functions and the API Gateway method integrations.

- Rename the variables from:

  - `hello` to `helloHandler`.
  - `usersList` to `usersListHandler`.
  - `userGet` to `userGetHandler`.
  - `userCreate` to `userCreateHandler`.
  - `userUpdate` to `userUpdateHandler`.
  - `userDelete` to `userDeleteHandler`.

- Your code should now look like this:

  ![alt text](/images/workshop-4/lambda-functions--refactor.png)

- If you run `cdk diff`, there is no differences.

  ![alt text](/images/workshop-4/lambda-functions--refactor--cdk-diff.png)

> [!TIP]
> Although you rename variables in your CDK stack. There is nothing changes in perspective of the deployed stack.

### Deploy a Rest API Gateway and integrate with `UsersListHandler`

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below code for granting permissions to Lambda functions, add the following code:

  ```ts
  // Rest API Gateway with Lambda integration
  const gateway = new LambdaRestApi(this, "UsersAPI", {
    handler: helloHandler,
    proxy: false,
  });

  // Resource /users
  const usersResource = gateway.root.addResource("users");

  // Resource /users - GET method
  const usersListIntegration = new LambdaIntegration(usersListHandler, {
    proxy: false,
    integrationResponses: [{ statusCode: "200" }],
  });
  const usersGet = usersResource.addMethod("GET", usersListIntegration, {
    methodResponses: [{ statusCode: "200" }],
  });
  ```

- Add the import to the top of the stack file:

  ```ts
  import { LambdaIntegration, LambdaRestApi } from "aws-cdk-lib/aws-apigateway";
  ```

- Your CDK stack file should now look like this:

  ![alt text](/images/workshop-4/api-gateway--UsersListHandler--cdk-stack.png)

- Run `cdk deploy` to deploy deploy your local stack

  ```shell
  cdk deploy
  ```

  - Output

    ```shell
    ✨  Synthesis time: 9.99s

    Stack CdkWorkshopStack
    IAM Statement Changes
    ┌───┬─────────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
    │   │ Resource                │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                              │
    ├───┼─────────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
    │ + │ ${UsersListHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                           │
    │   │                         │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/GET/users" │
    │   │                         │        │                       │                                  │ }                                                                                                                                                      │
    │ + │ ${UsersListHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                           │
    │   │                         │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/GET/users"                │
    │   │                         │        │                       │                                  │ }                                                                                                                                                      │
    └───┴─────────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
    (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)


    Do you wish to deploy these changes (y/n)?
    ```

  - Type `y` and press `Enter` to confirm.

    ![alt text](/images/workshop-4/api-gateway--UsersListHandler--cdk-deploy.png)

- Copy the `UsersAPIEndpoint` from deploy outputs.

- To test the API Gateway, invoke a GET HTTP request to the `/users` path (of the `UsersAPIEndpoint`).

  ```shell
  curl https://b22ew70ek7.execute-api.ap-southeast-1.amazonaws.com/prod/users
  ```

  > [!NOTE]
  > Replace the endpoint with yours and notice the `/prod` suffix of the endpoint.

  ![alt text](/images/workshop-4/api-gateway--UsersListHandler--test.png)
