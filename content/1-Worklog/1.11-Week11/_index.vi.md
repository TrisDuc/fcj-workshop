---
title: "Worklog Tuần 11"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

{{% notice info %}}
Tuần này là kế hoạch công việc cho dự án AskFirst. Hãy cập nhật ngày và kết quả cho đúng với những gì thực tế đã dựng và kiểm thử.
{{% /notice %}}

Trong Tuần 11 (23/11/2026–27/11/2026), tôi xây dựng và kết nối Lambda AI bằng Python: mô hình, pipeline, Amazon Bedrock với Guardrails, kiểm tra URL, cache, cảnh báo người thân và hỏi lại.

### Mục tiêu tuần 11

- Xây dựng và đóng gói Lambda AI (Python): OCR, embedding, che PII, truy xuất tri thức và đánh giá rủi ro bằng Amazon Bedrock.
- Kết nối toàn bộ luồng, gồm kiểm tra uy tín URL, cache, cảnh báo người thân và hỏi lại sau 15 phút.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Đóng gói Lambda AI thành container image trong ECR; lưu trọng số mô hình và chỉ mục knowledge base trong S3. | 23/11/2026 | 23/11/2026 | [Docker](https://000015.awsstudygroup.com/)<br>[Amazon S3](https://000057.awsstudygroup.com/) |
| 3 | Hiện thực các bước pipeline: trích xuất văn bản, che dữ liệu cá nhân, truy xuất tri thức lừa đảo tương tự rồi đánh giá. | 24/11/2026 | 24/11/2026 | Project docs / Tài liệu dự án |
| 4 | Gọi Amazon Bedrock (Qwen3 32B) và Bedrock Guardrails ở Sydney; thiết kế prompt và kết quả có cấu trúc. | 25/11/2026 | 25/11/2026 | [Amazon Bedrock docs](https://docs.aws.amazon.com/bedrock/) |
| 5 | Thêm kiểm tra uy tín URL (RDAP, Safe Browsing) và cache DynamoDB cho các lần kiểm tra lặp lại. | 26/11/2026 | 26/11/2026 | Public RDAP / Safe Browsing docs |
| 6 | Trả kết quả về Telegram, cảnh báo người thân và đặt lịch hỏi lại sau 15 phút. | 27/11/2026 | 27/11/2026 | [SQS and SNS](https://000077.awsstudygroup.com/) |

### Kết quả đạt được

- Build container image và đẩy lên Amazon ECR.
- Tải mô hình OCR (PP-OCRv5, ONNX) và mô hình embedding đa ngữ (e5-small, int8) lên S3 và nạp vào bộ nhớ khi cold start.
- Giữ ảnh người dùng chỉ trong bộ nhớ, đúng như thiết kế.
- Trích xuất văn bản từ tin nhắn và từ ảnh bằng OCR.
- Che các định danh cá nhân trước khi bất kỳ văn bản nào rời khỏi Lambda AI.
- Truy xuất các mục tương tự nhất từ chỉ mục knowledge base để làm căn cứ cho bước đánh giá.
- Áp dụng guardrail cho đầu vào và đầu ra quanh lời gọi mô hình.
- Yêu cầu mô hình trả kết luận có cấu trúc (mức rủi ro, lý do, lời khuyên bằng tiếng Việt đơn giản) để worker định dạng ổn định.
- Xử lý độ trễ liên Region và throttling bằng timeout và retry.
- Tra dữ liệu đăng ký tên miền (RDAP) và trạng thái an toàn duyệt web cho các đường link trong tin nhắn.
- Cache kết quả kèm thời gian sống để link lặp lại được trả lời nhanh và rẻ.
- Coi tra cứu bên ngoài là tín hiệu tùy chọn: nếu một nguồn lỗi, pipeline vẫn tiếp tục với bằng chứng còn lại.
- Gửi kết luận cho người cao tuổi bằng ngôn ngữ dễ hiểu và lưu kết quả vào DynamoDB.
- Chỉ gửi cảnh báo cho người thân đã liên kết khi kết quả rủi ro cao và đã có sự đồng ý được ghi nhận.
- Đưa lại một job hỏi lại vào hàng đợi với độ trễ 15 phút để hỏi người dùng đã chuyển tiền hay chưa.

### Bài học rút ra

- Tra cứu bên ngoài nên là bằng chứng tùy chọn để pipeline suy giảm êm.
- Che dữ liệu cá nhân trước mọi lời gọi mô hình là quy tắc thiết kế, không phải việc làm sau.

### Bước tiếp theo

Củng cố, giám sát, kiểm thử end-to-end và dọn dẹp ở Tuần 12.
