---
title: "Sử dụng Edge Functions để xử lí Rewrites and Redirects"
date: "`r Sys.Date()`"
weight: 1
chapter: false
---

# Sử dụng Edge Functions để xử lí Rewrites và Redirects

### Tổng quan

Trong bài workshop này, chúng ta sẽ tìm hiểu cách xử lí các trường hợp **redirect** và **rewrite** phổ biến bằng cách sử dụng các tính năng của **Edge Compute** được cung cấp bởi AWS.

Mục tiêu chính của việc này là cung cấp cho người xem kiến thức về cách sử dụng các tính năng của Edge Compute để áp dụng được redirects cho ứng dụng của họ và hiểu cách sử dụng các tính năng đó hiệu quả nhất.

Bài workshop này là nơi bạn sẽ thực hiện các bài labs sử dụng **Compute platforms** có sẵn trong CloudFront để tiếp cận được những trường hợp redirects và rewrites khác nhau. Ở đây chúng ta sẽ sử dụng **Lambda@Edge** vì những responses từ các trường hợp này là cacheable responses.

### Prerequisites

Để hoàn thành bài workshop này, chúng ta cần có kiến thức về CDNs và kiến thức lập trình cơ bản JavaScript/Python để hiểu các đoạn code và những kiến thức xuyên suốt bài workshop.

### Nội dung

1.  [Giới thiệu](1-introduce/)
2.  [Solution Deployment](2-prerequiste/)
3.  [Trường hợp cho Cacheable](3-cache/)
4.  [Dọn dẹp tài nguyên](4-terminate/)
