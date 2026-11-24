---
title : "DynamoDB single-table"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---
#### 1. Bắt đầu từ access pattern
Viết ra các câu hỏi hệ thống phải trả lời, rồi suy ra key từ đó.

| Access pattern | PK | SK |
| --- | --- | --- |
| Update Telegram này đã được xử lý chưa? | `UPD#<update_id>` | `DEDUP` |
| Trạng thái buffer tin nhắn đang chờ của người dùng | `BUFFER#<chat_id>` | `META` |
| Các tin nhắn đang chờ theo thứ tự | `BUFFER#<chat_id>` | `MSG#<seq>` |
| Hồ sơ người dùng, sự đồng ý và cờ tính năng | `USER#<chat_id>` | `PROFILE` |
| Lịch sử các lần kiểm tra của người dùng | `USER#<chat_id>` | `CHECK#<timestamp>#<id>` |
| Kết quả đã cache cho một URL/tên miền | `URL#<sha256>` | `CACHE` |
| Bản ghi cảnh báo người thân | `USER#<chat_id>` | `ALERT#<timestamp>` |

Các item tồn tại ngắn (`UPD#`, `BUFFER#`, `URL#`) có thuộc tính số `ttl` để DynamoDB tự xóa.

#### 2. Thêm bảng vào `template.yaml`
```yaml
Resources:
  MainTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Sub askfirst-${Stage}-main
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - { AttributeName: PK, AttributeType: S }
        - { AttributeName: SK, AttributeType: S }
      KeySchema:
        - { AttributeName: PK, KeyType: HASH }
        - { AttributeName: SK, KeyType: RANGE }
      TimeToLiveSpecification:
        AttributeName: ttl
        Enabled: true
      SSESpecification:
        SSEEnabled: true
```
+ **Tính phí on-demand** phù hợp với lưu lượng thử nghiệm khó đoán.
+ **TTL** xóa buffer, dấu chống trùng và mục cache mà không cần thêm mã.

#### 3. Kiểm tra sau khi triển khai (mục 5.3.2 triển khai stack)
```
aws dynamodb describe-table --table-name askfirst-dev-main \
  --query "Table.{Status:TableStatus,Billing:BillingModeSummary.BillingMode}"
```
Kết quả mong đợi: `ACTIVE` và `PAY_PER_REQUEST`.
