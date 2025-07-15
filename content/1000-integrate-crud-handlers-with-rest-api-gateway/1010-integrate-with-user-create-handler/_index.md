---
title: "Integrate UserCreateHandler"
weight: 1
chapter: false
pre: " <b> 10.1 </b> "
---

{{% toc %}}

### Add CDK code for integration

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below code for integrating with `UsersListHandler`, add the following code:

  ```ts
  // Resource /users - POST method
  const userCreateIntegration = new LambdaIntegration(userCreateHandler, {
    proxy: false,
    integrationResponses: [{ statusCode: "201" }],
  });
  const usersPost = usersResource.addMethod("POST", userCreateIntegration, {
    methodResponses: [{ statusCode: "201" }],
  });
  ```

### [Optional] Run cdk diff to check the difference

- Run `cdk diff` to compare local stack to deployed stack.

  ```shell
  cdk diff
  ```

  Output

  ```shell
  start: Building CdkWorkshopStack Template
  success: Built CdkWorkshopStack Template
  start: Publishing CdkWorkshopStack Template (current_account-current_region)
  success: Published CdkWorkshopStack Template (current_account-current_region)
  Hold on while we create a read-only change set to get a diff with accurate replacement information (use --no-change-set to use a less accurate but faster template-only diff)

  Stack CdkWorkshopStack
  IAM Statement Changes
  ┌───┬──────────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
  │   │ Resource                 │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                               │
  ├───┼──────────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
  │ + │ ${UserCreateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                            │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/POST/users" │
  │   │                          │        │                       │                                  │ }                                                                                                                                                       │
  │ + │ ${UserCreateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                            │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/POST/users"                │
  │   │                          │        │                       │                                  │ }                                                                                                                                                       │
  └───┴──────────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
  (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

  Resources
  [-] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F1ae325c60dc8dcba7dad4da76e2fceb3e destroy
  [+] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F1c6d778d19d85104147ecfe03192804ff
  [+] AWS::Lambda::Permission UsersAPI/Default/users/POST/ApiPermission.CdkWorkshopStackUsersAPI1B3B5CD1.POST..users UsersAPIusersPOSTApiPermissionCdkWorkshopStackUsersAPI1B3B5CD1POSTusersBAF016C9
  [+] AWS::Lambda::Permission UsersAPI/Default/users/POST/ApiPermission.Test.CdkWorkshopStackUsersAPI1B3B5CD1.POST..users UsersAPIusersPOSTApiPermissionTestCdkWorkshopStackUsersAPI1B3B5CD1POSTusers662172D4
  [+] AWS::ApiGateway::Method UsersAPI/Default/users/POST UsersAPIusersPOSTE2B1DF30
  [~] AWS::ApiGateway::Stage UsersAPI/DeploymentStage.prod UsersAPIDeploymentStageprod08341ECA
   └─ [~] DeploymentId
       └─ [~] .Ref:
           ├─ [-] UsersAPIDeploymentD3AE71F1ae325c60dc8dcba7dad4da76e2fceb3e
           └─ [+] UsersAPIDeploymentD3AE71F1c6d778d19d85104147ecfe03192804ff



  ✨  Number of stacks with differences: 1
  ```

### Run cdk deploy to deploy your CDK stack

- Run `cdk deploy` to deploy local stack to your AWS account.

  ```shell
  cdk deploy
  ```

  - Output:

    ```shell
    ✨  Synthesis time: 10.23s

    Stack CdkWorkshopStack
    IAM Statement Changes
    ┌───┬──────────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
    │   │ Resource                 │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                               │
    ├───┼──────────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
    │ + │ ${UserCreateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                            │
    │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/POST/users" │
    │   │                          │        │                       │                                  │ }                                                                                                                                                       │
    │ + │ ${UserCreateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                            │
    │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/POST/users"                │
    │   │                          │        │                       │                                  │ }                                                                                                                                                       │
    └───┴──────────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
    (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)


    Do you wish to deploy these changes (y/n)? y
    ```

  - Type in `y` and press `Enter`.

    ![alt text](/images/workshop-4/api-gateway--UserCreateHandler--cdk-deploy.png)

### Test the API Gateway integration and method

- Verify that you can make a POST request to `/users` path

  ```shell
  curl 'https://b22ew70ek7.execute-api.ap-southeast-1.amazonaws.com/prod/users' \
    -X POST -H 'content-type: application/json' \
    -d '{ "name": "First Cloud Journey", "email": "fcj@example.com" }'
  ```

  > [!NOTE]
  > Replace the endpoint with yours and notice the `/prod` suffix of the endpoint.

  ![alt text](/images/workshop-4/api-gateway--UserCreateHandler--test.png)

> [!TIP]
> Copy the `id` of the created user, you will need it in later steps.
