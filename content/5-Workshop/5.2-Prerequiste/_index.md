---
title : "Prerequisites"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---
#### Accounts and access
+ An **AWS account** (do not use the root user for daily work; enable MFA).
+ An **IAM user or role** for the workshop with permission to manage CloudFormation, Lambda, IAM roles for the stack, SQS, DynamoDB, S3, ECR, SSM, KMS, CloudWatch, SNS, Budgets, and CloudTrail. Tighten it after the build.
+ **Amazon Bedrock** access in **ap-southeast-2** for your chosen model (the architecture uses Qwen3 32B). Confirm in the Bedrock console that the model is listed for your account and Region; if not, choose an available model and update `MODEL_ID` later.
+ A **Telegram account** and a bot created with **@BotFather** (you will keep the bot token secret).
+ A **GitHub repository** (the project uses `ask-first-fcaj/ask-first`).

#### Tools
```
aws --version        # AWS CLI v2
sam --version        # AWS SAM CLI
java -version        # 21
mvn -version         # 3.9+
docker --version
python3 --version    # 3.12 (match the Lambda base image)
git --version
```

Configure a named profile and use it for every command:
```
aws configure --profile askfirst
export AWS_PROFILE=askfirst
export AWS_REGION=ap-southeast-1
```

#### Repository layout
```
ask-first/
├── pom.xml                 # Maven parent (modules below)
├── common/                 # shared models, DynamoDB/SQS helpers
├── webhook-handler/        # Lambda: Telegram webhook
├── worker/                 # Lambda: SQS consumer
├── ai/                     # Python AI Lambda (container)
├── contracts/              # JSON contract between worker and AI
├── template.yaml           # SAM template (grows through this workshop)
├── samconfig.toml
└── .github/workflows/deploy.yml
```

{{% notice warning %}}
Never commit the bot token, the webhook secret, or API keys. They will live in **SSM Parameter Store** (section 5.4 and 5.7).
{{% /notice %}}

#### Set a budget first
Before creating billable resources, create a monthly AWS Budget with an email alert (see [5.7.3](../5.7-Security-Ops/5.7.3-monitoring/)). It takes two minutes and protects you from surprises.
