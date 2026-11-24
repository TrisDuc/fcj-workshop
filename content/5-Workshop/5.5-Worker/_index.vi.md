---
title : "Worker Lambda"
date : 2026-10-19
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---
Worker tiêu thụ job từ SQS. Nó biến một loạt tin nhắn ngắn thành **một** yêu cầu phân tích, gọi Lambda AI, gửi phản hồi và xử lý việc hỏi lại.

#### Nội dung
- [Gom tin nhắn với cửa sổ 3 giây](5.5.1-buffering/)
- [SQS trigger, retry và idempotency](5.5.2-sqs-trigger/)
- [Kết quả, cảnh báo người thân và hỏi lại sau 15 phút](5.5.3-results-followup/)
