---
title: "Worklog Tuần 12"
date: 2026-11-30
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

{{% notice info %}}
Tuần này là kế hoạch công việc cho dự án AskFirst. Hãy cập nhật ngày và kết quả cho đúng với những gì thực tế đã dựng và kiểm thử.
{{% /notice %}}

Trong Tuần 12 (30/11/2026–04/12/2026), tôi củng cố hệ thống bằng IAM, bí mật, CI/CD không dùng khóa, giám sát và ngân sách, chạy kiểm thử end-to-end, dọn dẹp tài nguyên và hoàn thiện báo cáo.

### Mục tiêu tuần 12

- Củng cố hệ thống: IAM đặc quyền tối thiểu, bí mật, mã hóa, CI/CD không dùng khóa dài hạn, giám sát, cảnh báo và ngân sách.
- Chạy kiểm thử end-to-end, dọn dẹp tài nguyên và hoàn thiện proposal, workshop, báo cáo.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Role IAM đặc quyền tối thiểu cho từng function; bot token trong SSM Parameter Store; mã hóa bằng KMS. | 30/11/2026 | 30/11/2026 | [AWS IAM](https://000002.awsstudygroup.com/)<br>[AWS Secrets Manager](https://000096.awsstudygroup.com/) |
| 3 | Triển khai không dùng khóa: GitHub Actions dùng OIDC để assume IAM role rồi chạy `sam deploy`. | 01/12/2026 | 01/12/2026 | [AWS CloudFormation](https://000037.awsstudygroup.com/)<br>[CodePipeline CI/CD](https://000017.awsstudygroup.com/) |
| 4 | Quan sát và chi phí: log và metric CloudWatch, alarm gửi email qua SNS, CloudTrail và AWS Budgets. | 02/12/2026 | 02/12/2026 | [Amazon CloudWatch](https://000008.awsstudygroup.com/)<br>[AWS Budgets](https://000007.awsstudygroup.com/) |
| 5 | Kiểm thử end-to-end: văn bản lừa đảo, ảnh chụp màn hình, đường link, media quá lớn, update trùng và phát lại DLQ. | 03/12/2026 | 03/12/2026 | Test plan / Kế hoạch kiểm thử |
| 6 | Dọn dẹp tài nguyên phát sinh chi phí; hoàn thiện proposal, hướng dẫn workshop, blog, sự kiện và tự đánh giá. | 04/12/2026 | 04/12/2026 | — |

### Kết quả đạt được

- Cấp cho mỗi function một role riêng, giới hạn đúng bảng, hàng đợi, bucket và API cần dùng.
- Chuyển bot token sang tham số SSM được mã hóa và chỉ cấp quyền đọc cho các function gửi tin.
- Xem xét tài nguyên nào cần khóa KMS do khách hàng quản lý và tài nguyên nào dùng mã hóa do AWS quản lý để cân bằng bảo mật và chi phí.
- Đăng ký GitHub làm OIDC identity provider và tạo role triển khai giới hạn cho repository của dự án.
- Cập nhật workflow để triển khai mà không lưu access key AWS dài hạn.
- Xác nhận pull request chỉ build và test, còn triển khai chạy từ nhánh main.
- Đặt alarm cho độ sâu DLQ, lỗi function và thời gian chạy Lambda AI, thông báo cho nhóm qua email bằng SNS.
- Bật CloudTrail để kiểm toán và đặt ngân sách hằng tháng với các ngưỡng cảnh báo.
- Đặt thời gian lưu log để không trả tiền cho log không còn hữu ích.
- Chạy từng kịch bản qua Telegram và so sánh kết quả với mức rủi ro kỳ vọng.
- Đo độ trễ end-to-end và chi phí mỗi lần kiểm tra, ghi nhận ảnh hưởng của cold start Lambda AI.
- Ghi lại lỗi và cách sửa, xác nhận job bị từ chối hoặc lỗi hoạt động đúng đặc tả.
- Xóa stack dev, image ECR, object S3 và log group không còn cần thiết.
- Cập nhật tài liệu để người học khác có thể lặp lại từng bước.
- Viết bản nhìn lại ngắn: điều gì hiệu quả, điều gì tốn kém và cần cải thiện gì tiếp theo.

### Bài học rút ra

- Alarm và ngân sách nên tạo trước lần triển khai đầu tiên, không phải sau.
- Đo độ trễ và chi phí mỗi lần kiểm tra cho thấy chỗ nào đáng tối ưu.

### Bước tiếp theo

Rà soát toàn bộ báo cáo và chuẩn bị cho buổi trình bày cuối.
