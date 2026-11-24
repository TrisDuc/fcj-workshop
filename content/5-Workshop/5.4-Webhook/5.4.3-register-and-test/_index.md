---
title : "Register the webhook and test"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---
#### 1. Register the webhook with Telegram
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
Expected: `{"ok":true,"result":true,"description":"Webhook was set"}`.

Check status:
```
curl -s "https://api.telegram.org/bot${BOT_TOKEN}/getWebhookInfo"
```
`pending_update_count` should be low and `last_error_message` should be absent.

#### 2. Test the three guards
| Test | How | Expected |
| --- | --- | --- |
| Wrong secret | `curl -X POST "$WEBHOOK_URL" -d '{}'` (no header) | Request rejected, nothing written |
| Duplicate update | Send the same update JSON twice with the correct header | Second call returns `200`, no second job |
| Oversized media | Send a file above 20 MB in Telegram | Friendly rejection, no message in SQS |

#### 3. Check the logs
```
aws logs tail /aws/lambda/askfirst-dev-webhook --since 10m --follow
```
Send `/start` to the bot and confirm an immediate reply (fast path).

{{% notice warning %}}
Do not paste the bot token or secret into screenshots or the report. Clear your shell variables when finished: `unset BOT_TOKEN WEBHOOK_SECRET`.
{{% /notice %}}
