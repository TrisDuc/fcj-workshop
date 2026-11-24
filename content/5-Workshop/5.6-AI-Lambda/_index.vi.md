---
title : "Lambda AI với Amazon Bedrock"
date : 2026-10-19
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---
Lambda AI là một **container image Python**. Nó nhận các tin nhắn đã gom từ worker và trả về kết quả rủi ro có cấu trúc. Pipeline gồm: **trích xuất → che dữ liệu cá nhân → truy xuất (RAG) → đánh giá**.

{{% notice note %}}
Mã AI và hợp đồng JSON giữa worker và AI nằm trong repository dự án (`ai/` và `contracts/`). Workshop này chỉ hướng dẫn đóng gói, cấu hình và kết nối; không định nghĩa lại hợp đồng.
{{% /notice %}}

#### Nội dung
- [Mô hình trong S3 và container image trong ECR](5.6.1-s3-ecr/)
- [Amazon Bedrock và Guardrails ở Sydney](5.6.2-bedrock-guardrails/)
- [Tra cứu URL và cache](5.6.3-url-intel-cache/)
