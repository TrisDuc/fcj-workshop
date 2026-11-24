---
title: "Worklog Tuần 1"
date: 2026-09-14
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

Trong Tuần 1 (14/09/2026–18/09/2026), tôi làm quen với chương trình và xây nền tảng cho một tài khoản AWS: onboarding, an toàn chi phí, IAM và mạng VPC.

### Mục tiêu tuần 1

- Hoàn tất onboarding, làm quen với các thành viên FCAJ và nắm quy định, quy trình làm việc khi thực tập.
- Xây dựng nền tảng về quản lý tài khoản AWS, an toàn chi phí, IAM và mạng VPC.

### Nhật ký công việc theo ngày

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Tiếp nhận thực tập, làm quen với mọi người và tìm hiểu quy định. | 14/09/2026 | 14/09/2026 | — |
| 3 | Hoàn thiện môi trường làm việc, tìm hiểu quyền truy cập AWS và lưu ý về chi phí. | 15/09/2026 | 15/09/2026 | — |
| 4 | Tìm hiểu tài khoản AWS, ngân sách, hỗ trợ và các nhóm dịch vụ chính. | 16/09/2026 | 16/09/2026 | [Creating Your First AWS Account](https://000001.awsstudygroup.com/)<br>[AWS Budgets](https://000007.awsstudygroup.com/)<br>[AWS Support](https://000009.awsstudygroup.com/) |
| 5 | Tìm hiểu nền tảng IAM và VPC. | 17/09/2026 | 17/09/2026 | [AWS IAM](https://000002.awsstudygroup.com/)<br>[Amazon VPC](https://000003.awsstudygroup.com/) |

### Kết quả đạt được

- Làm quen với các thành viên FCAJ, kênh trao đổi và cách đặt câu hỏi, cập nhật tiến độ.
- Đọc và ghi chú các quy định thực tập, tác phong làm việc, yêu cầu bảo mật và thủ tục hành chính cần thiết.
- Xác định nguyên tắc chỉ tạo tài khoản hoặc tài nguyên cloud theo hướng dẫn được phép.
- Chuẩn bị công cụ phát triển cần thiết và sắp xếp tài liệu học tập được cung cấp.
- Ôn tập cách bảo vệ thông tin xác thực, vai trò của MFA và lý do không nên dùng root user cho công việc thường ngày.
- Nhận biết cần theo dõi billing, thiết lập ngân sách và dọn dẹp tài nguyên sau khi thực hành.
- Hiểu tài khoản AWS là phạm vi để quản lý tài nguyên cloud, thanh toán, danh tính và dịch vụ theo Region.
- Tìm hiểu AWS Budgets để đặt ngưỡng chi phí và nhận cảnh báo; budget chủ yếu dùng theo dõi và cảnh báo, không tự động là giới hạn chi tiêu cứng.
- Biết nơi tìm AWS Support và tài liệu chính thức khi gặp vấn đề về dịch vụ hoặc tài khoản.
- Phân loại các nhóm dịch vụ chính như compute, storage, networking, database, security và monitoring theo nhiệm vụ thường gặp.
- Hiểu cách IAM User, Group, Role và Policy xác định ai được thực hiện hành động nào trên tài nguyên AWS, theo nguyên tắc đặc quyền tối thiểu.
- Phân biệt quyền gắn với danh tính và IAM Role mà ứng dụng hoặc dịch vụ AWS đảm nhận để truy cập dịch vụ khác.
- Tìm hiểu VPC, dải CIDR, public/private subnet, route table, internet gateway và security group như các thành phần tạo nên mạng cô lập.
- Nhận biết để kết nối được cần cấu hình đúng cả định tuyến lẫn các quy tắc bảo mật.

### Bài học rút ra

- Thiết lập cảnh báo chi phí và quyền tối thiểu trước khi tạo tài nguyên.
- Thiết kế mạng (subnet, định tuyến, security group) quyết định thành phần nào được truy cập thành phần nào.

### Bước tiếp theo

Tiếp tục với các dịch vụ compute và storage ở Tuần 2.
