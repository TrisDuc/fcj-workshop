---
title : "Models in S3 and the container image in ECR"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.6.1 </b> "
---
#### 1. Create the bucket for models and the knowledge-base index
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

#### 2. Upload the artifacts
```
aws s3 cp models/ s3://$BUCKET/models/ --recursive     # OCR PP-OCRv5 (ONNX), e5-small int8 embeddings
aws s3 cp kb-index/ s3://$BUCKET/kb-index/ --recursive # scam knowledge-base index
aws s3 ls s3://$BUCKET/ --recursive --human-readable
```
At cold start the AI Lambda downloads these into **memory** (step 8 in the architecture) and reuses them for later invocations in the same execution environment.

#### 3. Create the ECR repository and push the image
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
Use a Lambda-compatible base image (for example the AWS Python base image) so the runtime interface client is included.

#### 4. Add the function to `template.yaml`
```yaml
Parameters:
  AiImageTag:
    Type: String
    Default: v1
  BedrockModelId:
    Type: String
    Default: qwen.qwen3-32b-v1:0          # verify the ID in the Bedrock console
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
If the model is only reachable through a cross-Region inference profile, the invoke permission must also cover that profile and the foundation models behind it. Check the Bedrock documentation for the model you chose.
{{% /notice %}}

Memory above 3,008 MB may be blocked on new accounts by a quota; lower the value or request a quota increase if deployment fails.
