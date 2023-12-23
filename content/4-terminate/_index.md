---
title: "Cleanup"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4. </b> "
---

#### CloudFormation

1. We go to [CloudFormation Console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringText=&filteringStatus=active&viewNested=true )
2. We will see a stack named **edge-redirect-workshop** created from the CloudFormation template. We select that stack and click on the **Delete** button at the top right.

![VPC](/images/4.clean/4-1new.png)

Wait a few minutes and we have successfully deleted CloudFormation and the resources created by it while we started the workshop.

#### Lambda Function

1. Let's go to [Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions)
2. We will see all the Lambda Functions created in this workshop. Tick all functions then click on the **Actions** button at the top right and select **Delete.**

![VPC](/images/4.clean/4-2new.png)

We enter `delete` in the **Confirm** box and finally press the **Delete** button to delete. So we have successfully deleted all the Lambda Functions created in this workshop.

![VPC](/images/4.clean/4-3new.png)

{{% notice tip %}}
If you receive the warning **Deleting Lambda@Edge functions and replicas** when deleting a Lambda Function, please see [this document](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide /lambda-edge-delete-replicas.html) and follow to delete successfully.
{{% /notice %}}

So we have successfully completed this workshop. Thank you for your participation.
