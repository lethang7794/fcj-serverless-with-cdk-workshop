---
title: "Integrate UserUpdateHandler"
weight: 3
chapter: false
pre: " <b> 10.3 </b> "
---

{{% toc %}}

### Add CDK code for integration

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below code for integrating with `UsersGetHandler`, add the following code:

  ```ts
  // Resource /users/{userId} - PATCH method
  const userUpdateIntegration = new LambdaIntegration(userUpdateHandler, {
    proxy: false,
    requestTemplates: {
      "application/json": `
            #set($inputRoot = $input.path('$'))
            {
              #foreach($key in $inputRoot.keySet())
                "$key": "$util.escapeJavaScript($inputRoot.get($key))"#if($foreach.hasNext),#end
              #end,
              "id": "$input.params('userId')"
            }
          `,
    },
    integrationResponses: [{ statusCode: "200" }],
  });
  const userIdPatch = userIdResource.addMethod("PATCH", userUpdateIntegration, {
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
  ┌───┬──────────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
  │   │ Resource                 │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                                  │
  ├───┼──────────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
  │ + │ ${UserUpdateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                               │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/PATCH/users/*" │
  │   │                          │        │                       │                                  │ }                                                                                                                                                          │
  │ + │ ${UserUpdateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                               │
  │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/PATCH/users/*"                │
  │   │                          │        │                       │                                  │ }                                                                                                                                                          │
  └───┴──────────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
  (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)

  Resources
  [-] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F1f87d0b07f7184c3208c494cf982ac317 destroy
  [+] AWS::ApiGateway::Deployment UsersAPI/Deployment UsersAPIDeploymentD3AE71F131f172fd77abff42b586693db143b8d3
  [+] AWS::Lambda::Permission UsersAPI/Default/users/{userId}/PATCH/ApiPermission.CdkWorkshopStackUsersAPI1B3B5CD1.PATCH..users.{userId} UsersAPIusersuserIdPATCHApiPermissionCdkWorkshopStackUsersAPI1B3B5CD1PATCHusersuserId1CE51411
  [+] AWS::Lambda::Permission UsersAPI/Default/users/{userId}/PATCH/ApiPermission.Test.CdkWorkshopStackUsersAPI1B3B5CD1.PATCH..users.{userId} UsersAPIusersuserIdPATCHApiPermissionTestCdkWorkshopStackUsersAPI1B3B5CD1PATCHusersuserIdB30B161D
  [+] AWS::ApiGateway::Method UsersAPI/Default/users/{userId}/PATCH UsersAPIusersuserIdPATCH066A04B4
  [~] AWS::ApiGateway::Stage UsersAPI/DeploymentStage.prod UsersAPIDeploymentStageprod08341ECA
   └─ [~] DeploymentId
       └─ [~] .Ref:
           ├─ [-] UsersAPIDeploymentD3AE71F1f87d0b07f7184c3208c494cf982ac317
           └─ [+] UsersAPIDeploymentD3AE71F131f172fd77abff42b586693db143b8d3



  ✨  Number of stacks with differences: 1
  ```

### Run `cdk deploy` to deploy your CDK stack

- Run `cdk deploy` to deploy local stack to your AWS account.

  ```shell
  cdk deploy
  ```

  - Output:

    ```shell
    ✨  Synthesis time: 10.58s

    Stack CdkWorkshopStack
    IAM Statement Changes
    ┌───┬──────────────────────────┬────────┬───────────────────────┬──────────────────────────────────┬────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┐
    │   │ Resource                 │ Effect │ Action                │ Principal                        │ Condition                                                                                                                                                  │
    ├───┼──────────────────────────┼────────┼───────────────────────┼──────────────────────────────────┼────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┤
    │ + │ ${UserUpdateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                               │
    │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/${UsersAPI/DeploymentStage.prod}/PATCH/users/*" │
    │   │                          │        │                       │                                  │ }                                                                                                                                                          │
    │ + │ ${UserUpdateHandler.Arn} │ Allow  │ lambda:InvokeFunction │ Service:apigateway.amazonaws.com │ "ArnLike": {                                                                                                                                               │
    │   │                          │        │                       │                                  │   "AWS:SourceArn": "arn:${AWS::Partition}:execute-api:${AWS::Region}:${AWS::AccountId}:${UsersAPIDF91FF81}/test-invoke-stage/PATCH/users/*"                │
    │   │                          │        │                       │                                  │ }                                                                                                                                                          │
    └───┴──────────────────────────┴────────┴───────────────────────┴──────────────────────────────────┴────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
    (NOTE: There may be security-related changes not in this list. See https://github.com/aws/aws-cdk/issues/1299)


    Do you wish to deploy these changes (y/n)?
    ```

  - Type in `y` and press `Enter` to confirm.

    ![alt text](/images/workshop-4/api-gateway--UserUpdateHandler--cdk-deploy.png)

### Test the API Gateway integration and method

- Verify that you can make a `PATCH` request to `/users/{userId}` path.

  ```shell
  curl 'https://b22ew70ek7.execute-api.ap-southeast-1.amazonaws.com/prod/users/9303bb7e-9938-478f-ab02-8a5bfd2406e0' \
    -X PATCH -H 'content-type: application/json' \
    -d '{ "name": "First Cloud Journey v2", "email": "fcj-v2@aws.com" }'
  ```

  > [!NOTE]
  > Replace
  >
  > - the endpoint with yours and notice the `/prod` suffix of the endpoint.
  > - the `9303bb7e-9938-478f-ab02-8a5bfd2406e0` with the id of the created user in [previous step](#integrate-rest-api-gateway-with-usercreatehandler).

  ![alt text](/images/workshop-4/api-gateway--UserUpdateHandler--test.png)
