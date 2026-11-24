---
title : "URL intelligence and caching"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.6.3 </b> "
---
Links are one of the strongest signals in scam messages. The AI Lambda adds two kinds of evidence (step 12) and caches them (step 9).

#### 1. Evidence sources
| Source | What it tells you |
| --- | --- |
| **RDAP** (domain registration data) | How recently the domain was registered, and by which registrar. Very new domains are a warning sign |
| **Web-safety lookup** (for example Google Safe Browsing) | Whether the URL is flagged for phishing or malware |

{{% notice warning %}}
Check the **terms of service** of any safety-lookup API for your use case (some are limited to non-commercial use), and store its API key in SSM Parameter Store as a SecureString, never in code.
{{% /notice %}}

#### 2. Cache key and TTL
```python
import hashlib, time
def cache_key(domain):
    return "URL#" + hashlib.sha256(domain.lower().encode()).hexdigest()

def get_cached(table, domain):
    r = table.get_item(Key={"PK": cache_key(domain), "SK": "CACHE"})
    return r.get("Item")

def put_cached(table, domain, evidence, ttl_seconds=86400):
    table.put_item(Item={"PK": cache_key(domain), "SK": "CACHE",
                         "evidence": evidence, "ttl": int(time.time()) + ttl_seconds})
```
+ Cache by **domain** so different links on the same site share one lookup.
+ A 24-hour TTL balances freshness against cost; DynamoDB removes expired items automatically.

#### 3. Fail soft
```python
def gather_evidence(domain):
    evidence = {}
    for name, fn in (("rdap", lookup_rdap), ("safety", lookup_safety)):
        try:
            evidence[name] = fn(domain, timeout=3)
        except Exception as e:           # network error, rate limit, bad response
            evidence[name] = {"status": "unavailable"}
    return evidence
```
An unavailable source must **not** stop the pipeline; the model is told which evidence is missing.

#### 4. Test
1. Send the same link twice. The second check should be faster and show a cache hit in the AI Lambda logs.
2. Temporarily block a lookup (for example with a wrong API key in a test environment) and confirm the bot still replies using the remaining evidence.
