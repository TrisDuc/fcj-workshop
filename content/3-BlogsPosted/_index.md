---
title: "Blogs Posted"
date: 2026-11-21
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

My technical articles published in [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj).

### [Blog 1 - Lambda + EventBridge: Event-driven Architecture for Daily Data Updates]({{< relref "3-blogsposted/3.1-blog1" >}})

**Published:** 21 November 2026

How can one page update itself every day without a server running 24/7? This article uses AWS Lambda and Amazon EventBridge to split a small system into parts that each react only to their own event: a scheduler for each data source, a collector Lambda, an S3 event, and a renderer Lambda behind CloudFront. The article page includes the diagram and publication screenshot.
