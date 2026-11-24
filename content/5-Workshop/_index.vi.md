---
title : "Workshop"
date : 2026-10-19
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
{{% notice info %}}
**Lưu ý bản nháp:** Workshop này được viết trước khi dựng, dựa trên kiến trúc mục tiêu. Tên tài nguyên, phiên bản và nhãn trên console có thể hơi khác trong account của bạn — hãy điều chỉnh khi làm, và thay các bước dự kiến bằng những gì bạn thực sự đã chạy và quan sát.
{{% /notice %}}

# Xây dựng AskFirst: trợ lý Telegram serverless chống lừa đảo trên AWS

#### Tổng quan

**AskFirst** ("Hỏi Trước Khi Chuyển") cho phép người cao tuổi chuyển tiếp tin nhắn, ảnh chụp màn hình hoặc đường link đáng ngờ cho một bot Telegram và nhận kết luận rủi ro bằng ngôn ngữ dễ hiểu trước khi gửi tiền.

Trong workshop này bạn sẽ dựng hệ thống từng bước:

+ **Webhook Lambda (Java 21)** xác thực Telegram, loại update trùng, chặn media quá lớn và đưa việc vào hàng đợi.
+ **Hàng đợi Amazon SQS kèm dead-letter queue**, thử lại job lỗi tối đa 5 lần.
+ **Worker Lambda (Java 21)** gom tin nhắn của một người dùng trong cửa sổ 3 giây, gọi Lambda AI, trả lời, cảnh báo người thân và đặt lịch hỏi lại sau 15 phút.
+ **Lambda AI (container Python)** chạy OCR, che dữ liệu cá nhân, truy xuất tri thức lừa đảo và đánh giá rủi ro bằng **Amazon Bedrock** và **Bedrock Guardrails**.
+ **Bảo mật và vận hành**: IAM đặc quyền tối thiểu, SSM Parameter Store, KMS, triển khai GitHub Actions không dùng khóa, alarm CloudWatch, email SNS, CloudTrail và Budgets.

#### Nội dung

1. [Tổng quan workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequiste/)
3. [Nền tảng: DynamoDB và SQS](5.3-Foundation/)
4. [Webhook Lambda cho Telegram](5.4-Webhook/)
5. [Worker Lambda](5.5-Worker/)
6. [Lambda AI với Amazon Bedrock](5.6-AI-Lambda/)
7. [Bảo mật và vận hành](5.7-Security-Ops/)
8. [Kiểm thử end-to-end](5.8-Testing/)
9. [Dọn dẹp](5.9-Cleanup/)
