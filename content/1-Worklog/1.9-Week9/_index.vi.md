---
title: "Worklog Tuần 9"
date: 2026-11-09
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

{{% notice info %}}
Tuần này là kế hoạch công việc cho dự án AskFirst. Hãy cập nhật ngày và kết quả cho đúng với những gì thực tế đã dựng và kiểm thử.
{{% /notice %}}

Trong Tuần 9 (09/11/2026–13/11/2026), tôi bắt đầu dự án AskFirst: xác định bài toán và phạm vi, viết yêu cầu, thiết kế kiến trúc, chuẩn bị repository và phần nền tảng.

### Mục tiêu tuần 9

- Xác định bài toán, người dùng và phạm vi của AskFirst: trợ lý Telegram giúp người cao tuổi kiểm tra tin nhắn đáng ngờ trước khi chuyển tiền.
- Hoàn thiện đặc tả yêu cầu (SRS), kiến trúc mục tiêu và chuẩn bị repository, CI, môi trường phát triển.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Tổng hợp nghiên cứu về lừa đảo trực tuyến tại Việt Nam; xác định persona (người cao tuổi, người thân) và phạm vi chỉ dùng Telegram. | 09/11/2026 | 09/11/2026 | Project docs / Tài liệu dự án |
| 3 | Viết SRS: FRS, NFRS và Business Rules, bắt đầu bằng nhóm tính năng thiết lập ban đầu/đồng ý xử lý dữ liệu. | 10/11/2026 | 10/11/2026 | ScamProtection_System_Requirements.xlsx |
| 4 | Thiết kế kiến trúc mục tiêu, chọn Region và vẽ luồng xử lý end-to-end. | 11/11/2026 | 11/11/2026 | [AWS Well-Architected](https://aws.amazon.com/architecture/well-architected/) |
| 5 | Dựng repository, cấu trúc Maven multi-module, CI bằng GitHub Actions và stack SAM cho môi trường dev. | 12/11/2026 | 12/11/2026 | [AWS SAM](https://000136.awsstudygroup.com/) |
| 6 | Thiết kế mô hình DynamoDB single-table và tạo hàng đợi SQS kèm dead-letter queue. | 13/11/2026 | 13/11/2026 | [Amazon DynamoDB](https://000060.awsstudygroup.com/)<br>[SQS and SNS](https://000077.awsstudygroup.com/) |

### Kết quả đạt được

- Tóm tắt các dạng lừa đảo phổ biến (mạo danh, đầu tư giả, đường link độc hại) mà bot cần nhận diện.
- Chọn Telegram làm kênh duy nhất để mọi yêu cầu đều khả thi với năng lực thực tế của một Telegram bot.
- Liệt kê nền tảng pháp lý cần tuân thủ (Luật 91/2025/QH15 và Nghị định 356/2025/NĐ-CP) và yêu cầu giao diện thân thiện với người cao tuổi.
- Chia yêu cầu chức năng thành các nhóm: thiết lập ban đầu và đồng ý, phân tích tin nhắn/media, đánh giá rủi ro, cảnh báo người thân, hướng dẫn qua bot và báo cáo.
- Tách yêu cầu phi chức năng (độ trễ, sẵn sàng, quyền riêng tư, chi phí) khỏi quy tắc nghiệp vụ.
- Xuất ba sheet (FRS, NFRS, Business Rules) vào một file Excel để rà soát.
- Vẽ luồng serverless: Telegram → webhook Lambda → SQS → worker Lambda → AI Lambda → Telegram, dùng DynamoDB làm kho dữ liệu duy nhất.
- Đặt phần lõi ở ap-southeast-1 (Singapore) để giảm độ trễ, còn mô hình Amazon Bedrock và guardrails ở ap-southeast-2 (Sydney).
- Quyết định ảnh người dùng gửi chỉ nằm trong bộ nhớ RAM của Lambda AI và không được ghi ra lưu trữ.
- Tạo các module `common`, `webhook-handler` và `worker` (package `com.askfirst`) nhắm tới Java 21.
- Thêm workflow GitHub Actions build và test với Java 21 cho mỗi lần push.
- Soạn `template.yaml` cho stack `askfirst-dev` ở ap-southeast-1 và môi trường Docker + LocalStack để chạy local.
- Liệt kê access pattern trước (buffer tin nhắn theo người dùng, phiên, kết quả kiểm tra, cờ, cảnh báo người thân) rồi suy ra partition/sort key.
- Cấu hình hàng đợi job để một message được thử lại tối đa 5 lần trước khi chuyển sang DLQ.
- Chốt phương án lưu bot token của Telegram trong SSM Parameter Store thay vì trong mã nguồn hoặc file môi trường.

### Bài học rút ra

- Viết access pattern trước khi thiết kế bảng giúp tránh làm lại trong DynamoDB.
- Giới hạn phạm vi ở một kênh (Telegram) giúp mọi yêu cầu đều khả thi.

### Bước tiếp theo

Hiện thực webhook và worker Lambda ở Tuần 10.
