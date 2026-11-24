---
title : "Tra cứu URL và cache"
date : 2026-10-19
weight : 3
chapter : false
pre : " <b> 5.6.3 </b> "
---
Đường link là một trong những tín hiệu mạnh nhất trong tin nhắn lừa đảo. Lambda AI bổ sung hai loại bằng chứng (bước 12) và cache chúng (bước 9).

#### 1. Nguồn bằng chứng
| Nguồn | Cho biết điều gì |
| --- | --- |
| **RDAP** (dữ liệu đăng ký tên miền) | Tên miền mới được đăng ký gần đây hay không và qua nhà đăng ký nào. Tên miền rất mới là dấu hiệu cảnh báo |
| **Tra cứu an toàn web** (ví dụ Google Safe Browsing) | URL có bị gắn cờ lừa đảo hoặc mã độc hay không |

{{% notice warning %}}
Hãy kiểm tra **điều khoản dịch vụ** của mọi API tra cứu an toàn cho trường hợp sử dụng của bạn (một số chỉ cho phép phi thương mại), và lưu API key trong SSM Parameter Store dạng SecureString, không bao giờ để trong mã.
{{% /notice %}}

#### 2. Khóa cache và TTL
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
+ Cache theo **tên miền** để các link khác nhau trên cùng một trang dùng chung một lần tra cứu.
+ TTL 24 giờ cân bằng giữa độ mới và chi phí; DynamoDB tự xóa mục hết hạn.

#### 3. Lỗi thì giảm cấp êm
```python
def gather_evidence(domain):
    evidence = {}
    for name, fn in (("rdap", lookup_rdap), ("safety", lookup_safety)):
        try:
            evidence[name] = fn(domain, timeout=3)
        except Exception as e:           # lỗi mạng, giới hạn tốc độ, phản hồi sai
            evidence[name] = {"status": "unavailable"}
    return evidence
```
Một nguồn không khả dụng **không** được làm dừng pipeline; mô hình được cho biết bằng chứng nào đang thiếu.

#### 4. Kiểm thử
1. Gửi cùng một link hai lần. Lần kiểm tra thứ hai nên nhanh hơn và hiện cache hit trong log của Lambda AI.
2. Tạm chặn một nguồn tra cứu (ví dụ dùng API key sai trong môi trường thử) và xác nhận bot vẫn trả lời bằng các bằng chứng còn lại.
