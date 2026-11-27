---
title: "Tự đánh giá"
date: 2026-11-27
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Trong thời gian thực tập tại chương trình First Cloud AI Journey, tôi là thành viên backend (BE) của dự án nhóm **Ask First**. Tôi làm phần Java 21 của luồng kiểm tra tin nhắn lừa đảo: webhook handler cho Telegram (BE-22), bước chặn media quá lớn trước khi tạo job (BE-34) và worker gom tin nhắn rồi gọi Lambda AI. Tôi cũng đang làm cảnh báo người thân (FR6), nhắc lại sau 15 phút (FR9) và BE-29. Bảng dưới là tự đánh giá của tôi, dẫn chứng lấy từ worklog và repo dự án.

| STT | Tiêu chí | Tốt | Khá | Trung bình | Nhận xét / dẫn chứng |
|---|---|---|---|---|---|
| 1 | Kiến thức chuyên môn | ✅ | | | Làm thực tế với Lambda Java 21 (webhook handler và worker), SQS kèm dead-letter queue, thiết kế DynamoDB single-table, AWS SAM và môi trường Docker + LocalStack để chạy local. |
| 2 | Tự học | | ✅ | | Học các dịch vụ AWS trong tám tuần học và áp dụng vào dự án; một số phần như handler idempotent và retry của SQS cần thêm thời gian để nắm chắc. |
| 3 | Chủ động | ✅ | | | Nhận các issue BE-22 (#43) và BE-34 (#72), sau đó FR6 (#24) và FR9 (#27), và giữ ghi chú handoff cho worker để công việc có thể được tiếp tục và review. |
| 4 | Kỷ luật | | | ✅ | Công việc bám theo độ ưu tiên của issue, nhưng worklog chưa ghi đều từng ngày và tôi cần lên kế hoạch theo phụ thuộc sớm hơn (BE-29 cần BE-28 và INT-01 trước). |
| 5 | Giao tiếp | | ✅ | | Làm việc chủ yếu qua issue GitHub và contract BE ↔ AI dùng chung; phía Java khớp theo contract và không sửa code AI hay thư mục contracts. Còn có thể báo vướng mắc cho nhóm sớm hơn. |
| 6 | Teamwork | ✅ | | | BE-29 là việc làm theo cặp với một thành viên backend khác, và contract đã thống nhất giúp hai phía BE và AI làm song song. |
| 7 | Giải quyết vấn đề | | ✅ | | Thiết kế bộ đệm tin nhắn cửa sổ 3 giây (BUFFER# META + MSG# theo seq), chống trùng update Telegram bằng conditional write và chặn sớm media quá lớn để chúng không vào hàng đợi. |
| 8 | Đóng góp cho Workshop | ✅ | | | Luồng webhook, worker, cảnh báo người thân và nhắc lại mà tôi làm được mô tả từng bước trong Workshop. |

### Cần cải thiện
* **Ghi worklog hằng ngày.** Ghi ngay khi làm thay vì bù lại sau.
* **Lên kế hoạch theo phụ thuộc.** Kiểm tra trước một issue cần gì (ví dụ BE-28 và INT-01 cho BE-29) và báo sớm khi bị vướng.
* **Test song song khi code.** Viết test cho giá trị biên và hành vi retry cùng lúc với code, không để đến cuối.
