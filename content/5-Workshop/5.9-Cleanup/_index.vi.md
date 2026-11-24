---
title : "Dọn dẹp"
date : 2026-10-19
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---
Chúc mừng bạn đã hoàn thành workshop! Hãy xóa mọi thứ không còn cần để tránh phát sinh chi phí.

#### 1. Dừng lưu lượng
```
curl -s "https://api.telegram.org/bot${BOT_TOKEN}/deleteWebhook"
unset BOT_TOKEN WEBHOOK_SECRET
```

#### 2. Xóa stack
```
sam delete --stack-name askfirst-dev --region ap-southeast-1
```
Lệnh này xóa các function, bảng, hàng đợi và role được định nghĩa trong template. Hãy xuất dữ liệu bảng trước nếu cần.

#### 3. Tài nguyên tạo ngoài stack
```
# Bucket mô hình (làm rỗng rồi xóa)
aws s3 rm s3://askfirst-dev-models-$ACCOUNT --recursive
aws s3api delete-bucket --bucket askfirst-dev-models-$ACCOUNT --region ap-southeast-1

# ECR repository và các image
aws ecr delete-repository --repository-name askfirst-ai --force --region ap-southeast-1

# Tham số
aws ssm delete-parameters --names /askfirst/dev/telegram/bot-token /askfirst/dev/telegram/webhook-secret

# Guardrail (Sydney)
aws bedrock delete-guardrail --guardrail-identifier <GUARDRAIL_ID> --region ap-southeast-2

# Alarm, topic và log
aws cloudwatch delete-alarms --alarm-names askfirst-dev-dlq-not-empty askfirst-dev-worker-errors \
  askfirst-dev-webhook-errors askfirst-dev-ai-errors askfirst-dev-ai-slow
aws sns delete-topic --topic-arn $TOPIC_ARN
for f in webhook worker ai; do aws logs delete-log-group --log-group-name /aws/lambda/askfirst-dev-$f; done
```

#### 4. Tài nguyên bảo mật và kiểm toán
+ **Khóa KMS** (nếu đã tạo): `aws kms schedule-key-deletion --key-id $KEY_ID --pending-window-in-days 7` và xóa alias.
+ **Role và provider OIDC của GitHub:** xóa role `askfirst-github-deploy`, sau đó xóa OIDC provider nếu không dự án nào khác dùng.
+ **CloudTrail trail và bucket S3 của nó:** xóa trail, rồi làm rỗng và xóa bucket.
+ **AWS Budget:** giữ lại nếu bạn tiếp tục dùng tài khoản.

#### 5. Kiểm tra cuối
Mở **Billing → Bills** và **Cost Explorer** vào hôm sau để xác nhận không còn khoản phí bất ngờ nào.
