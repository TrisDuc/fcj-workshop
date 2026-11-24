---
title : "Amazon Bedrock and Guardrails in Sydney"
date : 2026-10-19
weight : 2
chapter : false
pre : " <b> 5.6.2 </b> "
---
#### 1. Check model availability (Sydney)
1. Switch the console Region to **Asia Pacific (Sydney) ap-southeast-2**.
2. Open **Amazon Bedrock → Model catalog** and confirm your chosen model (Qwen3 32B in the architecture) is available to your account.
3. Note its **model ID** and on-demand price for the budget.

#### 2. Create the guardrail
1. In the Sydney Region, open **Amazon Bedrock → Guardrails → Create guardrail**.
2. Name it `askfirst-guardrail` and write friendly blocked-message texts in Vietnamese.
3. Enable content filters at a sensible strength and add a denied topic for requests that try to make the assistant help *commit* fraud.
4. Create a **version** and note the **Guardrail ID** and **version number**.
5. Redeploy the stack with the new parameters:
```
sam deploy --parameter-overrides Stage=dev GuardrailId=<ID> GuardrailVersion=1
```
{{% notice note %}}
Personal data is already masked inside the AI Lambda *before* any call leaves it. The guardrail adds a second layer on both input and output.
{{% /notice %}}

#### 3. Call pattern in the AI Lambda (sketch)
```python
import os, boto3
bedrock = boto3.client("bedrock-runtime", region_name=os.environ["BEDROCK_REGION"])

def guard(text, source):                       # source: "INPUT" or "OUTPUT"
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
    text = next(b["text"] for b in blocks if "text" in b)   # skip any reasoning blocks
    return parse_json(text) if guard(text, "OUTPUT") else {"risk": "UNKNOWN"}
```
+ Ask the model for **JSON only** (risk level, reasons, advice) so the worker can format it reliably.
+ Use a **low temperature** for consistent verdicts, and set client **timeouts and retries** to handle cross-Region latency and throttling.
+ Treat the verdict as **advice**: always remind the user to confirm with family or the bank.

#### 4. Test the call
Invoke the AI function directly with a sample payload that follows the project contract:
```
aws lambda invoke --function-name askfirst-dev-ai \
  --payload fileb://contracts/examples/analyze-request.json --cli-binary-format raw-in-base64-out out.json
cat out.json
```
