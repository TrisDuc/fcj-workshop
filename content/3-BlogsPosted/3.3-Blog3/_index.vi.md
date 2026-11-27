---
title: "Blog 3 - Luồng dữ liệu IoT tối ưu chi phí: MQTT → AWS IoT Core → Glue → S3"
date: 2026-11-26
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

### Thông tin bài đăng

- **Tác giả:** Ngô Thị Kim Ngân (tên hiển thị Facebook: Ngô Thị Kim Ngân), thành viên trong nhóm.
- **Ngày đăng:** 26/11/2026, lúc 22:44 theo ảnh bài đăng.
- **Nơi đăng:** AWS Study Group VN trên Facebook.
- **Bài gốc:** TBD — dán link bài đăng Facebook vào đây.

### Thiết kế luồng dữ liệu IoT tối ưu chi phí cho đồ án quy mô nhỏ (MQTT → AWS IoT Core → Glue → S3)

**Bài toán:** Dự án trạm quan trắc thời tiết dùng cảm biến ESP32 hoặc Raspberry Pi thường chỉ dừng ở việc đẩy dữ liệu qua MQTT lên dashboard. Khi chạy 24/7 trong vài tháng, hàng triệu bản tin JSON nhỏ làm phình chi phí API request, truy vấn chậm và dữ liệu thô khó phân tích về sau. Bài viết giới thiệu kiến trúc serverless để thu thập và tối ưu dữ liệu cảm biến dài hạn với chi phí rất thấp cho quy mô đồ án sinh viên.

{{< event-photo src="images/blogs/iot-parquet/architecture.png" alt="Cảm biến ESP32 hoặc Raspberry Pi gửi MQTT qua TLS tới AWS IoT Core, rule engine ghi JSON vào S3 raw bucket; EventBridge Scheduler kích hoạt hằng ngày một Glue job chuyển JSON sang Parquet trong S3 curated bucket cho Athena truy vấn; S3 lifecycle policy xóa JSON thô sau 7 ngày." caption="Kiến trúc trong bài: IoT Core rule ghi S3 raw, Glue chuyển sang Parquet hằng ngày và truy vấn bằng Athena." >}}

#### Vấn đề: "Cơn ác mộng small files"
Nếu mỗi 10 giây một thiết bị gửi một bản tin JSON khoảng 200 bytes, thì 10 thiết bị trong 3 tháng tạo ra khoảng 7,7 triệu file nhỏ trên S3 theo ví dụ của bài viết.

- **Tốn phí API request:** S3 tính phí theo số lượng PUT/GET, nên hàng triệu file nhỏ có thể tốn phí request hơn cả phí lưu trữ.
- **Truy vấn chậm và đắt:** Amazon Athena phải mở từng file JSON, vừa chậm vừa quét nhiều dữ liệu.

#### Kiến trúc: giai đoạn Nóng và Nguội
- **Bước 1 — Định tuyến từ IoT Core xuống S3 theo phân vùng thời gian.** Thay vì dựng EC2 hay Lambda chỉ để hứng MQTT, dùng IoT Core Rule Engine lọc bằng SQL và ghi thẳng vào S3 raw bucket theo đường dẫn như `raw-data/year=YYYY/month=MM/day=DD/hour=HH/`.
- **Bước 2 — Gộp file và chuyển sang Parquet bằng AWS Glue.** Cuối mỗi ngày, Amazon EventBridge Scheduler kích hoạt một Glue job (PySpark hoặc Python Shell) đọc JSON của ngày hôm trước, làm sạch kiểu dữ liệu, gộp lại và xuất Apache Parquet nén Snappy vào S3 curated bucket.
- **Bước 3 — Dọn dẹp tự động bằng S3 Lifecycle policy.** Xóa JSON thô sau 7 ngày, vừa có bản backup một tuần để debug vừa không tốn phí lưu trữ vĩnh viễn.

#### Kết quả trong kịch bản của bài (10 triệu bản tin mỗi tháng, khoảng 2 GB JSON thô)
| Chỉ số | Trước | Sau |
| --- | --- | --- |
| Dung lượng S3 | ~2,0 GB | ~280 MB (giảm khoảng 85%) |
| Số lượng file | 10.000.000 | ~30 (một file mỗi ngày) |
| Dữ liệu Athena quét khi query một cột | 2,0 GB | ~40 MB (giảm khoảng 98%) |

Các con số này là ước tính minh họa của tác giả cho kịch bản, không phải số đo từ hệ thống production.

#### Mẹo để không "lố" tiền khi chạy AWS Glue
- Nếu dùng Spark: chọn worker type G.1X và giới hạn tối đa 2 worker.
- Nếu dữ liệu nhỏ (dưới vài trăm MB mỗi ngày): dùng AWS Glue Python Shell (0,0625 DPU) kết hợp thư viện `awswrangler` để chuyển JSON sang Parquet chỉ trong vài giây.

#### Điểm rút ra
- Lưu thô có phân vùng, job gộp file hằng ngày và tự động xóa dữ liệu cũ giúp dự án nhỏ vừa rẻ vừa truy vấn được.
- EventBridge Scheduler là cách đơn giản để chạy việc theo lô theo nhịp cố định mỗi ngày mà không cần server.

### Minh chứng bài đã đăng

{{< event-photo src="images/blogs/iot-parquet/facebook-publication.png" alt="Ảnh bài đăng của Ngô Thị Kim Ngân trong AWS Study Group VN, ngày 26 tháng 11 lúc 22:44." caption="Ảnh bài Blog 3 đã đăng trên AWS Study Group VN." >}}

### Tài liệu tham khảo

- [AWS IoT rules](https://docs.aws.amazon.com/iot/latest/developerguide/iot-rules.html)
- [EventBridge Scheduler User Guide](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html)
- [Tài liệu AWS Glue](https://docs.aws.amazon.com/glue/)
- [Amazon S3 Lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)
