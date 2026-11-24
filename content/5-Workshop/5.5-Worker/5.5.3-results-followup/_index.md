---
title : "Results, family alerts, and the 15-minute follow-up"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.5.3 </b> "
---
#### 1. Send the verdict
The AI Lambda returns a structured result (risk level, reasons, short advice). The worker:
1. Formats it in **simple, calm Vietnamese** with a clear next step ("Do not transfer yet; call your family or the bank using the number on your card").
2. Sends it with the Telegram `sendMessage` method using the token from SSM.
3. Stores a `CHECK#<timestamp>#<id>` item under the user.

#### 2. Alert family members (only with consent)
```java
if (result.risk() == Risk.HIGH && profile.familyAlertConsent()) {
    for (FamilyContact c : profile.familyContacts()) {
        telegram.sendMessage(c.chatId(), alertText(profile.displayName(), result.summary()));
        alertRepo.record(profile.chatId(), c.chatId(), now);
    }
}
```
+ Share a **short summary only**, never the original message text or masked personal data.
+ Record each alert in `ALERT#<timestamp>` for traceability.

#### 3. Schedule the follow-up (step 17)
```java
sqs.sendMessage(b -> b.queueUrl(queueUrl)
    .delaySeconds(900)                       // 15 minutes, the SQS maximum
    .messageBody(json(Map.of(
        "jobType", "FOLLOWUP",
        "chatId", chatId,
        "jobId", "FU#" + checkId))));
```
+ If the user replies before the follow-up arrives, the worker can skip sending it by checking the check's status first.
+ For delays longer than 15 minutes, use Amazon EventBridge Scheduler (see the Amazon EventBridge Scheduler documentation).

#### 4. Verify
Send a test message; within a few seconds you should receive a reply. In DynamoDB, a `CHECK#...` item appears for your chat. Fifteen minutes later, the follow-up question arrives.
