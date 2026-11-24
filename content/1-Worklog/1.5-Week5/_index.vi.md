---
title: "Worklog Tuần 5"
date: 2026-10-12
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

Trong Tuần 5 (12/10/2026–16/10/2026), tôi củng cố quản trị, kiểm soát chi phí và tìm hiểu các thành phần bảo mật như truy cập S3 riêng tư, WAF, Secrets Manager và Cognito.

### Mục tiêu tuần 5

- Củng cố quản trị và kiểm soát chi phí: ủy quyền billing, service quotas, báo cáo chi phí và mức sử dụng.
- Tìm hiểu các thành phần bảo mật: truy cập S3 riêng tư, WAF, Secrets Manager, Cognito, thực hành bảo mật S3.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Ôn tập phân quyền billing, service quota và báo cáo chi phí sử dụng. | 12/10/2026 | 12/10/2026 | [Billing Console Delegation](https://000075.awsstudygroup.com/)<br>[Service Quotas](https://000063.awsstudygroup.com/)<br>[Cost and Usage Management](https://000064.awsstudygroup.com/) |
| 3 | Tìm hiểu tự động hóa vòng đời EBS, phát hiện bất thường backup và công cụ phát triển. | 13/10/2026 | 13/10/2026 | [EBS Data Lifecycle Manager](https://000088.awsstudygroup.com/)<br>[EBS Backup Anomaly Detection](https://000089.awsstudygroup.com/)<br>[AWS Toolkit for VS Code](https://000087.awsstudygroup.com/) |
| 4 | Tìm hiểu truy cập S3 riêng tư, bảo vệ web và quản lý secrets. | 14/10/2026 | 14/10/2026 | [S3 VPC Endpoints](https://000111.awsstudygroup.com/)<br>[AWS WAF](https://000026.awsstudygroup.com/)<br>[AWS Secrets Manager](https://000096.awsstudygroup.com/) |
| 5 | Tìm hiểu xác thực người dùng bằng Cognito và thực hành bảo mật S3. | 15/10/2026 | 15/10/2026 | [Amazon Cognito](https://000141.awsstudygroup.com/)<br>[S3 Security Best Practices](https://000069.awsstudygroup.com/) |

### Kết quả đạt được

- Hiểu vì sao quyền xem/quản lý billing cần được phân quyền rõ ràng, tách khỏi quyền ứng dụng thông thường.
- Ôn tập Service Quotas là giới hạn theo account/Region ảnh hưởng đến lượng tài nguyên có thể tạo và nơi gửi yêu cầu tăng quota.
- Tìm hiểu công cụ chi phí và usage để xem mức sử dụng theo service, account, Region hoặc các chiều dữ liệu có sẵn.
- Liên hệ budgets, quotas, tags và báo cáo usage như các cơ chế bổ trợ để hạn chế chi phí bất ngờ và lập kế hoạch capacity.
- Tìm hiểu policy của EBS Data Lifecycle Manager để tự động hóa snapshot EBS và các tác vụ vòng đời image được hỗ trợ theo lịch, quy tắc lưu giữ.
- Hiểu mục đích của phát hiện bất thường backup là nhận diện hoạt động hoặc mẫu backup khác thường để điều tra; cảnh báo vẫn cần được đánh giá.
- Khám phá AWS Toolkit for VS Code giúp duyệt tài nguyên AWS và làm việc với dịch vụ được hỗ trợ ngay trong editor.
- Nhận biết backup cần có retention, kiểm thử khôi phục, cảnh báo và rà soát chi phí, không chỉ dừng ở việc tạo snapshot thành công.
- Hiểu VPC endpoint giúp kết nối riêng tư đến các AWS service được hỗ trợ; cần chọn loại endpoint và policy phù hợp với đường truy cập dự kiến.
- Ôn tập AWS WAF là lớp kiểm tra/lọc HTTP request bằng managed rule hoặc custom rule; nó bổ sung chứ không thay thế thiết kế ứng dụng an toàn.
- Tìm hiểu lưu và truy xuất thông tin nhạy cảm như database credential qua Secrets Manager thay vì nhúng trong source code hoặc image.
- Nhận biết private connectivity, lọc request, xoay vòng secrets và IAM least privilege là các lớp kiểm soát riêng nhưng bổ trợ nhau.
- Tìm hiểu Cognito User Pool và khái niệm identity phục vụ đăng ký/đăng nhập, xác thực bằng token; phân quyền dữ liệu trong ứng dụng vẫn cần thiết kế riêng.
- Ôn tập bảo mật S3 như chặn public access ngoài ý muốn, giới hạn bucket policy, bật mã hóa phù hợp và kiểm tra quyền truy cập.
- Phân biệt xác thực người dùng với phân quyền IAM cho truy cập giữa các dịch vụ.
- Liên hệ danh tính người dùng, quyền API, bucket policy và truy cập dữ liệu object như các điểm quyết định khác nhau trong ứng dụng an toàn.

### Bài học rút ra

- Bí mật nên nằm trong kho bí mật được quản lý, không bao giờ để trong mã.
- Nên xem quota và báo cáo billing sớm để tránh bất ngờ.

### Bước tiếp theo

Học backup, nhắn tin với SQS/SNS và container ở Tuần 6.
