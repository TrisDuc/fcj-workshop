---
title : "Kiểm thử end-to-end"
date : 2026-10-19
weight : 8
chapter : false
pre : " <b> 5.8. </b> "
---
Chạy từng kịch bản qua Telegram, rồi kiểm tra Telegram, DynamoDB và CloudWatch Logs.

| # | Kịch bản | Kết quả mong đợi |
| --- | --- | --- |
| 1 | `/start` | Chào mừng và luồng đồng ý hiện ngay (đường nhanh) |
| 2 | Văn bản giống lừa đảo (trúng thưởng gấp, "chuyển phí để nhận") | Rủi ro cao, lý do dễ hiểu, khuyên không chuyển tiền |
| 3 | Tin nhắn bình thường từ gia đình ("tối 7 giờ ăn cơm nhé?") | Rủi ro thấp |
| 4 | Ảnh chụp màn hình tin nhắn lừa đảo | Dùng văn bản OCR; mức rủi ro phản ánh nội dung ảnh |
| 5 | Tin nhắn có link mới hoặc bị gắn cờ | Hiện bằng chứng về link; lần kiểm tra thứ hai trúng cache |
| 6 | Ba tin ngắn trong vòng 3 giây | Một lần phân tích gom và một phản hồi |
| 7 | File trên 20 MB, voice trên 120 giây | Từ chối thân thiện; **không** có gì được xếp hàng |
| 8 | Cùng một update gửi hai lần | Request thứ hai được xác nhận, không có phản hồi trùng |
| 9 | Kết quả rủi ro cao với sự đồng ý cảnh báo người thân | Người thân chỉ nhận bản tóm tắt ngắn |
| 10 | 15 phút sau một kết luận rủi ro | Câu hỏi hỏi lại đến |
| 11 | Cố ý làm function AI lỗi | Thử lại tối đa 5 lần, sau đó vào DLQ, email alarm |

#### Phát lại message từ DLQ sau khi sửa lỗi
```
DLQ_ARN=$(aws cloudformation describe-stacks --stack-name askfirst-dev \
  --query "Stacks[0].Outputs[?OutputKey=='JobsDlqArn'].OutputValue" --output text)
aws sqs start-message-move-task --source-arn $DLQ_ARN
aws sqs list-message-move-tasks --source-arn $DLQ_ARN
```

#### Đo lường
+ **Độ trễ:** thời gian từ lúc gửi tin đến lúc nhận kết luận; ghi lại lần gọi đầu sau khi nghỉ (cold start).
+ **Chi phí mỗi lần kiểm tra:** thời gian chạy Lambda AI × bộ nhớ, token Bedrock và các khoản khác, rồi so với ước tính trong proposal.
+ **Chất lượng:** giữ một bảng nhỏ gồm tin nhắn thử, rủi ro kỳ vọng và rủi ro thực tế; liệt kê các kết luận sai và điều bạn đã thay đổi.

Lưu ảnh chụp màn hình (đã che bí mật) cho báo cáo.
