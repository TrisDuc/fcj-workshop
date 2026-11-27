---
title: "Self-evaluation"
date: 2026-11-27
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

During the First Cloud AI Journey internship I was a backend member (BE) of the team project **Ask First**. I worked on the Java 21 side of the scam-check flow: the Telegram webhook handler (BE-22), the media guard that rejects oversized files before any job is created (BE-34), and the worker that groups messages and calls the AI Lambda. I am also working on the family alert (FR6), the 15-minute follow-up (FR9), and BE-29. The table below is my self-evaluation; the evidence comes from my worklog and the project repo.

| No. | Criterion | Good | Fair | Average | Comment / evidence |
|---|---|---|---|---|---|
| 1 | Professional knowledge | ✅ | | | Worked hands-on with Java 21 Lambdas (webhook handler and worker), SQS with a dead-letter queue, DynamoDB single-table design, AWS SAM, and a Docker + LocalStack environment for local runs. |
| 2 | Self-learning | | ✅ | | Learned the AWS services during the eight learning weeks and applied them to the project; some topics, such as idempotent handlers and SQS retries, took extra time to absorb. |
| 3 | Proactiveness | ✅ | | | Took on issues BE-22 (#43) and BE-34 (#72), then FR6 (#24) and FR9 (#27), and kept a handoff note for the worker so the work could be continued and reviewed. |
| 4 | Discipline | | | ✅ | Work followed the issue priorities, but the worklog was not always kept daily, and I need to plan around dependencies earlier (BE-29 needs BE-28 and INT-01 first). |
| 5 | Communication | | ✅ | | Worked mainly through GitHub issues and the shared BE ↔ AI contract; the Java side follows the contract and leaves the AI code and the contracts folder unchanged. I could report blockers to the team earlier. |
| 6 | Teamwork | ✅ | | | BE-29 is a paired task with another backend member, and the agreed contract let the BE and AI sides build in parallel. |
| 7 | Problem solving | | ✅ | | Designed the 3-second message buffer (BUFFER# META + MSG# by sequence), de-duplication of Telegram updates with a conditional write, and early rejection of oversized media so it never reaches the queue. |
| 8 | Contribution to the Workshop | ✅ | | | The webhook, worker, family alert, and follow-up flow I worked on are described step by step in the Workshop. |

### Areas to improve
* **Keep the worklog daily.** Write it as I work instead of catching up later.
* **Plan around dependencies.** Check what an issue needs first (for example BE-28 and INT-01 for BE-29) and raise a blocker early.
* **Test as I build.** Write tests for boundary values and retry behavior alongside the code, not at the end.
