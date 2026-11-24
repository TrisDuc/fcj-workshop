---
title : "IAM đặc quyền tối thiểu và KMS"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.7.1 </b> "
---
#### 1. Mỗi function một role
Các policy template của SAM đã tạo role có phạm vi hẹp. Hãy rà soát mỗi function được làm gì:

| Function | Được phép |
| --- | --- |
| Webhook | Đọc/ghi bảng; gửi vào hàng đợi job; đọc `/askfirst/dev/telegram/*` |
| Worker | Đọc/ghi bảng; gửi vào hàng đợi job; đọc tham số Telegram; chỉ invoke function AI |
| AI | Đọc bucket mô hình; đọc/ghi bảng; đọc `/askfirst/dev/*`; `bedrock:InvokeModel` cho một mô hình và `bedrock:ApplyGuardrail` cho một guardrail |

Tìm mọi quyền rộng hơn bảng trên:
```
aws iam list-attached-role-policies --role-name <role-name>
aws iam list-role-policies --role-name <role-name>
```
Dùng kiểm tra policy của **IAM Access Analyzer** hoặc các phát hiện về quyền không dùng để siết chặt thêm.

#### 2. Quyết định nơi nào đáng dùng khóa KMS do khách hàng quản lý
| Tài nguyên | Mặc định | Nâng cấp tùy chọn |
| --- | --- | --- |
| SSM SecureString | Khóa do AWS quản lý (không phí khóa) | Khóa do khách hàng quản lý để kiểm toán và kiểm soát ở cấp khóa |
| DynamoDB, SQS, S3 | Mã hóa do AWS sở hữu / do dịch vụ quản lý | Khóa do khách hàng quản lý khi chính sách yêu cầu |

Một khóa do khách hàng quản lý tốn khoảng 1 USD mỗi tháng cộng phí request. Với bản thử nghiệm, một khóa cho các bí mật là mức cân bằng hợp lý.

#### 3. Tạo và dùng khóa (tùy chọn)
```
KEY_ID=$(aws kms create-key --description "AskFirst dev secrets" \
  --query KeyMetadata.KeyId --output text)
aws kms create-alias --alias-name alias/askfirst-dev --target-key-id $KEY_ID
aws ssm put-parameter --name /askfirst/dev/telegram/bot-token --type SecureString \
  --key-id alias/askfirst-dev --value "$BOT_TOKEN" --overwrite
```
Nếu đổi khóa, hãy cấp cho các function quyền `kms:Decrypt` trên khóa đó; nếu không chúng không đọc được tham số.

#### 4. Quy tắc vệ sinh
+ Không để access key trong mã, file môi trường hay secret của CI.
+ Không dùng resource `*` cho các hành động data-plane.
+ Xoay vòng bot token (qua BotFather) nếu nó từng bị lộ.
