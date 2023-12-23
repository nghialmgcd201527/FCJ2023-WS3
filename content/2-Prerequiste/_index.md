---
title: "Solution Deployment"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

This method will create services to serve this workshop. These services will be created using **AWS CloudFormation**. **AWS CloudFormation** is an AWS service that helps us automate the deployment of AWS resources. We will use AWS CloudFormation to create **CloudFront distribution, Amazon S3 Bucket and IAM Role.**

Let's deploy this template as follows:

- [Launch this CloudFormation template](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://s3.us-east-1.amazonaws.com/ee-assets-prod-us-east-1/modules/9d6613dae7674a6d96e1ccd3b3096dda/v1/edge-workshop.yaml&stackName=edge-redirect-workshop). On this page, scroll down to the **Capabilities section,** check the box "**I acknowledge that AWS CloudFormation might create IAM resources with custom names.**" and click the **Create stack** button as shown below

![bo sung](/images/2.prerequisite/2-2new.png)

- It will take a few minutes for this stack to be created successfully. You will see something similar to the image below.

![bo sung](/images/2.prerequisite/2-3.png)

- Next, download the **html file** (which will serve our **Origin**). [Download here](https://static.us-east-1.prod.workshops.aws/public/cabd0ea6-06a9-4598-8680-71c16b92fe28/static/edge-redirect-origin.zip). Store it on your device and extract it. This file will contain the **html file** that we will use for the **Origin content.** Now let's move to the [S3 bucket page](https://s3.console.aws.amazon.com/s3/home?region=us-east-1#). We will see the S3 bucket created by our CloudFormation template.

![bo sung](/images/2.prerequisite/2-4.png)

- Click on that S3 bucket, click on the **Upload button.**

![bo sung](/images/2.prerequisite/2-5new.png)

- Click on the **Add files button,** select the **html file** downloaded above and upload that file to our S3 bucket.

![bo sung](/images/2.prerequisite/2-6new.png)

So we have just finished installing the environment. Now we can start practicing with our workshop.

{{% notice info %}}
We can deploy our resource another way which is **CDK Project.** We will use [CloudShell](https://us-east-1.console.aws.amazon.com/cloudshell/home ?region=us-east-1) in AWS Region **us-east-1**. Download the CDK project file in zip format [at this link](https://s3.amazonaws.com/ee-assets-prod-us-east-1/modules/9d6613dae7674a6d96e1ccd3b3096dda/v1/workshop.zip). And read the support documentation for deploy CDK project [here](https://docs.aws.amazon.com/cdk/v2/guide/home.html).
{{% /notice %}}

Let's review all deployed resources to better understand our workshop. Then let's start practicing.
