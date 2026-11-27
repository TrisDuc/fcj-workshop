---
title: "Các bài blogs đã đăng"
date: 2026-11-26
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Các bài viết kỹ thuật của nhóm đã đăng trên [AWS Study Group VN](https://www.facebook.com/groups/awsstudygroupfcj). Mỗi blog ghi rõ tác giả.

### [Blog 1 - Lambda + EventBridge: Kiến trúc hướng sự kiện để tự cập nhật dữ liệu hằng ngày]({{< relref "3-blogsposted/3.1-blog1" >}})

**Tác giả:** Võ Đức Trí. **Ngày đăng:** 21/11/2026.

Cách AWS Lambda và Amazon EventBridge chia một hệ thống nhỏ thành các phần chỉ phản ứng với sự kiện của riêng nó, để một trang tự cập nhật mỗi ngày mà không cần server chạy 24/7.

### [Blog 2 - Test luồng Serverless & Event-driven (SQS + DLQ + DynamoDB) trên local với LocalStack và Java 21]({{< relref "3-blogsposted/3.2-blog2" >}})

**Tác giả:** Ngô Thị Kim Ngân. **Ngày đăng:** 25/11/2026.

Cách test local-first cho luồng bất đồng bộ, gồm cả nhánh lỗi vào dead-letter queue, trước khi deploy bằng AWS SAM.

### [Blog 3 - Luồng dữ liệu IoT tối ưu chi phí: MQTT → AWS IoT Core → Glue → S3]({{< relref "3-blogsposted/3.3-blog3" >}})

**Tác giả:** Ngô Thị Kim Ngân. **Ngày đăng:** 26/11/2026.

Cách tránh bài toán "small files" bằng việc chuyển dữ liệu cảm biến JSON sang Parquet với một Glue job chạy theo lịch.
