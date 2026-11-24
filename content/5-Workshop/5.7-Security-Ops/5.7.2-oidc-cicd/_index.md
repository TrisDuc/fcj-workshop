---
title : "Keyless CI/CD with GitHub Actions OIDC"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.7.2 </b> "
---
With OpenID Connect, GitHub Actions requests short-lived credentials for an IAM role. No long-lived AWS access key is stored in GitHub.

#### 1. Register GitHub as an identity provider
```
aws iam create-open-id-connect-provider \
  --url https://token.actions.githubusercontent.com \
  --client-id-list sts.amazonaws.com
```

#### 2. Create the deployment role
Trust policy (replace `<ACCOUNT>`), limited to your repository and the `main` branch:
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
Attach a permissions policy that covers only what `sam deploy` needs: CloudFormation for the `askfirst-dev` stack, the Lambda/SQS/DynamoDB/S3/ECR actions for its resources, S3 access to the SAM artifact bucket, and `iam:PassRole` for the stack's execution roles. Start broad in a sandbox account and tighten with Access Analyzer.

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
+ Pull requests only **build and test**; they cannot assume the role because the trust policy accepts only `main`.
+ Pin third-party actions to a version (or commit) you have reviewed.

#### 4. Verify
Push to `main`, open the workflow run, and confirm the credentials step succeeds and the stack updates. In **CloudTrail**, the `AssumeRoleWithWebIdentity` event shows the GitHub repository as the caller.
