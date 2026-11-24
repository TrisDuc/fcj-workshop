---
title : "CI/CD không dùng khóa với GitHub Actions OIDC"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.7.2 </b> "
---
Với OpenID Connect, GitHub Actions xin thông tin xác thực ngắn hạn cho một IAM role. Không có access key AWS dài hạn nào được lưu trong GitHub.

#### 1. Đăng ký GitHub làm identity provider
```
aws iam create-open-id-connect-provider \
  --url https://token.actions.githubusercontent.com \
  --client-id-list sts.amazonaws.com
```

#### 2. Tạo role triển khai
Trust policy (thay `<ACCOUNT>`), giới hạn cho repository của bạn và nhánh `main`:
```json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Federated": "arn:aws:iam::<ACCOUNT>:oidc-provider/token.actions.githubusercontent.com" },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
      "StringEquals": { "token.actions.githubusercontent.com:aud": "sts.amazonaws.com" },
      "StringLike":   { "token.actions.githubusercontent.com:sub": "repo:ask-first-fcaj/ask-first:ref:refs/heads/main" }
    }
  }]
}
```
```
aws iam create-role --role-name askfirst-github-deploy \
  --assume-role-policy-document file://trust.json
```
Gắn một permissions policy chỉ bao gồm những gì `sam deploy` cần: CloudFormation cho stack `askfirst-dev`, các hành động Lambda/SQS/DynamoDB/S3/ECR cho tài nguyên của nó, quyền S3 tới bucket artifact của SAM và `iam:PassRole` cho các execution role của stack. Bắt đầu rộng trong tài khoản sandbox rồi siết chặt bằng Access Analyzer.

#### 3. Workflow
`.github/workflows/deploy.yml`:
```yaml
name: deploy
on:
  push: { branches: [main] }
  pull_request:
permissions:
  id-token: write
  contents: read
jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with: { distribution: temurin, java-version: '21' }
      - run: mvn -B verify
  deploy:
    if: github.ref == 'refs/heads/main'
    needs: build-test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with: { distribution: temurin, java-version: '21' }
      - uses: aws-actions/setup-sam@v2
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::<ACCOUNT>:role/askfirst-github-deploy
          aws-region: ap-southeast-1
      - run: sam build
      - run: sam deploy --no-confirm-changeset --no-fail-on-empty-changeset
```
+ Pull request chỉ **build và test**; chúng không thể assume role vì trust policy chỉ chấp nhận `main`.
+ Ghim các action bên thứ ba vào phiên bản (hoặc commit) bạn đã xem xét.

#### 4. Kiểm tra
Push lên `main`, mở lần chạy workflow và xác nhận bước lấy thông tin xác thực thành công và stack được cập nhật. Trong **CloudTrail**, sự kiện `AssumeRoleWithWebIdentity` cho thấy repository GitHub là bên gọi.
