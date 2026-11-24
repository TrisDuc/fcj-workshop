---
title : "Chuẩn bị"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---
#### Tài khoản và quyền truy cập
+ Một **tài khoản AWS** (không dùng root user cho công việc hằng ngày; bật MFA).
+ Một **IAM user hoặc role** cho workshop có quyền quản lý CloudFormation, Lambda, các IAM role của stack, SQS, DynamoDB, S3, ECR, SSM, KMS, CloudWatch, SNS, Budgets và CloudTrail. Hãy siết chặt lại sau khi dựng xong.
+ Quyền truy cập **Amazon Bedrock** ở **ap-southeast-2** cho mô hình bạn chọn (kiến trúc dùng Qwen3 32B). Hãy xác nhận trong console Bedrock rằng mô hình có trong tài khoản và Region của bạn; nếu không, chọn mô hình khả dụng và cập nhật `MODEL_ID` sau.
+ Một **tài khoản Telegram** và một bot tạo bằng **@BotFather** (bạn sẽ giữ bí mật bot token).
+ Một **repository GitHub** (dự án dùng `ask-first-fcaj/ask-first`).

#### Công cụ
```
aws --version        # AWS CLI v2
sam --version        # AWS SAM CLI
java -version        # 21
mvn -version         # 3.9+
docker --version
python3 --version    # 3.12 (match the Lambda base image)
git --version
```

Cấu hình một profile có tên và dùng cho mọi lệnh:
```
aws configure --profile askfirst
export AWS_PROFILE=askfirst
export AWS_REGION=ap-southeast-1
```

#### Cấu trúc repository
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
Không bao giờ commit bot token, webhook secret hay API key. Chúng sẽ nằm trong **SSM Parameter Store** (mục 5.4 và 5.7).
{{% /notice %}}

#### Đặt ngân sách trước
Trước khi tạo tài nguyên tính phí, hãy tạo một AWS Budget hằng tháng kèm cảnh báo email (xem [5.7.3](../5.7-Security-Ops/5.7.3-monitoring/)). Chỉ mất hai phút và giúp bạn tránh bất ngờ.
