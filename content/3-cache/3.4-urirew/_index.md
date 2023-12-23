---
title: "URI based Rewrites"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 3.4. </b> "
---

This is a technique that does not involve redirecting the client browser to a completely new URL. However, the browser will continue to recognize the URI it requested, but instead of CloudFront retrieving it from the same URI at Original, it will get the content from a different path. For example, let's say a browser has sent a request to resource **www.example.com/uri-rewrite.html**, but you want to get the content from a different backend path or even a different Origin that the viewer don't know about this. You would then use the Lambda@Edge function or CloudFront function to do the URI rewrite and actually access the origin to request a URI such as **www.example.com/rewrite.html**

For this case, we will use **Default Behavior** from CloudFront and will not use any specific headers to base our function logic on, so you also do not need to create a new cache policy. However, those can very well be used to create more complex scenarios, where instead of redirecting, we can rewrite the URI on the backend. In our previous examples in this workshop, we could detect the location or device type of the viewer and get a different URI from the backend instead of redirecting the user. The following image depicts the structure of what will be built in this workshop:

![bổ sung](/images/3.cache/3.4-urirew/3.4-chart.png)

#### Step 1: Create CloudFront Behavior:

Looking back at [example **URI based Redirects**](/vi/3-cache/3.1-urired), we create a Behavior with **Path Pattern** as `/uri-rewrite.html` and in the section **Origin and origin group**, we choose **myS3Origin**.

![bổ sung](/images/3.cache/3.4-urirew/3.4-1new.png)

#### Step 2: Create Lambda@Edge function and publish new version

In this step, we will do as in the previous part, create a **Lambda function** with the name `edge-uri-rewrite`, **Runtime** is **Python 3.9** and deploy it. After that, we need to publish the new version of the Lambda function we just created.

The source code of this Lambda function is:

```
import json
def lambda_handler(event, context):
    #let's first extract the URI from the request
    get_uri = event['Records'][0]['cf']['request']['uri']
    #let's check what is the URI and decide if the URI sent to the Origin should be modified
    if (get_uri == '/uri-rewrite.html'):
        event['Records'][0]['cf']['request']['uri'] = '/rewrite.html'
        request = event['Records'][0]['cf']['request']
        return request
    #if the uri should not be modified then just continue with the request as is
    else:
        request = event['Records'][0]['cf']['request']
        return request
```

#### Step 3: Combine Lambda Function with CloudFront Behavior

We do as in the **Geo Location Redirects** section above, we will combine the Lambda function **edge-uri-rewrite** with the CloudFront Behavior we just created above.

#### Step 4: Set up clients for testing

We will need a client to run curl commands. The easy way is to create **CloudShell Environments**. CloudShell is a shell available in the AWS console and we can run Linux commands from it. Go to [CloudShell Console](https://us-east-1.console.aws.amazon.com/cloudshell/home?region=us-east-1#) and wait until the terminal is ready to use.

{{% notice info %}}
If CloudShell does not work, if you are doing this workshop on a Linux/MacOS client, these two operating systems already have **curl** and you just need to run the command on that client. If you are using Windows, use online curl tools like [this one](https://reqbin.com/curl). You can run an EC2 instance or Cloud9 IDE from AWS COnsole to run commands.
{{% /notice %}}

#### Step 5: Test redirect configuration

1. Go to [CloudShell Console](https://us-east-1.console.aws.amazon.com/cloudshell/home?region=us-east-1#).

2. In the test, we will run the curl command to send http requests against our distribution, to do so, we need to copy the Distribution domain name from the CloudFront console where we can find it.

![VPC](/images/3.cache/3.1-urired/3.1-13new.png)

Once you've found the distribution domain name, copy the following command and replace it with our domain name.

```
curl -v -o /dev/null https://<YOUR-DISTRIBUTION-DOMAIN-NAME>/uri-rewrite.html
```

3. After building the above command from cloudshell, we will see the result as below.

```

< HTTP/1.1 200 OK
< Content-Type: text/html
< Content-Length: 92
< Connection: keep-alive
< ETag: "a1764624bd34afb8e52157f114ef6db1"
< Accept-Ranges: bytes
< Server: AmazonS3
< X-Cache: Miss from cloudfront
< Via: 1.1 3cf1bfec064e2e01f071e8051a22d830.cloudfront.net (CloudFront)
< X-Amz-Cf-Pop: ATL56-C1
< X-Amz-Cf-Id: HeF7XzxKoVyNxUKAAnP0t8bCzpwbN-pyex3mRMbqcH-mQOT5V3yjAw==
<

<!DOCTYPE html>
<html>
<body>

<h1>Rewrite Page</h1>
<p>Rewrite Page</p>

</body>
</html>
```

{{% notice info %}}
The above request shows that the direct response to our request is HTTP 200 OK, meaning this is not a redirect, instead content has been provided, however in this page you can see the generated HTML saying This is rewrite content instead of main content.
{{% /notice %}}

So we have successfully deployed the URI rewrite case. This scenario can be extended to handle more complex logic as well as even retrieve content from different origins if required. This case can also be done using CloudFront functions if our case requires all requests to be evaluated and rewritten.
