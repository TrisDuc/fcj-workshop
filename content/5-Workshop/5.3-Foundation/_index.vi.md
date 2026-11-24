---
title : "Nền tảng: DynamoDB và SQS"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---
Trong phần này bạn tạo hai tài nguyên mà mọi bước sau đều phụ thuộc: **bảng DynamoDB single-table** và **hàng đợi job cùng dead-letter queue**. Bạn sẽ định nghĩa chúng trong `template.yaml` để có thể dựng lại hoặc xóa bằng một lệnh.

#### Nội dung
- [DynamoDB single-table](5.3.1-dynamodb/)
- [Hàng đợi SQS và dead-letter queue](5.3.2-sqs-dlq/)
