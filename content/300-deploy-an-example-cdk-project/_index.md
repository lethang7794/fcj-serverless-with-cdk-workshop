---
title: "Deploy an example CDK project"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

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
