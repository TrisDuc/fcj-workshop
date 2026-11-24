---
title : "Telegram webhook Lambda"
date : 2026-10-19
weight : 4
chapter : false
pre : " <b> 5.4. </b> "
---
The webhook Lambda is the public entry point. It must answer Telegram **quickly** and do as little as possible: verify, de-duplicate, guard, buffer, enqueue.

#### Content
- [Create the bot and store secrets](5.4.1-bot-and-secrets/)
- [Deploy the webhook function](5.4.2-deploy-webhook/)
- [Register the webhook and test](5.4.3-register-and-test/)
