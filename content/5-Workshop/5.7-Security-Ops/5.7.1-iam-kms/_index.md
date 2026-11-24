---
title : "IAM least privilege and KMS"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.7.1 </b> "
---
#### 1. One role per function
SAM policy templates already created scoped roles. Review what each function can do:

| Function | Allowed |
| --- | --- |
| Webhook | Read/write the table; send to the jobs queue; read `/askfirst/dev/telegram/*` |
| Worker | Read/write the table; send to the jobs queue; read Telegram parameters; invoke **only** the AI function |
| AI | Read the models bucket; read/write the table; read `/askfirst/dev/*`; `bedrock:InvokeModel` for one model and `bedrock:ApplyGuardrail` for one guardrail |

Look for anything broader than this table:
```
aws iam list-attached-role-policies --role-name <role-name>
aws iam list-role-policies --role-name <role-name>
```
Use **IAM Access Analyzer** policy validation or unused-access findings to tighten further.

#### 2. Decide where a customer-managed KMS key is worth it
| Resource | Default | Optional upgrade |
| --- | --- | --- |
| SSM SecureString | AWS-managed key (no key fee) | Customer-managed key for key-level audit and control |
| DynamoDB, SQS, S3 | AWS-owned / service-managed encryption | Customer-managed key when policy requires it |

A customer-managed key costs about 1 USD per month plus request charges. For a pilot, one key for secrets is a reasonable balance.

#### 3. Create and use a key (optional)
```
KEY_ID=$(aws kms create-key --description "AskFirst dev secrets" \
  --query KeyMetadata.KeyId --output text)
aws kms create-alias --alias-name alias/askfirst-dev --target-key-id $KEY_ID
aws ssm put-parameter --name /askfirst/dev/telegram/bot-token --type SecureString \
  --key-id alias/askfirst-dev --value "$BOT_TOKEN" --overwrite
```
If you switch keys, grant the functions `kms:Decrypt` on that key; otherwise they cannot read the parameter.

#### 4. Housekeeping rules
+ No access keys in code, environment files, or CI secrets.
+ No wildcard `*` resources for data-plane actions.
+ Rotate the bot token (via BotFather) if it is ever exposed.
