---
title: "cdk synth"
weight: 4
chapter: false
pre: " <b> 3.4. </b> "
---

## Why `cdk synth`?

The AWS CDK utilizes the AWS CloudFormation service to perform deployments.

AWS CDK apps are effectively only a definition of your infrastructure using CloudFormation code.

- When CDK apps are executed, they produce (or "synthesize", in CDK parlance) an AWS CloudFormation template for each stack defined in your application.
- To synthesize a CDK app, use the `cdk synth` command.

## Synthesize CloudFormation template for your `cdk-workshop` stack

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
