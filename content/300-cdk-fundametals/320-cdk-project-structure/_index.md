---
title: "CDK project structure"
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

### CDK project structure

The structure of a CDK project in TypeScript looks like this:

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

Let's looks at two important directories in a CDK project: `bin` and `lib`.

#### The `bin` directory

The `bin/cdk-workshop.ts` file is the entry-point of your CDK application:

```typescript
#!/usr/bin/env node
import * as cdk from "aws-cdk-lib";
import { CdkWorkshopStack } from "../lib/cdk-workshop-stack";

const app = new cdk.App();
new CdkWorkshopStack(app, "CdkWorkshopStack");
```

It initializes:

- The CDK app.
- The `CdkWorkshopStack` stack.

> [!NOTE]
> The directory name `cdk-workshop` (where you run `cdk init`) is hard-coded in many places: file name, class name, stack name...

#### The `lib` directory

The `lib/cdk-workshop-stack.ts` file contains a CDK stack - the smallest single unit of deployment.

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

Our CDK **app** was created with a sample CDK **stack** (`CdkWorkshopStack`).

The `CdkWorkshopStack` **stack** includes the following resources:

- Amazon Simple Queue Service (SQS) Queue resource (defined with `sqs.Queue` construct).
- Amazon Simple Notification Service (SNS) Topic resource (defined with `new sns.Topic` construct).
- Subscribes the queue to receive any messages published to the topic - a subscription resource (defined with `topic.addSubscription` construct).
