---
title : "Tạo bot và lưu bí mật"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---
#### 1. Tạo bot
1. Trong Telegram, mở **@BotFather** và gửi `/newbot`.
2. Chọn tên hiển thị và username kết thúc bằng `bot`.
3. Sao chép **bot token**. Hãy coi nó như mật khẩu.

#### 2. Tạo webhook secret
Telegram có thể gửi một secret trong header `X-Telegram-Bot-Api-Secret-Token` ở mỗi request. Function của bạn sẽ từ chối mọi request không có nó.
```
openssl rand -hex 32
```
Secret chỉ được chứa chữ cái, chữ số, `_` và `-` (1–256 ký tự); chuỗi hex thỏa mãn điều này.

#### 3. Lưu cả hai vào SSM Parameter Store dạng SecureString
```
read -s BOT_TOKEN            # dán token, nhấn Enter (không lưu vào history)
aws ssm put-parameter --name /askfirst/dev/telegram/bot-token \
  --type SecureString --value "$BOT_TOKEN" --overwrite
unset BOT_TOKEN

read -s WEBHOOK_SECRET
aws ssm put-parameter --name /askfirst/dev/telegram/webhook-secret \
  --type SecureString --value "$WEBHOOK_SECRET" --overwrite
```
{{% notice note %}}
Tham số SecureString mặc định dùng khóa do AWS quản lý `alias/aws/ssm`, không có phí khóa hằng tháng. Bạn có thể chuyển sang khóa KMS riêng ở [5.7.1](../../5.7-Security-Ops/5.7.1-iam-kms/).
{{% /notice %}}

#### 4. Xác nhận các tham số tồn tại (không in giá trị)
```
aws ssm describe-parameters \
  --parameter-filters "Key=Name,Option=BeginsWith,Values=/askfirst/dev/" \
  --query "Parameters[].Name"
```
