---
title : "Mô hình trong S3 và container image trong ECR"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.6.1 </b> "
---
#### 1. Tạo bucket cho mô hình và chỉ mục knowledge base
```
ACCOUNT=$(aws sts get-caller-identity --query Account --output text)
BUCKET=askfirst-dev-models-$ACCOUNT
aws s3api create-bucket --bucket $BUCKET --region ap-southeast-1 \
  --create-bucket-configuration LocationConstraint=ap-southeast-1
aws s3api put-public-access-block --bucket $BUCKET \
  --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true
aws s3api put-bucket-encryption --bucket $BUCKET \
  --server-side-encryption-configuration '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'
```

#### 2. Tải các artifact lên
```
aws s3 cp models/ s3://$BUCKET/models/ --recursive     # OCR PP-OCRv5 (ONNX), embedding e5-small int8
aws s3 cp kb-index/ s3://$BUCKET/kb-index/ --recursive # chỉ mục knowledge base lừa đảo
aws s3 ls s3://$BUCKET/ --recursive --human-readable
```
Khi cold start, Lambda AI tải các file này vào **bộ nhớ** (bước 8 trong kiến trúc) và tái sử dụng cho các lần gọi sau trong cùng execution environment.

#### 3. Tạo ECR repository và đẩy image
```
aws ecr create-repository --repository-name askfirst-ai --region ap-southeast-1 \
  --image-scanning-configuration scanOnPush=true
REGISTRY=$ACCOUNT.dkr.ecr.ap-southeast-1.amazonaws.com
aws ecr get-login-password --region ap-southeast-1 | \
  docker login --username AWS --password-stdin $REGISTRY
docker build -t askfirst-ai:v1 ai/
docker tag askfirst-ai:v1 $REGISTRY/askfirst-ai:v1
docker push $REGISTRY/askfirst-ai:v1
```
Dùng base image tương thích Lambda (ví dụ base image Python của AWS) để có sẵn runtime interface client.

#### 4. Thêm function vào `template.yaml`
```yaml
Parameters:
  AiImageTag:
    Type: String
    Default: v1
  BedrockModelId:
    Type: String
    Default: qwen.qwen3-32b-v1:0          # kiểm tra lại ID trong console Bedrock
  GuardrailId:
    Type: String
  GuardrailVersion:
    Type: String
    Default: "1"

Resources:
  AiFunction:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: !Sub askfirst-${Stage}-ai
      PackageType: Image
      ImageUri: !Sub ${AWS::AccountId}.dkr.ecr.${AWS::Region}.amazonaws.com/askfirst-ai:${AiImageTag}
      MemorySize: 3008
      Timeout: 60
      Environment:
        Variables:
          MODELS_BUCKET: !Sub askfirst-${Stage}-models-${AWS::AccountId}
          BEDROCK_REGION: ap-southeast-2
          MODEL_ID: !Ref BedrockModelId
          GUARDRAIL_ID: !Ref GuardrailId
          GUARDRAIL_VERSION: !Ref GuardrailVersion
          TOKEN_PARAM: /askfirst/dev/telegram/bot-token
      Policies:
        - S3ReadPolicy: { BucketName: !Sub askfirst-${Stage}-models-${AWS::AccountId} }
        - DynamoDBCrudPolicy: { TableName: !Ref MainTable }
        - SSMParameterReadPolicy: { ParameterName: askfirst/dev/* }
        - Statement:
            - Effect: Allow
              Action: bedrock:InvokeModel
              Resource: !Sub arn:aws:bedrock:ap-southeast-2::foundation-model/${BedrockModelId}
            - Effect: Allow
              Action: bedrock:ApplyGuardrail
              Resource: !Sub arn:aws:bedrock:ap-southeast-2:${AWS::AccountId}:guardrail/${GuardrailId}
```
{{% notice note %}}
Nếu mô hình chỉ truy cập được qua cross-Region inference profile, quyền invoke cũng phải bao gồm profile đó và các foundation model phía sau. Hãy xem tài liệu Bedrock của mô hình bạn chọn.
{{% /notice %}}

Bộ nhớ trên 3.008 MB có thể bị quota chặn ở tài khoản mới; hãy giảm giá trị hoặc xin tăng quota nếu triển khai thất bại.
