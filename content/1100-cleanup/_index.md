---
title: "Clean up"
weight: 11
chapter: false
pre: "<b>11. </b>"
---

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
