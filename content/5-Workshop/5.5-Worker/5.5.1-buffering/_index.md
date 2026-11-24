---
title : "Message buffering with a 3-second window"
date : 2026-10-19
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---
People often send several short messages in a row (a sentence, then a link, then a screenshot). Analyzing each one separately wastes money and loses context.

#### The design
1. The webhook appends each message to the user's buffer: `BUFFER#<chat_id>` / `MSG#<seq>`.
2. `seq` comes from an atomic counter on `BUFFER#<chat_id>` / `META`.
3. Only the **first** message of a window queues an `AnalyzeJob`, with a **3-second delivery delay**. Later messages in the same window just add `MSG#` items.
4. When the worker receives the job, it reads all `MSG#` items in order, deletes the buffer, and sends **one** request to the AI Lambda.

#### Webhook side (sketch)
```java
// 1) increment the counter and learn whether a window was already open
UpdateItemResponse r = ddb.updateItem(UpdateItemRequest.builder()
    .tableName(table)
    .key(Map.of("PK", s("BUFFER#" + chatId), "SK", s("META")))
    .updateExpression("ADD seq :one SET ttl = :ttl, windowOpen = if_not_exists(windowOpen, :now)")
    .expressionAttributeValues(Map.of(":one", n(1), ":ttl", n(now + 600), ":now", n(now)))
    .returnValues(ReturnValue.ALL_NEW)
    .build());
long seq = Long.parseLong(r.attributes().get("seq").n());

// 2) store the message
ddb.putItem(msgItem(chatId, seq, text, fileRef));

// 3) first message of the window? -> queue the job with a 3 s delay
if (seq == 1) {
    sqs.sendMessage(b -> b.queueUrl(queueUrl)
        .delaySeconds(3)
        .messageBody(json(Map.of("jobType", "ANALYZE", "chatId", chatId))));
}
```
{{% notice note %}}
SQS delivery delay allows 0–900 seconds, so a 3-second delay is fine. Messages that arrive after the worker has deleted the buffer start a **new** window (`seq` restarts at 1), so nothing is lost.
{{% /notice %}}

#### Worker side (sketch)
```java
List<Msg> msgs = bufferRepo.readAllInOrder(chatId);   // Query PK=BUFFER#id, SK begins_with MSG#
bufferRepo.deleteAll(chatId);                         // after a successful read
String grouped = Msg.join(msgs);
```
Delete the buffer only after you have the data. If the worker fails before calling the AI, SQS redelivers the job and the idempotency key (next page) prevents duplicate replies.
