---
title: "Chatbot với Amazon Bedrock"
date: 2026-11-20
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---
#### Thiết kế

- **Model:** Claude Haiku 4.5 cho câu phức tạp hoặc cần tool; Amazon Nova cho câu đơn giản; định tuyến theo luật, không chắc thì chuyển Haiku
- **Gọi model:** Converse API với `max_tokens` theo loại câu
- **Tool:** `get_forecast` (đọc DynamoDB, `Limit=1`), `get_news` (tin đã gắn nhãn từ thông cáo Fed, EIA)
- **Quy tắc:** mọi con số lấy từ tool, lý do có trích nguồn, không tư vấn đầu tư

#### Khai báo tool và gọi Converse

```python
import os
import boto3

bedrock = boto3.client("bedrock-runtime", region_name="ap-southeast-1")
MODEL_ID = os.environ["CHAT_MODEL_ID"]  # inference profile global của Claude Haiku 4.5

TOOLS = [{"toolSpec": {
    "name": "get_forecast",
    "description": "Lấy dự đoán mới nhất (q10, q50, q90, xác suất tăng) cho một tài sản",
    "inputSchema": {"json": {
        "type": "object",
        "properties": {"asset": {"type": "string", "enum": ["gold", "silver", "brent", "wti"]}},
        "required": ["asset"],
    }},
}}]

resp = bedrock.converse(
    modelId=MODEL_ID,
    system=[{"text": "Chỉ dùng số liệu từ tool. Không tư vấn đầu tư."}],
    messages=[{"role": "user", "content": [{"text": "Giá vàng tuần tới thế nào?"}]}],
    toolConfig={"tools": TOOLS},
    inferenceConfig={"maxTokens": 1500},
)
```

Khi `stopReason` là `tool_use`, Lambda chạy tool, gửi kết quả lại bằng một message `toolResult`, rồi gọi Converse lần nữa để model viết câu trả lời.

#### Kiểm thử

- Bộ câu hỏi cố định gồm câu trong phạm vi và ngoài phạm vi
- Chấm: con số khớp database, có trích nguồn, từ chối đúng câu ngoài phạm vi
- Chỉ chạy chấm chính thức **một lần** trên bộ đã chốt; không sửa prompt rồi chấm lại trên cùng bộ

**Ảnh minh họa:** ...
