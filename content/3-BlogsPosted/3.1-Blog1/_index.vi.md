---
title: "Blog 1 - Lambda + EventBridge: Kiến trúc hướng sự kiện để tự cập nhật dữ liệu hằng ngày"
date: 2026-11-21
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

### Thông tin bài đăng

- **Tác giả:** Võ Đức Trí (tên hiển thị Facebook: Trí Đức Võ).
- **Ngày đăng:** 21/11/2026, lúc 23:21 theo ảnh bài đăng.
- **Nơi đăng:** AWS Study Group VN trên Facebook.
- **Bài gốc:** TBD — dán link bài đăng Facebook của bạn vào đây.

### Lambda + EventBridge: Cách mình dùng Event-driven Architecture để tự cập nhật data hằng ngày

**Bài toán:** Mỗi sáng tác giả mở bốn, năm tab để xem giá xăng, giá vàng, tỉ số esport và thời tiết. Mục tiêu là một trang duy nhất tự cập nhật, xây bằng AWS Lambda và Amazon EventBridge để mỗi phần chỉ phản ứng với sự kiện của riêng nó.

#### Yêu cầu đặt ra
- **Không chạy server 24/7:** chỉ tốn tài nguyên khi thật sự có việc.
- **Mỗi nguồn một nhịp riêng:** thời tiết thay đổi trong ngày, tỉ số esport cần nhanh hơn vào giờ thi đấu, giá xăng điều chỉnh theo kỳ.
- **Một nguồn lỗi không làm hỏng cả trang:** API giá vàng chết thì thời tiết vẫn phải hiện.
- **Dễ thêm nguồn mới** và **chi phí cá nhân nhỏ.**

#### Event-driven ở đây nghĩa là gì?
Một script lấy mọi thứ rồi dựng trang có thể bị ảnh hưởng chỉ vì một API chậm hoặc lỗi. Với thiết kế hướng sự kiện, sự kiện "đến giờ lấy giá vàng" gọi một Lambda, còn sự kiện "có dữ liệu mới trong kho" gọi một Lambda khác dựng lại trang. Hai phần không gọi trực tiếp nhau mà được EventBridge nối bằng sự kiện, nên mỗi phần có thể chạy, lỗi và thay đổi độc lập.

{{< event-photo src="images/blogs/lambda-eventbridge/architecture.png" alt="EventBridge Scheduler gọi Lambda collector ghi vào S3; sự kiện S3 Object Created đi qua EventBridge rule tới Lambda renderer ghi trang vào S3, phân phối qua CloudFront." caption="Kiến trúc trong bài: Scheduler, Lambda collector, S3, EventBridge rule, Lambda renderer, S3 và CloudFront." >}}

#### Kiến trúc
Website là các file HTML tĩnh trên Amazon S3, phân phối qua Amazon CloudFront.

1. **EventBridge Scheduler** đóng vai trò đồng hồ. Mỗi nguồn có một schedule riêng và gửi payload JSON như `{"source": "gold"}`.
2. **Lambda collector** lấy dữ liệu một nguồn, chuẩn hóa thành JSON gọn và ghi `data/<nguồn>.json` vào S3.
3. **EventBridge rule** bắt sự kiện *Object Created* của S3 với prefix `data/`.
4. **Lambda renderer** gộp các file JSON, dựng lại `index.html` và làm mới cache CloudFront.

#### Bước 1: Mỗi nguồn một nhịp với EventBridge Scheduler
Scheduler gọi Lambda theo kiểu bất đồng bộ và hỗ trợ lịch một lần hoặc định kỳ bằng biểu thức `rate` hoặc `cron`. Vì mỗi schedule có thể gửi kèm payload riêng nên chỉ cần một hàm collector cho mọi nguồn. Scheduler cũng hỗ trợ múi giờ nên có thể đặt giờ Việt Nam. Các lịch dự kiến: thời tiết mỗi 3 giờ, giá xăng và vàng theo ngày hoặc tuần, tỉ số trận đấu mỗi 30 phút. Mỗi schedule cần một execution role; retry policy và dead-letter queue giúp các lần gọi thất bại không biến mất âm thầm.

#### Bước 2: Lambda collector
Collector đọc tên nguồn từ payload, gọi API tương ứng có timeout, chuẩn hóa kết quả và ghi vào S3. Chuẩn hóa ngay tại đây giúp renderer không cần biết định dạng của từng nguồn. Một nguyên tắc được giữ: **nếu một nguồn lỗi, Lambda báo lỗi và không ghi đè file cũ**, nhờ vậy trang vẫn có dữ liệu lần trước thay vì bị trống. Với nguồn dữ liệu, ưu tiên API chính thức hoặc nguồn cho phép truy cập tự động, và đọc kỹ điều khoản sử dụng của từng nơi.

#### Bước 3: Sự kiện nối Collector với Renderer
Collector ghi xong file là xong việc, không cần biết ai dùng file đó. S3 có thể gửi sự kiện của bucket sang EventBridge (bật trong mục Event notifications của bucket), và một EventBridge rule bắt sự kiện *Object Created* của bucket với prefix `data/`. Có hai điểm cần nhớ:

- **Phải lọc prefix `data/`.** Renderer cũng ghi `index.html` vào cùng bucket. Nếu rule bắt mọi object mới thì lần ghi đó lại gọi renderer, tạo thành vòng lặp vô tận.
- **S3 giao sự kiện kiểu at-least-once** nên có thể nhận sự kiện trùng. Renderer được thiết kế **idempotent**: luôn dựng lại trang từ dữ liệu hiện có và ghi đè, nên chạy hai lần cho cùng kết quả.

CloudFront cache nội dung ở edge nên renderer tạo invalidation cho `/index.html`. Mỗi mục trên trang hiển thị `updatedAt` của nguồn đó để dễ biết số liệu nào đang cũ.

#### Điểm rút ra
- Các phần nhỏ, chạy độc lập: lỗi không lan, thêm nguồn mới chỉ là thêm một schedule và một hàm chuẩn hóa.
- Điều thú vị không phải là viết một Lambda mà là cách các dịch vụ AWS kết nối thành một hệ thống hoàn chỉnh mà không cần server chạy liên tục.

Bạn kích hoạt Lambda bằng gì: lịch, sự kiện S3 hay thứ khác?

### Minh chứng bài đã đăng

{{< event-photo src="images/blogs/lambda-eventbridge/facebook-publication.png" alt="Ảnh bài đăng của Trí Đức Võ trong AWS Study Group VN, ngày 21 tháng 11 lúc 23:21." caption="Ảnh xác nhận bài Blog 1 đã đăng trên AWS Study Group VN do tác giả cung cấp." >}}

### Tài liệu tham khảo

- [Invoke a Lambda function on a schedule – AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/with-eventbridge-scheduler.html)
- [EventBridge Scheduler User Guide](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html)
- [New – Use Amazon S3 Event Notifications with Amazon EventBridge – AWS News Blog](https://aws.amazon.com/blogs/aws/new-use-amazon-s3-event-notifications-with-amazon-eventbridge/)
- Step-by-step guide to CloudFront invalidation in CodePipeline using Lambda – Medium
