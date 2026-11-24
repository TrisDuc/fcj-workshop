---
title : "Đăng ký webhook và kiểm thử"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---
#### 1. Đăng ký webhook với Telegram
```
WEBHOOK_URL=$(aws cloudformation describe-stacks --stack-name askfirst-dev \
  --query "Stacks[0].Outputs[?OutputKey=='WebhookUrl'].OutputValue" --output text)
BOT_TOKEN=$(aws ssm get-parameter --with-decryption \
  --name /askfirst/dev/telegram/bot-token --query Parameter.Value --output text)
WEBHOOK_SECRET=$(aws ssm get-parameter --with-decryption \
  --name /askfirst/dev/telegram/webhook-secret --query Parameter.Value --output text)

curl -s "https://api.telegram.org/bot${BOT_TOKEN}/setWebhook" \
  --data-urlencode "url=${WEBHOOK_URL}" \
  --data-urlencode "secret_token=${WEBHOOK_SECRET}" \
  --data-urlencode 'allowed_updates=["message","callback_query"]'
```
Kết quả mong đợi: `{"ok":true,"result":true,"description":"Webhook was set"}`.

Kiểm tra trạng thái:
```
curl -s "https://api.telegram.org/bot${BOT_TOKEN}/getWebhookInfo"
```
`pending_update_count` nên thấp và không có `last_error_message`.

#### 2. Kiểm thử ba lớp chặn
| Kiểm thử | Cách làm | Kết quả mong đợi |
| --- | --- | --- |
| Secret sai | `curl -X POST "$WEBHOOK_URL" -d '{}'` (không có header) | Request bị từ chối, không ghi gì |
| Update trùng | Gửi cùng JSON update hai lần với header đúng | Lần hai trả `200`, không có job thứ hai |
| Media quá lớn | Gửi file trên 20 MB trong Telegram | Từ chối thân thiện, không có message trong SQS |

#### 3. Xem log
```
aws logs tail /aws/lambda/askfirst-dev-webhook --since 10m --follow
```
Gửi `/start` cho bot và xác nhận có phản hồi ngay (đường nhanh).

{{% notice warning %}}
Không dán bot token hoặc secret vào ảnh chụp màn hình hay báo cáo. Xóa biến shell khi xong: `unset BOT_TOKEN WEBHOOK_SECRET`.
{{% /notice %}}
