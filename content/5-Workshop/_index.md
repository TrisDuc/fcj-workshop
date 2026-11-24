---
title : "Workshop"
date : 2026-10-19
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
{{% notice info %}}
**Draft note:** This workshop is written ahead of the build, from the target architecture. Resource names, versions, and console labels may differ slightly in your account — adjust them as you go, and replace the planned steps with what you actually ran and observed.
{{% /notice %}}

# Build AskFirst: a Serverless Anti-Scam Telegram Assistant on AWS

#### Overview

**AskFirst** ("Ask Before You Transfer") lets an older adult forward a suspicious message, screenshot, or link to a Telegram bot and receive a plain-language risk verdict before sending money.

In this workshop you will build the system step by step:

+ A **webhook Lambda (Java 21)** that authenticates Telegram, removes duplicate updates, rejects oversized media, and queues work.
+ An **Amazon SQS queue with a dead-letter queue** that retries failed jobs up to 5 times.
+ A **worker Lambda (Java 21)** that groups a user's messages in a 3-second window, calls the AI Lambda, replies, alerts family, and schedules a 15-minute follow-up.
+ An **AI Lambda (Python container)** that runs OCR, masks personal data, retrieves scam knowledge, and evaluates risk with **Amazon Bedrock** and **Bedrock Guardrails**.
+ **Security and operations**: least-privilege IAM, SSM Parameter Store, KMS, keyless GitHub Actions deployment, CloudWatch alarms, SNS email, CloudTrail, and Budgets.

#### Content

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Foundation: DynamoDB and SQS](5.3-Foundation/)
4. [Telegram webhook Lambda](5.4-Webhook/)
5. [Worker Lambda](5.5-Worker/)
6. [AI Lambda with Amazon Bedrock](5.6-AI-Lambda/)
7. [Security and operations](5.7-Security-Ops/)
8. [End-to-end testing](5.8-Testing/)
9. [Clean up](5.9-Cleanup/)
