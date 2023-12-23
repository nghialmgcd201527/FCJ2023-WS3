---
title: "URI based Redirects"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

In this section, we will start with a simple case that can be easily understood using **Lambda@Edge** as this case can be cached, so it is best handled with the **Lambda@Edge.**

This case is often used to redirect users for some reason when requesting a URI that the admin does not want that user to see or the URI may no longer be available. For example, a user can send requests to **/uri-main.html**, however, the admin wants all those requests to be made by **/uri-redirect.html**

#### Step 1: Create Cache Behavior for this case

To make this happen, you will first create a specific cache behavior. The following steps will guide you to do this.

1. Go to [CloudFront console](https://us-east-1.console.aws.amazon.com/cloudfront/v3/home). You will see the Distribution created by the CloudFormation template, it will be determined by the **Edge Redirect Workshop Distribution** as in the **Description** section.

![VPC](/images/3.cache/3.1-urired/3.1-1new.png)

2. Select that Distribution then select **Behaviors.**

![VPC](/images/3.cache/3.1-urired/3.1-2.png)

3. Click on the **Create Behavior button.**

![VPC](/images/3.cache/3.1-urired/3.1-3new.png)

4. In the **Path pattern section,** we enter `/uri-main.html`, below is the **Origin and origin groups section,** we select **myS3Origin.** In the **Viewer protocol policy section ,** we choose **Redirect HTTP to HTTPS.** We will leave the remaining items as default and click on the **Create behavior** button at the bottom of the page.

![VPC](/images/3.cache/3.1-urired/3.1-4new.png)

#### Step 2: Create Lambda@Edge function and publish new version

This step will be the process of creating our function along with its version. Lambda@Edge functions need to be referred to by CloudFront Distribution based on their ARN version, not their main function ARN.

1. Go into [Lambda Console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/create/function) in AWS Region **us-east-1**, click on the **Create function.** button

![VPC](/images/3.cache/3.1-urired/3.1-5new.png)

2. On the **Create function page,** name our function `edge-uri-redirect`, select **Python 3.9** for the **Runtime section.** At the bottom, we open the section **Change default execution role** then select **Use an existing role,** we select **edge-redirect-lambda-role** (this is a role created from CloudFormation template). Finally, click on the **Create function.** button

![VPC](/images/3.cache/3.1-urired/3.1-6new.png)

3. Once the function is created, we are on the main page of that function. In the **Code** section below, we copy the code below and paste it into the **Code source section.**

```
import json
def lambda_handler(event, context):
    get_uri = event['Records'][0]['cf']['request']['uri']
    print(get_uri)

    if (get_uri == '/uri-main.html'):
        response = {
            'status': '301',
            'statusDescription': 'Permanent Redirect',
            'headers': {
                'location': [{
                    'key': 'Location',
                    'value': '/uri-redirect.html'
                }]
            }
        }
        return response
    else:
        request = event['Records'][0]['cf']['request']
        return request
```

In this code, it is used as an AWS CloudFront function. It serves the CloudFront event handler and implements logic for URL redirection based on the requested URI. Function **lambda_handler** is the entry point of Lambda function, it includes 2 parameters **event** and **context**. This function extracts the URI requested from the CloudFront event. If the request URI is **/uri-main.html**, the code will return a response with **status code 301** and **location header** set to **/uri-redirect.html**. If the URI is not **/uri-main.html**, the code will return the request object.

![VPC](/images/3.cache/3.1-urired/3.1-7new.png)

4. Next, click on the **Deploy** button to commit the code of our lambda function. When our code is successfully deployed, we will publish a new version of this lambda. Click on the **Actions** button in the right corner, we choose **Publish new version.**

![VPC](/images/3.cache/3.1-urired/3.1-8new.png)

We enter `edge-uri-redirect-v1` for the **Version description** and click on the **Publish button.**

![VPC](/images/3.cache/3.1-urired/3.1-9new.png)

#### Step 3: Combine Lambda Function with CloudFront Behavior

1. Return to the lambda function console and open function **edge-uri-redirect.**

2. Click **+ Add trigger**

![VPC](/images/3.cache/3.1-urired/3.1-10new.png)

3. On the **Trigger configuration** page, we select **CloudFront** for the **source** section. Then click **Deploy on Lambda@Edge**.

![VPC](/images/3.cache/3.1-urired/3.1-11new.png)

4. A new window opens, in the **Distribution** section, we select the distribution created from the CloudFormation template. For **Cache behavior,** we choose **/uri-main.html**. In the **CloudFront event** section, select **Origin request**. Check the **Confirm deploy to Lambda@Edge** box and click on the **Deploy button.**

![VPC](/images/3.cache/3.1-urired/3.1-12new.png)

#### Step 4: Set up client for testing

To test specific redirects, we will need a client to run curl commands. The easy way is to create **CloudShell Environments**. CloudShell is a shell available in the AWS console and we can run Linux commands from it. Go to [CloudShell Console](https://us-east-1.console.aws.amazon.com/cloudshell/home?region=us-east-1#) and wait until the terminal is ready to use.

{{% notice info %}}
If CloudShell does not work, if you are doing this workshop on a Linux/MacOS client, these two operating systems already have **curl** and you just need to run the command on that client. If you are using Windows, use online curl tools like [this one](https://reqbin.com/curl). You can run an EC2 instance or Cloud9 IDE from AWS COnsole to run commands.
{{% /notice %}}

#### Step 5: Test redirect configuration

1. Forward to [CloudShell Console](https://us-east-1.console.aws.amazon.com/cloudshell/home?region=us-east-1#).

2. In the test, we will run the curl command to send http requests to our distrubtion, to do so, we need to copy the Distribution domain name from the CloudFront console where we can find it.

![VPC](/images/3.cache/3.1-urired/3.1-13new.png)

Once you've found the distribution domain name, copy the following command and replace it with our domain name.

```
curl -v -o /dev/null https://<YOUR-DISTRIBUTION-DOMAIN-NAME>/uri-main.html
```

3. After building the above command from cloudshell, we will see the result as below.

![VPC](/images/3.cache/3.1-urired/3.1-14.png)

{{% notice info %}}
We can see in the above result, this request receives an HTTP 301 response, indicating redirect, and the Location header is the URI where the client is redirected to.
{{% /notice %}}

4. Now, let's run the same command again. And see the difference.

![VPC](/images/3.cache/3.1-urired/3.1-15.png)

{{% notice info %}}
The difference when running this command is the value of the X-Cache header. The request now receives a redirected and cached response from the previous command run. This means that our Lambda@edge does not need to be triggered again, saving time and costs.
{{% /notice %}}

We have successfully deployed the first redirect using Lambda@Edge and have finished testing. Now we move to the next case.
