---
title: "Use Cases"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1.1 </b> "
---

In this section, we'll learn the basics about the Edge Compute features we'll explore in this workshop. It will provide us with background knowledge and content related to labs.

Before introducing [AWS Edge computes](/en/1-introduce/1.2-edge/), we need to have some knowledge about the different techniques used in this workshop to redirect pages from one page to another. different from user requirements. It's called **Redirects** and **Rewrites**.

- **Redirects:** URL redirections take the browser to a new resource or another website. Common HTTP response codes are HTTP 301, HTTP 302 and HTTP 308. After the browser receives the response from the server, it will fetch content from the URL returned by the server. For more information, see [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Redirections). There are some use cases where customers want Redirects including situations where they need to route users to a new domain or even when redirecting users to the main website, redirecting to send users to a page based on device or geographical location. theirs and other cases that we will learn about in this workshop.

- **Rewrites:** URL rewrites are also a technique used to store a different content than what the user requested, however it will not handle a full redirect. Instead, it will just serve another content from the backend of the website or application without the user knowing what is going on. For example, we can think of the browser sending a GET request to fetch content for "**_/index.html_**", but we can modify the URL according to this request and make the backend return content from "**_/index_rewrite.html_**". This will essentially provide different content than the original request without the server sending the user to a completely different URL/page. Some use cases of URL rewrite are cases where it is necessary to rearrange characters from the URL, in this case, it will retrieve the content and not change the URL which is very important so that the user experience can be simple. simpler and other cases that we will learn about in this workshop.
