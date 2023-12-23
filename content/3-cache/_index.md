---
title: "Use case of Cacheable"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

#### Handle Redirects by Lambda@Edge

In this section, we will cover cases that can be cached. Ideally, these cases would be handled using **Lambda@Edge.**

As mentioned in the [Edge Compute Introduction](/vi/1-introduce/1.2-edge/) section, there are many triggers available for **Lambda@Edge,** cases that will be resolved using **Origin facing event triggers.**

![bo sung](/images/3.cache/3-1new.png)

Lambda@Edge Functions are using **Origin facing triggers** which will be triggered after the request is evaluated in **CloudFront Caching Layers,** so if there is a cached response then a response will be returned to the viewer. , saves function invocations. This not only speeds up the response but also helps reduce the cost of our redirects as the Lambda will only be triggered if no response is cached.

### Content

- [Declaring Table in DynamoDB](3.1-dynamodb/)
- [Create Lambda function](3.2-lambdafunction/)
