---
title: "Workshop 4"
date: 2025-07-09T14:12:25+07:00
---

# Infrastructure as code for serverless app with AWS Cloud Development Kit (CDK)

## Introduction

## Prerequisites

## Deploy an example CDK project

To understand how CDK works, we will deploy an example CDK TypeScript project.

### `cdk init`: Create a new AWS CDK TypeScript project

- Make a new directory and cd to it:

  ```shell
  mkdir cdk-workshop
  cd cdk-workshop
  ```

- Check the available template that CDK init supports:

  ```shell
  cdk init --list
  ```

  ![alt text](/images/workshop-4/cdk--init--list-templates.png)

- Use cdk init to create a new `sample-app` CDK project in TypeScript:

  ```shell
  cdk init sample-app --language=typescript
  ```

  ![alt text](/images/workshop-4/cdk--init--sample-app.png)

### CDK project structure

The structure of of a CDK project in TypeScript looks like this:

```shell
.
├── bin
│   └── cdk-workshop.ts       # An application file: defines your CDK app.
├── lib
│   └── cdk-workshop-stack.ts # A stack file: defines your CDK stack.
├── test
│   └── cdk-workshop.test.ts  # A sample test file for your CDK stack.
├── jest.config.js            # Configuration file for Jest (a JavaScript testing framework).
├── node_modules              # Dependencies of a Node.js project.
│   └── ...
├── package.json              # Metadata file of Node.js project.
├── package-lock.json         # Metadata file for managing dependencies.
├── tsconfig.json             # Configuration file for TypeScript.
├── cdk.json                  # Configuration file for the AWS CDK.
└── README.md                 # Information about the CDK project and how to run it.
```

![alt text](/images/workshop-4/cdk--project-structure.png)

Let's looks at two important directories in a CDK project: `bin` and `lib`.

#### The `bin` directory

```typescript
// bin/cdk-workshop.ts
#!/usr/bin/env node
import * as cdk from "aws-cdk-lib";
import { CdkWorkshopStack } from "../lib/cdk-workshop-stack";

const app = new cdk.App();
new CdkWorkshopStack(app, "CdkWorkshopStack");
```

The `cdk-workshop.ts` file - in the `bin` directory - is the entrypoint of your CDK application.

It initializes:

- The CDK app.
- The `CdkWorkshopStack` stack.

#### The `lib` directory:

The `cdk-workshop-stack.ts` file - in the `lib` directory - is a CDK stack - the smallest single unit of deployment.

```typescript
import { Duration, Stack, StackProps } from "aws-cdk-lib";
import * as sns from "aws-cdk-lib/aws-sns";
import * as subs from "aws-cdk-lib/aws-sns-subscriptions";
import * as sqs from "aws-cdk-lib/aws-sqs";
import { Construct } from "constructs";

export class CdkWorkshopStack extends Stack {
  constructor(scope: Construct, id: string, props?: StackProps) {
    super(scope, id, props);

    const queue = new sqs.Queue(this, "CdkWorkshopQueue", {
      visibilityTimeout: Duration.seconds(300),
    });

    const topic = new sns.Topic(this, "CdkWorkshopTopic");

    topic.addSubscription(new subs.SqsSubscription(queue));
  }
}
```

Our CDK app was created with a sample CDK stack (`CdkWorkshopStack`).

The stack includes:

- Amazon Simple Queue Service (SQS) Queue (`new sqs.Queue`).
- Amazon Simple Notification Service (SNS) Topic (`new sns.Topic`).
- Subscribes the queue to receive any messages published to the topic (`topic.addSubscription`).

> [!NOTE]
> The overall grouping of your stacks and constructs are known as your _CDK app_.

> [!NOTE]
> A CDK _stack_ represents a collection of AWS resources that you define using CDK _constructs_.
>
> - When you deploy CDK apps, the resources within a CDK stack are deployed together as an AWS CloudFormation stack.

> [!NOTE]
> CDK _constructs_ are the basic building blocks of CDK applications.
>
> - A construct is a component within your application that represents one or more AWS CloudFormation resources and their configuration.
> - You build your application, piece by piece, by importing and configuring constructs.

### `cdk synth`: Synthesize a CloudFormation template from your CDK stack

> [!NOTE]
> AWS CDK apps are effectively only a definition of your infrastructure using code.
>
> - When CDK apps are executed, they produce (or "synthesize", in CDK parlance) an AWS CloudFormation template for each stack defined in your application.
> - To synthesize a CDK app, use the `cdk synth` command.

- Synthesize the CloudFormation template from the `cdk-workshop` stack

  ```shell
  cdk synth
  ```

  ```yaml
  # Output
  Resources:
    CdkWorkshopQueue50D9D426:
      Type: AWS::SQS::Queue
      Properties:
        VisibilityTimeout: 300
      UpdateReplacePolicy: Delete
      DeletionPolicy: Delete
      Metadata:
        aws:cdk:path: CdkWorkshopStack/CdkWorkshopQueue/Resource
    CdkWorkshopQueuePolicyAF2494A5:
      Type: AWS::SQS::QueuePolicy
      Properties:
        PolicyDocument:
          Statement:
            - Action: sqs:SendMessage
              Condition:
                ArnEquals:
                  aws:SourceArn:
                    Ref: CdkWorkshopTopicD368A42F
              Effect: Allow
              Principal:
                Service: sns.amazonaws.com
              Resource:
                Fn::GetAtt:
                  - CdkWorkshopQueue50D9D426
                  - Arn
          Version: "2012-10-17"
        Queues:
          - Ref: CdkWorkshopQueue50D9D426
      Metadata:
        aws:cdk:path: CdkWorkshopStack/CdkWorkshopQueue/Policy/Resource
    CdkWorkshopQueueCdkWorkshopStackCdkWorkshopTopicD7BE96438B5AD106:
      Type: AWS::SNS::Subscription
      Properties:
        Endpoint:
          Fn::GetAtt:
            - CdkWorkshopQueue50D9D426
            - Arn
        Protocol: sqs
        TopicArn:
          Ref: CdkWorkshopTopicD368A42F
      DependsOn:
        - CdkWorkshopQueuePolicyAF2494A5
      Metadata:
        aws:cdk:path: CdkWorkshopStack/CdkWorkshopQueue/CdkWorkshopStackCdkWorkshopTopicD7BE9643/Resource
    CdkWorkshopTopicD368A42F:
      Type: AWS::SNS::Topic
      Metadata:
        aws:cdk:path: CdkWorkshopStack/CdkWorkshopTopic/Resource
    CDKMetadata:
      Type: AWS::CDK::Metadata
      Properties:
        Analytics: v2:deflate64:H4sIAAAAAAAA/22QTU/DMAyGfws+TmlgPSDRI+wIErBJHKoJpY03vGVJG6etqqj/HfUDqQcusV77if3aqdw+PcqHO9VxUuprYqiQcR9UeRWq4+/INcv40WCDeYSWmAoyFPoD3dA1ATLYwHAULyc7MWJ6352hss8j1KNiyHLYwHGFzcAg2LKM+6bg0lMVyNk8QnAVlVNfAWh15ciGRVbeBVc6AxlwzSDAq+4NmdUZd2ioRd8v5IlMQD+P+Sf1ReFn+fjs9B/h8UzOLkKj0q8YAvrJ8WrRtV9xGN1O642lSQ3DIKzTKC9836ap3I7nvTBR4hsb6Ibyc46/LeCGIXsBAAA=
      Metadata:
        aws:cdk:path: CdkWorkshopStack/CDKMetadata/Default
      Condition: CDKMetadataAvailable
  Conditions:
    CDKMetadataAvailable:
      # ...
  Parameters:
    BootstrapVersion:
      Type: AWS::SSM::Parameter::Value<String>
      Default: /cdk-bootstrap/hnb659fds/version
      Description: Version of the CDK Bootstrap resources in this environment, automatically retrieved from SSM Parameter Store. [cdk:skip]
  ```

  The structure of the CloudFormation template synthesized from the example CDK app looks like this:

  ![alt text](/images/workshop-4/cdk--synth--structure.png)

  - `CdkWorkshopQueueXXXXXXXX` is the SQS queue.
  - `CdkWorkshopTopicXXXXXXXX` is the SNS topic.
  - `CdkWorkshopQueuePolicyXXXXXXXX` is the IAM policy which allows this topic to send messages to the queue.
  - `CdkWorkshopQueueCdkWorkshopStackCdkWorkshopTopicXXXXXXXXXXXXXXXX` is the subscription between the queue and the topic..

### `cdk bootstrap`: Bootstrap the CDK Toolkit stack

> [!NOTE]
> The first time you deploy an AWS CDK app into an environment (account/region), you need to deploy a "bootstrap stack".
>
> This stack includes resources that are used in the toolkit's operation.
>
> - An S3 bucket that is used to store templates and assets during the deployment process.
> - IAM roles, policies for CDK to deploy the CDK app on your be half.

- Run `cdk bootstrap` to bootstrap CDK Toolkit stack

  ```shell
  cdk bootstrap
  ```

  ```shell
  # Output
  ⏳  Bootstrapping environment aws://924932512913/ap-southeast-1...
  Trusted accounts for deployment: (none)
  Trusted accounts for lookup: (none)
  Using default execution policy of 'arn:aws:iam::aws:policy/AdministratorAccess'. Pass '--cloudformation-execution-policies' to customize.
  CDKToolkit: creating CloudFormation changeset...
   ✅  Environment aws://924932512913/ap-southeast-1 bootstrapped.
  ```

  ![alt text](/images/workshop-4/cdk--boostrap.png)

- A CloudFormation stack (`CDKToolkit`) has been created in your AWS account.

  ![alt text](/images/workshop-4/cdk--bootstrap--cfn-stack.png)

### `cdk deploy`: Deploy CDK stacks in your AWS account

- Now you will deploy your `cdk-workshop` app

  ```shell
  cdk deploy
  ```

  ![alt text](/images/workshop-4/cdk--deploy--iam-permissions-notice.png)

  - You will first be informed of security-related changes that the CDK is going to perform on your behalf, if there are any security-related changes.

  - To confirm, type in `y` then press `Enter`.

    ![alt text](/images/workshop-4/cdk--deploy--confirm.png)

  - A new CloudFormation stack which corresponding to your CDK stack are deployed in your AWS account.

    ![alt text](/images/workshop-4/cdk--deploy--cfn-stack.png)

## Deploy a hello world Lambda function

- Create a `lambda` directory in root of your CDK project.
- In the `lambda` directory, create a `hello.mjs` file with the following content:

  ```mjs
  export async function handler(event) {
    console.log("request:", JSON.stringify(event, undefined, 2));
    return {
      statusCode: 200,
      headers: { "Content-Type": "text/plain" },
      body: `Hi, CDK! You've hit ${event.path}\n`,
    };
  }
  ```

- Update the main stack at `lib/cdk-workshop-stack.ts`:

  ```ts
  import { Stack, StackProps } from "aws-cdk-lib";
  import { Construct } from "constructs";
  import { Code, Function, Runtime } from "aws-cdk-lib/aws-lambda";

  export class CdkWorkshopStack extends Stack {
    constructor(scope: Construct, id: string, props?: StackProps) {
      super(scope, id, props);

      const hello = new Function(this, "HelloHandler", {
        runtime: Runtime.NODEJS_22_X,
        code: Code.fromAsset("lambda"),
        handler: "hello.handler",
      });
    }
  }
  ```

  Your CDK stack now contains a `HelloHandler` Lambda function that:

  - use the code from `lambda` directory.
  - the file is `hello`.
  - the function name is `handler`.

- Use `cdk diff` to compare the difference between the stack in your CDK app with the deployed CDK stack (in your AWS account)

  ```shell
  cdk diff
  ```

  ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff.png)

  - The IAM statement and resources in red will be deleted.

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--resources-to-be-deleted.png)

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--IAM-statement-to-be-deleted.png)

  - The IAM statement, IAM policy and resources in green will be created.

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--IAM-statement-to-be-created.png)

    ![alt text](/images/workshop-4/hello-world-lambda--cdk-diff--resources-to-be-created.png)

- Run `cdk deploy` to deploy your CDK stack with the hello world lambda function.

  ![alt text](/images/workshop-4/hello-world-lambda--cdk-deploy.png)

> [!NOTE]
> If you have deployed the CDK stack (by running `cdk deploy`), `cdk deploy` will update the deployed stack to match the local stack.
>
> In other words, CDK will
>
> - compare the local stack with the deployed stack (to get a change set)
> - apply these change set to your deployed stack

- To confirm, type `y` and press `Enter`.

  ![alt text](/images/workshop-4/hello-world-lambda--cdk-deploy-confirm.png)

- Check the `CdkWorkshopStack` in CloudFormation Console to verify that `HelloHandler` and a `HelloHandlerServiceRole` is created.

  ![alt text](/images/workshop-4/hello-world-lambda--deloyed-cfn-stack.png)

- Click on the `HelloHandler` Physical Id to open the detail page of the Lambda function.

  ![alt text](/images/workshop-4/hello-world-lambda--detail.png)

- Open the `Test` tab, click `Test` button to invoke your `HelloHandler`.

  ![alt text](/images/workshop-4/hello-world-lambda--test-invoke.png)

- Check the detail to verify `HelloHandler` is invoked succeeded.

  ![alt text](/images/workshop-4/hello-world-lambda--test-executing-detail.png)

- Now, you have used CDK to deploy
  - a Lambda function
  - a SQS queue
  - a SNS topic
- In the next step, you will deploy your `list-users` handler that interact with a DynamoDB.

## Deploy `UsersListHandler` Lambda function

- Copy the source code of our lambda functions from this link, and extract to root of your CDK app repo.<!-- TODO: add link to source code -->

  Your repo should looks like this:

  ![alt text](/images/workshop-4/crud--source-code.png)

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

- Run `cdk diff` to see the different between your local stack and the deployed stack.

  ![alt text](/images/workshop-4/crud--list-users--cdk-diff.png)

- Run `cdk deploy` to deploy the local stack into your AWS account.

  ![alt text](/images/workshop-4/crud--list-users--cdk-deploy.png)

- Type in `y` and press `Enter` to confirm.

- You should see something like this:

  ![alt text](/images/workshop-4/crud--list-users--cdk-deploy--result.png)

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

## Deploy a DynamoDB

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

## Allow Lambda function to interact with DynamoDB

> [!NOTE]
> To allow a Lambda function the permissions to interact with a DynamoDB table, you only needs a single line of code.

- In the CDK stack file (`cdk-workshop-stack.ts`), inside the constructor and below code for `UsersListHandler` Lambda function, add the following code:

  ```ts
  table.grantReadData(usersList);
  ```

  Your code should looks like this:

  ![alt text](/images/workshop-4/dynamodb-table--grant-permissions--stack-code.png)

- Run `cdk diff` to see the change set

  ```shell
  cdk diff
  ```

  ![alt text](/images/workshop-4/dynamodb-table--grant-permissions--cdk-diff.png)

- Run `cdk deploy` to deploy your CDK stack with the IAM permissions.

  ```shell
  cdk deploy
  ```

  ![alt text](/images/workshop-4/dynamodb-table--grant-permissions--cdk-deploy.png)

- Test the `UsersListHandler` Lambda function again, the function should be invoked succeeded with a `200` status.

  ![alt text](/images/workshop-4/crud--list-users-with-dynamodb-table-and-permissions--test-invoke.png)

## Deploys all CRUD with Lambda functions

> [!NOTE]
> In previous step, you have deployed the `UsersListHandler`, in this step you will complete the CRUD operations for `users` resource with other lambda functions:
>
> - `UserGetHandler`
> - `UserCreateHandler`
> - `UserUpdateHandler`
> - `UserDeleteHandler`

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

- Run `cdk deploy` to deploy your CDK stack with lambda functions and the IAM permissions for other CRUD operations.

  ```shell
  cdk deploy
  ```

- Go to CloudFormation Console, check `CdkWorkshopStack`, in the `Resources` tab, you will see `UsersListHandler` and other Lambda functions.

  ![alt text](/images/workshop-4/crud--all-operations--cfn-stack.png)

## Deploy an API Gateway

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

> [!TIP]
> Although you rename variables in your CDK stack. There is nothing changes in perspective of the deployed stack.
>
> If you run `cdk diff`, there is no differences.
>
> ![alt text](/images/workshop-4/lambda-functions--refactor--cdk-diff.png)

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

## Integrate other CRUD operations for `users` resource with Rest API Gateway

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

## Cleanup

This workshop is very long, but the cleanup is very quick.

- For most of the resources, you need only one command `cdk destroy`:

  - Inside the CDK app, run `cdk destroy`:

    ```
    cdk destroy
    ```

  - Type in `y` and press `Enter` to confirm:

    ![alt text](/images/workshop-4/cleanup--cdk-destroy.png)

  - Go to CloudFormation Console' _Stacks_ section, change the `Filter status` to `Deleted`, you should see the `CdkWorkshopStack` with the status of `DELETE_COMPLETE`.

    ![alt text](/images/workshop-4/cleanup--deleted-cloudformation-stack.png)

- The only other resource you need to manually delete is the DynamoDB table.

  - Go to DynamoDB Console's _Tables_ section, select the `CdkWorkshopStack-UsersXXXXXXXX-XXXXXXXXXXXXX` table, click `Delete`.

    ![alt text](/images/workshop-4/cleanup--dynamodb-table.png)

  - Type `confirm` and click `Delete`.

    ![alt text](/images/workshop-4/cleanup--dynamodb-table--confirm.png)

## Summary

In this workshop, you've learned about:

- AWS CDK: The IaC solution from AWS that model application infrastructure using programming languages (TypeScript, Python, Java, and .NET).

- The main concepts of CDK: _app_, _stack_, _construct_.

- The structure of an CDK app: `bin`, `lib` directories...

- AWS Toolkit CLI's commands:

  - `cdk init`
  - `cdk bootstrap`
  - `cdk synth`
  - `cdk diff`
  - `cdk deploy`
  - `cdk destroy`

- Using CDK with TypeScript to deploy:
  - A hello world Lambda function.
  - A DynamoDB table.
  - Grant permission to interact with DynamoDB table for a Lambda function.
  - Lambda functions for CRUD operations for `users`.
  - A REST API Gateway and integrate with Lambda functions.

> [!TIP]
> Now you have the infrastructure for the whole workshop as code (it's infrastructure as code), you can quickly re-deploy it to your AWS account.
>
> - Try running `cdk diff` and `cdk deploy` to see the power of CDK (and infrastructure as code).
