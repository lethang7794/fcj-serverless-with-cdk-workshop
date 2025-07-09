---
title: "Integrate other CRUD operations for `users` resource with Rest API Gateway"
weight: 8
chapter: false
pre: " <b> 8. </b> "
---

### Integrate Rest API Gateway with `UserCreateHandler`

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

### Integrate Rest API Gateway with `UserGetHandler`

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

### Integrate Rest API Gateway with `UserUpdateHandler`

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

### Integrate Rest API Gateway with `UserDeleteHandler`

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
