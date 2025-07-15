---
title: "Integrate UserGetHandler"
weight: 2
chapter: false
pre: " <b> 10.2 </b> "
---

{{% toc %}}

### Add CDK code for integration

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below code for integrating with `UsersCreateHandler`, add the following code:

  ```ts
  // Resource /users/{userId}
  const userIdResource = usersResource.addResource("{userId}");

  // Resource /users/{userId} - GET method
  const userGetIntegration = new LambdaIntegration(userGetHandler, {
    proxy: false,
    requestTemplates: {
      "application/json": `{ "id": "$input.params('userId')" }`,
    },

    integrationResponses: [{ statusCode: "200" }],
  });
  const userIdGet = userIdResource.addMethod("GET", userGetIntegration, {
    methodResponses: [{ statusCode: "200" }],
    requestParameters: {
      "method.request.path.userId": true,
    },
  });
  ```

### [Optional] Run `cdk diff` to check the difference

- Run `cdk diff` to compare local stack to deployed stack.

  ```shell
  cdk diff
  ```

  Output:

  ```shell
  start: Building CdkWorkshopStack Template
  success: Built CdkWorkshopStack Template
  start: Publishing CdkWorkshopStack Template (current_account-current_region)
  success: Published CdkWorkshopStack Template (current_account-current_region)
  Hold on while we create a read-only change set to get a diff with accurate replacement information (use --no-change-set to use a less accurate but faster template-only diff)

  Stack CdkWorkshopStack
  IAM Statement Changes
  ┌───┬───────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
  │   │ Resource              │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                                │
  ├───┼───────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
  │ + │ ${UserGetHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                             │
  │   │                       │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/GET/users/*" │
  │   │                       │        │                       │                                  │ }                                                                                                                                                        │
  │ + │ ${UserGetHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                             │
  │   │                       │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/GET/users/*"                │
  │   │                       │        │                       │                                  │ }                                                                                                                                                        │
  └───┴───────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
  (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

  Resources
  [-] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F1c6d778d19d85104147ecfe03192804ff destroy
  [+] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F1f87d0b07f7184c3208c494cf982ac317
  [+] AWS::ApiGateway::Resource UsersAPI/Default/users/{userId} UsersAPIusersuserId455D2208
  [+] AWS::Lambda::Permission UsersAPI/Default/users/{userId}/GET/ApiPermission.CdkWorkshopStackUsersAPI1B3B5CD1.GET..users.{userId} UsersAPIusersuserIdGETApiPermissionCdkWorkshopStackUsersAPI1B3B5CD1GETusersuserId6B262619
  [+] AWS::Lambda::Permission UsersAPI/Default/users/{userId}/GET/ApiPermission.Test.CdkWorkshopStackUsersAPI1B3B5CD1.GET..users.{userId} UsersAPIusersuserIdGETApiPermissionTestCdkWorkshopStackUsersAPI1B3B5CD1GETusersuserId3206B5D2
  [+] AWS::ApiGateway::Method UsersAPI/Default/users/{userId}/GET UsersAPIusersuserIdGET86F536F6
  [~] AWS::ApiGateway::Stage UsersAPI/DeploymentStage.prod UsersAPIDeploymentStageprod08341ECA
   └─ [~] DeploymentId
       └─ [~] .Ref:
           ├─ [-] UsersAPIDeploymentD3AE71F1c6d778d19d85104147ecfe03192804ff
           └─ [+] UsersAPIDeploymentD3AE71F1f87d0b07f7184c3208c494cf982ac317



  ✨  Number of stacks with differences: 1
  ```

### Run `cdk deploy` to deploy your CDK stack

- Run `cdk deploy` to deploy local stack to your AWS account.

  ```shell
  cdk deploy
  ```

  - Output:

    ```shell
    ✨  Synthesis time: 9.13s

    Stack CdkWorkshopStack
    IAM Statement Changes
    ┌───┬───────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
    │   │ Resource              │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                                │
    ├───┼───────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
    │ + │ ${UserGetHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                             │
    │   │                       │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/GET/users/*" │
    │   │                       │        │                       │                                  │ }                                                                                                                                                        │
    │ + │ ${UserGetHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                             │
    │   │                       │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/GET/users/*"                │
    │   │                       │        │                       │                                  │ }                                                                                                                                                        │
    └───┴───────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
    (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)


    Do you wish to deploy these changes (y/n)?
    ```

  - Type in `y` and press `Enter` to confirm.

    ![alt text](/images/workshop-4/api-gateway--UserGetHandler--cdk-deploy.png)

### Test the API Gateway integration and method

- Verify that you can make a `GET` request to `/users/{userId}` path.

  ```shell
  curl 'https://b22ew70ek7.execute-api.ap-southeast-1.amazonaws.com/prod/users/9303bb7e-9938-478f-ab02-8a5bfd2406e0'
  ```

  > [!NOTE]
  > Replace
  >
  > - the endpoint with yours and notice the `/prod` suffix of the endpoint.
  > - the `9303bb7e-9938-478f-ab02-8a5bfd2406e0` with the id of the created user in [previous step](#integrate-rest-api-gateway-with-usercreatehandler)

  ![alt text](/images/workshop-4/api-gateway--UserGetHandler--test.png)
