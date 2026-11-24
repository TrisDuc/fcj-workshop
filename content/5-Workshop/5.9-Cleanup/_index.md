---
title : "Clean up"
date : 2026-10-19
weight : 9
chapter : false
pre : " <b> 5.9. </b> "
---
Congratulations on completing this workshop! Delete everything you no longer need to avoid charges.

#### 1. Stop traffic
```
curl -s "https://api.telegram.org/bot${BOT_TOKEN}/deleteWebhook"
unset BOT_TOKEN WEBHOOK_SECRET
```

#### 2. Delete the stack
```
sam delete --stack-name askfirst-dev --region ap-southeast-1
```
This removes the functions, table, queues, and roles defined in the template. Delete the table's data first if you need an export.

#### 3. Resources created outside the stack
```
# Models bucket (empty it, then delete)
aws s3 rm s3://askfirst-dev-models-$ACCOUNT --recursive
aws s3api delete-bucket --bucket askfirst-dev-models-$ACCOUNT --region ap-southeast-1

# ECR repository and images
aws ecr delete-repository --repository-name askfirst-ai --force --region ap-southeast-1

# Parameters
aws ssm delete-parameters --names /askfirst/dev/telegram/bot-token /askfirst/dev/telegram/webhook-secret

# Guardrail (Sydney)
aws bedrock delete-guardrail --guardrail-identifier <GUARDRAIL_ID> --region ap-southeast-2

# Alarms, topic, and logs
aws cloudwatch delete-alarms --alarm-names askfirst-dev-dlq-not-empty askfirst-dev-worker-errors \
  askfirst-dev-webhook-errors askfirst-dev-ai-errors askfirst-dev-ai-slow
aws sns delete-topic --topic-arn $TOPIC_ARN
for f in webhook worker ai; do aws logs delete-log-group --log-group-name /aws/lambda/askfirst-dev-$f; done
```

#### 4. Security and audit resources
+ **KMS key** (if created): `aws kms schedule-key-deletion --key-id $KEY_ID --pending-window-in-days 7` and delete the alias.
+ **GitHub OIDC role and provider:** delete the role `askfirst-github-deploy`, then the OIDC provider if no other project uses it.
+ **CloudTrail trail and its S3 bucket:** delete the trail, then empty and delete the bucket.
+ **AWS Budget:** keep it if you continue to use the account.

#### 5. Final check
Open **Billing → Bills** and **Cost Explorer** the next day to confirm no unexpected charges remain.
