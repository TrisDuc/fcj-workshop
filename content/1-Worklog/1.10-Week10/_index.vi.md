---
title: "Worklog Tuần 10"
date: 2026-11-16
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

{{% notice info %}}
Tuần này là kế hoạch công việc cho dự án AskFirst. Hãy cập nhật ngày và kết quả cho đúng với những gì thực tế đã dựng và kiểm thử.
{{% /notice %}}

Trong Tuần 10 (16/11/2026–20/11/2026), tôi hiện thực webhook và worker Lambda bằng Java 21: xác thực, chống trùng, giới hạn media, gom tin, xử lý job và retry.

### Mục tiêu tuần 10

- Hiện thực webhook Lambda Java 21: xác thực Telegram, chống trùng update, chặn media quá lớn và đưa job vào hàng đợi.
- Hiện thực worker Lambda Java 21: gom tin theo người dùng, gọi Lambda AI và xử lý retry.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Webhook handler: kiểm tra secret thời gian hằng, chống trùng theo `update_id`, đường nhanh và đường chậm. | 16/11/2026 | 16/11/2026 | [Telegram Bot API](https://core.telegram.org/bots/api) |
| 3 | Chặn media: từ chối ảnh/file trên 20 MB và voice trên 120 giây trước khi tạo job. | 17/11/2026 | 17/11/2026 | Project issue tracker / Issue của dự án |
| 4 | Gom tin nhắn: item BUFFER# META và các item MSG# theo thứ tự seq, cửa sổ gom 3 giây cho mỗi người dùng. | 18/11/2026 | 18/11/2026 | [Amazon DynamoDB](https://000060.awsstudygroup.com/) |
| 5 | Worker Lambda: poll SQS, các loại job (phân tích, trả lời, hỏi lại), các repository và hợp đồng với AI client. | 19/11/2026 | 19/11/2026 | [SQS and SNS](https://000077.awsstudygroup.com/) |
| 6 | Retry, xử lý dead-letter, idempotency, kiểm thử và lần triển khai đầu tiên lên stack dev. | 20/11/2026 | 20/11/2026 | [AWS SAM](https://000136.awsstudygroup.com/) |

### Kết quả đạt được

- So sánh header secret-token bằng phép so sánh thời gian hằng để chênh lệch thời gian không để lộ thông tin.
- Dùng conditional write trong DynamoDB theo `update_id` để một lần gửi lặp được xác nhận nhưng không xử lý hai lần.
- Tách xử lý: lệnh đơn giản trả lời ngay (đường nhanh); phân tích tin nhắn đưa vào hàng đợi (đường chậm) để webhook phản hồi nhanh.
- Đọc kích thước và thời lượng từ metadata của update Telegram và từ chối mục quá lớn kèm thông báo rõ ràng cho người dùng.
- Đảm bảo mục bị từ chối không bao giờ vào SQS nên không phát sinh chi phí worker hay AI.
- Thêm unit test cho giá trị biên (đúng bằng và vừa vượt từng giới hạn).
- Lưu mỗi tin nhắn đến vào buffer của người dùng với số thứ tự tăng dần.
- Thiết kế worker chờ cửa sổ đóng rồi đọc toàn bộ tin nhắn theo thứ tự và gửi thành một job phân tích.
- Ghi lại cách tin đến muộn mở cửa sổ mới thay vì bị mất.
- Hiện thực SQS event handler và điều phối theo loại job.
- Xây dựng `AiClient` gọi Lambda AI (Python) theo hợp đồng JSON đã thống nhất, không sửa hợp đồng hay mã AI.
- Thêm các repository cho buffer, flags, checks, sessions và alerts trên nền single table.
- Kiểm chứng một message lỗi được thử lại tối đa 5 lần rồi vào DLQ.
- Làm cho việc xử lý job có tính idempotent để retry không gửi trùng phản hồi cho người dùng.
- Triển khai webhook và worker lên stack `askfirst-dev` bằng `sam build` và `sam deploy`.

### Bài học rút ra

- Việc giao message là at-least-once nên mọi handler phải idempotent.
- Từ chối đầu vào xấu ngay từ biên giúp tiết kiệm chi phí phía sau.

### Bước tiếp theo

Xây dựng và kết nối Lambda AI ở Tuần 11.
