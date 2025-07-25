---
title: "CDK app - stack - construct "
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

Let's review the main components of a CDK project by revisiting the diagram in [Introduction](/100-introduction/):

![CDK app and process overview](https://docs.aws.amazon.com/images/cdk/v2/guide/images/AppStacks.png)

1. The overall grouping of your stacks and constructs are known as your _CDK app_.

2. A CDK _stack_ represents a collection of AWS resources that you define using CDK _constructs_.

   When you deploy CDK apps, the resources within a CDK stack are deployed together as an AWS CloudFormation stack.

3. CDK _constructs_ are the basic building blocks of CDK applications.

   - A construct is a component within your application that represents one or more AWS CloudFormation resources and their configuration.
   - You build your application, piece by piece, by importing and configuring constructs.

---

Now have a recap of our CDK `example-app` to see which one is app, stack, and construct:

- The `bin/cdk-workshop.ts` file:

  ```typescript
  #!/usr/bin/env node
  import * as cdk from "aws-cdk-lib"; // 1.1. Import the app
  import { CdkWorkshopStack } from "../lib/cdk-workshop-stack"; // 2.1. Import the stack

  const app = new cdk.App(); // 1.2. Init the app
  new CdkWorkshopStack(app, "CdkWorkshopStack"); // 2.2. Init the stack
  ```

- The `lib/cdk-workshop-stack.ts` file

  ```typescript
  import { Duration, Stack, StackProps } from "aws-cdk-lib";
  import * as sns from "aws-cdk-lib/aws-sns"; // 3.1a Import sns construct
  import * as subs from "aws-cdk-lib/aws-sns-subscriptions"; // 3.2a Import subs construct
  import * as sqs from "aws-cdk-lib/aws-sqs"; // 3.3a Import sqs construct
  import { Construct } from "constructs";

  // 2. The stack
  export class CdkWorkshopStack extends Stack {
    constructor(scope: Construct, id: string, props?: StackProps) {
      super(scope, id, props);

      // 3.1b Define a queue with sns construct
      const queue = new sqs.Queue(this, "CdkWorkshopQueue", {
        visibilityTimeout: Duration.seconds(300),
      });

      // 3.2b Define a topic with sns construct
      const topic = new sns.Topic(this, "CdkWorkshopTopic");

      // 3.3b Define a subscription with subs construct
      topic.addSubscription(new subs.SqsSubscription(queue));
    }
  }
  ```
