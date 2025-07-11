---
title: "Integrate with `UserDeleteHandler`"
weight: 4
chapter: false
pre: " <b> 10.4 </b> "
---

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below code for integrating with `UsersUpdateHandler`, add the following code:

  ```ts
  // Resource /users/{userId} - DELETE method
  const userDeleteIntegration = new LambdaIntegration(userDeleteHandler, {
    proxy: false,
    requestTemplates: {
      "application/json": `{ "id": "$input.params('userId')" }`,
    },
    integrationResponses: [{ statusCode: "204" }],
  });
  const userIdDelete = userIdResource.addMethod(
    "DELETE",
    userDeleteIntegration,
    {
      methodResponses: [{ statusCode: "204" }],
      requestParameters: {
        "method.request.path.userId": true,
      },
    }
  );
  ```

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
  ┌───┬──────────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
  │   │ Resource                 │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                                   │
  ├───┼──────────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
  │ + │ ${UserDeleteHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                                │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/DELETE/users/*" │
  │   │                          │        │                       │                                  │ }                                                                                                                                                           │
  │ + │ ${UserDeleteHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                                │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/DELETE/users/*"                │
  │   │                          │        │                       │                                  │ }                                                                                                                                                           │
  └───┴──────────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
  (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

  Resources
  [-] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F131f172fd77abff42b586693db143b8d3 destroy
  [+] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F1da787b0e22923f2d02fb73fd15a444cb
  [+] AWS::Lambda::Permission UsersAPI/Default/users/{userId}/DELETE/ApiPermission.CdkWorkshopStackUsersAPI1B3B5CD1.DELETE..users.{userId} UsersAPIusersuserIdDELETEApiPermissionCdkWorkshopStackUsersAPI1B3B5CD1DELETEusersuserId0E8D92B1
  [+] AWS::Lambda::Permission UsersAPI/Default/users/{userId}/DELETE/ApiPermission.Test.CdkWorkshopStackUsersAPI1B3B5CD1.DELETE..users.{userId} UsersAPIusersuserIdDELETEApiPermissionTestCdkWorkshopStackUsersAPI1B3B5CD1DELETEusersuserId1836D96D
  [+] AWS::ApiGateway::Method UsersAPI/Default/users/{userId}/DELETE UsersAPIusersuserIdDELETEB98699CF
  [~] AWS::ApiGateway::Stage UsersAPI/DeploymentStage.prod UsersAPIDeploymentStageprod08341ECA
   └─ [~] DeploymentId
       └─ [~] .Ref:
           ├─ [-] UsersAPIDeploymentD3AE71F131f172fd77abff42b586693db143b8d3
           └─ [+] UsersAPIDeploymentD3AE71F1da787b0e22923f2d02fb73fd15a444cb



  ✨  Number of stacks with differences: 1
  ```

- Run `cdk deploy` to deploy local stack to your AWS account.

  ```shell
  cdk deploy
  ```

  - Output:

  ```shell
  ✨  Synthesis time: 10.56s

  Stack CdkWorkshopStack
  IAM Statement Changes
  ┌───┬──────────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
  │   │ Resource                 │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                                   │
  ├───┼──────────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
  │ + │ ${UserDeleteHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                                │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/DELETE/users/*" │
  │   │                          │        │                       │                                  │ }                                                                                                                                                           │
  │ + │ ${UserDeleteHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                                │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/DELETE/users/*"                │
  │   │                          │        │                       │                                  │ }                                                                                                                                                           │
  └───┴──────────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
  (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)


  Do you wish to deploy these changes (y/n)?
  ```

  - Type in `y` and press `Enter` to confirm.

    ![alt text](/images/workshop-4/api-gateway--UserDeleteHandler--cdk-deploy.png)

- Verify that you can make a `DELETE` request to `/users/{userId}` path.

  ```shell
  curl 'https://b22ew70ek7.execute-api.ap-southeast-1.amazonaws.com/prod/users/9303bb7e-9938-478f-ab02-8a5bfd2406e0' \
    -X DELETE \
    -i
  ```

  > [!NOTE]
  > Replace
  >
  > - the endpoint with yours and notice the `/prod` suffix of the endpoint.
  > - the `9303bb7e-9938-478f-ab02-8a5bfd2406e0` with the id of the created user in [previous step](#integrate-rest-api-gateway-with-usercreatehandler)

  ![alt text](/images/workshop-4/api-gateway--UserDeleteHandler--test.png)
