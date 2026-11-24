---
title: "Week 11 Worklog"
date: 2026-11-23
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

{{% notice info %}}
This week is the planned work for the AskFirst project. Update dates and results to match what was actually built and tested.
{{% /notice %}}

During Week 11 (23–27 November 2026), i built and connected the Python AI Lambda: models, the pipeline, Amazon Bedrock with Guardrails, URL checks, caching, family alerts, and the follow-up.

### Week 11 Objectives

- Build and package the Python AI Lambda: OCR, embeddings, PII masking, retrieval, and risk evaluation with Amazon Bedrock.
- Connect the full flow, including URL reputation checks, caching, family alerts, and the 15-minute follow-up.

### Daily Worklog

| Day | Work | Start Date | Completion Date | References |
| --- | --- | --- | --- | --- |
| Monday | Package the AI Lambda as a container image in ECR; store model weights and the knowledge-base index in S3. | 23 November 2026 | 23 November 2026 | [Docker](https://000015.awsstudygroup.com/)<br>[Amazon S3](https://000057.awsstudygroup.com/) |
| Tuesday | Implement the pipeline stages: extract text, mask personal data, retrieve similar scam knowledge, then evaluate. | 24 November 2026 | 24 November 2026 | Project docs / Tài liệu dự án |
| Wednesday | Call Amazon Bedrock (Qwen3 32B) and Bedrock Guardrails in Sydney; design the prompt and a structured result. | 25 November 2026 | 25 November 2026 | [Amazon Bedrock docs](https://docs.aws.amazon.com/bedrock/) |
| Thursday | Add URL reputation checks (RDAP, Safe Browsing) and the DynamoDB cache for repeated checks. | 26 November 2026 | 26 November 2026 | Public RDAP / Safe Browsing docs |
| Friday | Return results to Telegram, alert family members, and schedule the 15-minute follow-up. | 27 November 2026 | 27 November 2026 | [SQS and SNS](https://000077.awsstudygroup.com/) |

### Achievements

- Built the container image and pushed it to Amazon ECR.
- Uploaded the OCR model (PP-OCRv5, ONNX) and the multilingual embedding model (e5-small, int8) to S3 and loaded them into memory at cold start.
- Kept uploaded images in memory only, as designed.
- Extracted text from user messages and from images through OCR.
- Masked personal identifiers before any text leaves the AI Lambda.
- Retrieved the most similar entries from the knowledge-base index to ground the evaluation.
- Applied the guardrail to the input and output around the model call.
- Asked the model for a structured verdict (risk level, reasons, advice in simple Vietnamese) so the worker can format it reliably.
- Handled cross-Region latency and throttling with timeouts and retries.
- Looked up domain registration data (RDAP) and browsing-safety status for links found in a message.
- Cached results with a time-to-live so repeated links are answered quickly and cheaply.
- Treated external lookups as optional signals: if one fails, the pipeline continues with the remaining evidence.
- Sent the verdict to the older adult in plain language and stored the result in DynamoDB.
- Sent an alert to linked family members only for high-risk results and only where consent was recorded.
- Re-queued a follow-up job with a 15-minute delay to ask whether the user went ahead with the transfer.

### Lessons Learned

- External lookups should be optional evidence so the pipeline degrades gracefully.
- Masking personal data before any model call is a design rule, not an afterthought.

### Next Steps

Harden, monitor, test end to end, and clean up in Week 12.
