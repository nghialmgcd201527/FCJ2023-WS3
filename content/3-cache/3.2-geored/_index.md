---
title: "Geo Location Redirects"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

This case is often used to redirect the viewer to the country page of our website. In the diagram below, we can see the structure built for this part.

![bổ sung](/images/3.cache/3.2-geored/3.2-chart1.png)
![bổ sung](/images/3.cache/3.2-geored/3.2-chart2.png)

#### Step 1: Create CloudFront Cache policy to forward country header:

To be able to Redirect users based on **country location**. We need to make sure that the **Contry Header** binds to the Lambda@Edge function. To do this, we need to create a **Cache Policy** where headers are added to the cache key.

1. Go to the [CloudFront Cache Policies console](https://us-east-1.console.aws.amazon.com/cloudfront/v3/home?region=us-east-1#/policies/cache).

2. In the **Custom Policies section,** click on the **Create cache policy** button.

![bổ sung](/images/3.cache/3.2-geored/3.2-1new.png)

3. On the **Create cache policy** page, name it **edge-redirect-cache-policy**. In the **Cache key settings** section, expand the **Headers** section and select **Include the following headers**. In the **Add header** section, we search for **CloudFront-Viewer-Country** and select it. Leave the other items as default and click the **Create** button.

![bổ sung](/images/3.cache/3.2-geored/3.2-2new.png)

#### Step 2: Create Lambda@Edge function and publish new version

In this step, we will do as in the previous part, create a **Lambda function** with the name `edge-geo-redirect`, **Runtime** is **Python 3.9** and deploy it. After that, we need to publish the new version of the Lambda function we just created.

Code source của Lambda function này là:

```
import json
def lambda_handler(event, context):
    #Let's first get the Country Code from the Request coming in
    get_country_viewer_header = event['Records'][0]['cf']['request']['headers']['cloudfront-viewer-country']
    define_country = get_country_viewer_header[0]['value']
    #Now let's define which country that is and create our redirected response
    if (define_country == 'US'):
        response = {
            'status': '301',
            'statusDescription': 'Permanent Redirect',
            'headers': {
                'location': [{
                    'key': 'Location',
                    'value': '/en-us.html'
                }]
            }
        }
        #The response above has been created and a response will be sent to the viewer to redirect it to the /en-us.html
        return response
    else:
        #if the country has not been identified then move on with the request
        request = event['Records'][0]['cf']['request']
        return request
```

#### Step 3: Create CloudFront behavior for this case

Now the Lambda function has just been created and we have to assign it to a Cache Behavior in CloudFront.

1. Go to [CloudFront console](https://us-east-1.console.aws.amazon.com/cloudfront/v3/home). You will see the Distribution created by the CloudFormation template, it will be determined by the **Edge Redirect Workshop Distribution** as in the **Description** section.

2. Select that Distribution then select **Behaviors.**

3. Click on the **Create Behavior button.**

4. In the **Path pattern section,** we enter `/geo.html`. Below is the **Origin and origin groups section,** we choose **myS3Origin.** In the **Viewer protocol policy section,** we choose **Redirect HTTP to HTTPS.** In the **Cache key section and origin requests**, we select **Cache policy and origin request policy**, next expand the section **Cache policy**, we select the policy we just created in the previous step **edge-redirect-cache-policy .** We will leave the remaining items as default and click on the **Create behavior** button at the bottom of the page.

![bổ sung](/images/3.cache/3.2-geored/3.2-3new.png)

#### Step 4: Combine Lambda Function with CloudFront Behavior

We do the same as in the **URI based Redirects** case above, we will combine the Lambda function **edge-geo-redirect** with the CloudFront Behavior we just created above.

#### Step 5: Set up clients for testing

We will need a client to run curl commands. The easy way is to create **CloudShell Environments**. CloudShell is a shell available in the AWS console and we can run Linux commands from it. Go to [CloudShell Console](https://us-east-1.console.aws.amazon.com/cloudshell/home?region=us-east-1#) and wait until the terminal is ready to use.

{{% notice info %}}
If CloudShell does not work, if you are doing this workshop on a Linux/MacOS client, these two operating systems already have **curl** and you just need to run the command on that client. If you are using Windows, use online curl tools like [this one](https://reqbin.com/curl). You can run an EC2 instance or Cloud9 IDE from AWS COnsole to run commands.
{{% /notice %}}

#### Step 6: Test redirect configuration

1. Go to [CloudShell Console](https://us-east-1.console.aws.amazon.com/cloudshell/home?region=us-east-1#) in AWS Region **us-east-1 **.

2. In the test, we will run the curl command to send http requests against our distribution, to do so, we need to copy the Distribution domain name from the CloudFront console where we can find it.

![VPC](/images/3.cache/3.1-urired/3.1-13new.png)

Once you've found the distribution domain name, copy the following command and replace it with our domain name.

```
curl -v -o /dev/null https://<YOUR-DISTRIBUTION-DOMAIN-NAME>/geo.html
```

3. After building the above command from cloudshell, we will see the result as below.

```
< HTTP/1.1 301 Moved Permanently
< Content-Length: 0
< Connection: keep-alive
< Server: CloudFront
< Location: /en-us.html
< X-Cache: Miss from cloudfront
< Via: 1.1 6fbeae74487f866b555dc44d03fcc2a6.cloudfront.net (CloudFront)
< X-Amz-Cf-Pop: MIA3-P3
< X-Amz-Cf-Id: VRNhLpCaNiZ08Fw5f2eMtWn8KfHnkPp3qp4kQeft3CVfEjo6kXIEWQ==
```

4. Let's run the above command again in AWS Region **us-east-1**. We will see different responses.

```
< HTTP/1.1 301 Moved Permanently
< Content-Length: 0
< Connection: keep-alive
< Server: CloudFront
< Location: /en-us.html
< X-Cache: Hit from cloudfront
< Via: 1.1 e759cef9ef04dc6632a71818dfac3a76.cloudfront.net (CloudFront)
< X-Amz-Cf-Pop: MIA3-P3
< X-Amz-Cf-Id: aOe3ADve7yDyHH_Iyb7MGMPAX8LWuTli79qf-nFl8rT40-VxPb_Zeg==
< Age: 158
```

{{% notice info %}}
The only difference here is the **X-Cache** response header. We will see **Hit from cloudfront** because in the first response after the request, it has been cached in CloudFront so the second request will not need to go through all the functions instead it will only be fired from cached. content, this provides better performance and also saves costs because the function does not need to be triggered again.
{{% /notice %}}

5. Now, go back to the shell console and run the same command above in AWS Region **eu-west-1**. Now our response will not be **HTTP 301** but **HTTP 200 OK** as shown below.

```
< HTTP/1.1 200 OK
< Content-Type: text/html
< Content-Length: 98
< Connection: keep-alive
< ETag: "be3c901839ae019e0c58908e45f5ab45"
< Accept-Ranges: bytes
< Server: AmazonS3
< X-Cache: Miss from cloudfront
< Via: 1.1 9bbdfc2323989883f386114cc53fdbd0.cloudfront.net (CloudFront)
< X-Amz-Cf-Pop: MIA3-P3
< X-Amz-Cf-Id: 2UlANwUbwF5Mv1V-XKWcYWKrTzIJZh2asxxw_xOK7aahkVP6TS6tRg==
<

<!DOCTYPE html>
<html>
<body>

<h1>Not our US page</h1>
<p>Not our US page</p>

</body>
</html>
```

{{% notice info %}}
This request's HTTP 200 OK response means it was not redirected and instead, the requested resource was provided by CloudFront.
{{% /notice %}}

So we have successfully deployed redirect logic using Lambda@Edge and finished testing. This case can be expanded to be more complex, determining not only **Contry** but also other geographic locations such as **States, Cities, postal codes, time zones**. Refer to [this document](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-cloudfront-headers.html#cloudfront-headers-viewer-location) to learn more about all headers location is possible in CloudFront.
