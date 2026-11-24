---
title : "Amazon Bedrock và Guardrails ở Sydney"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.6.2 </b> "
---
#### 1. Kiểm tra mô hình có sẵn (Sydney)
1. Chuyển Region của console sang **Asia Pacific (Sydney) ap-southeast-2**.
2. Mở **Amazon Bedrock → Model catalog** và xác nhận mô hình bạn chọn (Qwen3 32B trong kiến trúc) khả dụng cho tài khoản.
3. Ghi lại **model ID** và giá on-demand để đưa vào ngân sách.

#### 2. Tạo guardrail
1. Ở Region Sydney, mở **Amazon Bedrock → Guardrails → Create guardrail**.
2. Đặt tên `askfirst-guardrail` và viết thông báo bị chặn thân thiện bằng tiếng Việt.
3. Bật content filter ở mức hợp lý và thêm một denied topic cho các yêu cầu cố khiến trợ lý giúp *thực hiện* lừa đảo.
4. Tạo một **version** và ghi lại **Guardrail ID** cùng **số version**.
5. Triển khai lại stack với tham số mới:
```
sam deploy --parameter-overrides Stage=dev GuardrailId=<ID> GuardrailVersion=1
```
{{% notice note %}}
Dữ liệu cá nhân đã được che bên trong Lambda AI *trước khi* bất kỳ lời gọi nào rời khỏi nó. Guardrail bổ sung lớp thứ hai cho cả đầu vào và đầu ra.
{{% /notice %}}

#### 3. Mẫu gọi trong Lambda AI (phác thảo)
```python
import os, boto3
bedrock = boto3.client("bedrock-runtime", region_name=os.environ["BEDROCK_REGION"])

def guard(text, source):                       # source: "INPUT" hoặc "OUTPUT"
    r = bedrock.apply_guardrail(
        guardrailIdentifier=os.environ["GUARDRAIL_ID"],
        guardrailVersion=os.environ["GUARDRAIL_VERSION"],
        source=source,
        content=[{"text": {"text": text}}])
    return r["action"] != "GUARDRAIL_INTERVENED"

def evaluate(masked_text, evidence):
    if not guard(masked_text, "INPUT"):
        return {"risk": "UNKNOWN", "reason": "blocked_by_guardrail"}
    resp = bedrock.converse(
        modelId=os.environ["MODEL_ID"],
        system=[{"text": SYSTEM_PROMPT}],
        messages=[{"role": "user", "content": [{"text": build_prompt(masked_text, evidence)}]}],
        inferenceConfig={"maxTokens": 500, "temperature": 0.1})
    blocks = resp["output"]["message"]["content"]
    text = next(b["text"] for b in blocks if "text" in b)   # bỏ qua các khối suy luận nếu có
    return parse_json(text) if guard(text, "OUTPUT") else {"risk": "UNKNOWN"}
```
+ Yêu cầu mô hình **chỉ trả JSON** (mức rủi ro, lý do, lời khuyên) để worker định dạng ổn định.
+ Dùng **temperature thấp** để kết luận nhất quán, và đặt **timeout và retry** cho client để xử lý độ trễ liên Region và throttling.
+ Coi kết luận là **lời khuyên**: luôn nhắc người dùng xác nhận với người thân hoặc ngân hàng.

#### 4. Kiểm thử lời gọi
Gọi trực tiếp function AI với payload mẫu theo hợp đồng của dự án:
```
aws lambda invoke --function-name askfirst-dev-ai \
  --payload fileb://contracts/examples/analyze-request.json --cli-binary-format raw-in-base64-out out.json
cat out.json
```
