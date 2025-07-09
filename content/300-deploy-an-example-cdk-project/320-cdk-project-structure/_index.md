---
title: "CDK project structure"
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

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
