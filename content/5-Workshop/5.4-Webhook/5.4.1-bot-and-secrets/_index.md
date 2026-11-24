---
title : "Create the bot and store secrets"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---
#### 1. Create the bot
1. In Telegram, open **@BotFather** and send `/newbot`.
2. Choose a display name and a username ending in `bot`.
3. Copy the **bot token**. Treat it like a password.

#### 2. Generate a webhook secret
Telegram can send a secret in the `X-Telegram-Bot-Api-Secret-Token` header on every request. Your function will reject any request without it.
```
openssl rand -hex 32
```
The secret may only contain letters, digits, `_`, and `-` (1–256 characters); a hex string satisfies this.

#### 3. Store both in SSM Parameter Store as SecureString
```
read -s BOT_TOKEN            # paste token, press Enter (not saved in history)
aws ssm put-parameter --name /askfirst/dev/telegram/bot-token \
  --type SecureString --value "$BOT_TOKEN" --overwrite
unset BOT_TOKEN

read -s WEBHOOK_SECRET
aws ssm put-parameter --name /askfirst/dev/telegram/webhook-secret \
  --type SecureString --value "$WEBHOOK_SECRET" --overwrite
```
{{% notice note %}}
SecureString parameters use the AWS-managed key `alias/aws/ssm` by default, which has no monthly key fee. You can switch to your own KMS key in [5.7.1](../../5.7-Security-Ops/5.7.1-iam-kms/).
{{% /notice %}}

#### 4. Confirm the parameters exist (without printing values)
```
aws ssm describe-parameters \
  --parameter-filters "Key=Name,Option=BeginsWith,Values=/askfirst/dev/" \
  --query "Parameters[].Name"
```
