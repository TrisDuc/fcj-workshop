---
title : "AI Lambda with Amazon Bedrock"
date : 2026-10-19
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---
The AI Lambda is a **Python container image**. It receives grouped messages from the worker and returns a structured risk result. The pipeline is: **extract → mask personal data → retrieve (RAG) → evaluate**.

{{% notice note %}}
The AI code and the JSON contract between worker and AI live in the project repository (`ai/` and `contracts/`). This workshop shows how to package, configure, and connect them; it does not redefine the contract.
{{% /notice %}}

#### Content
- [Models in S3 and the container image in ECR](5.6.1-s3-ecr/)
- [Amazon Bedrock and Guardrails in Sydney](5.6.2-bedrock-guardrails/)
- [URL intelligence and caching](5.6.3-url-intel-cache/)
