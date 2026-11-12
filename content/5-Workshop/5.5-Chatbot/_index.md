---
title: "Chatbot with Amazon Bedrock"
date: 2026-11-20
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---
#### Design

- **Models:** Claude Haiku 4.5 for complex or tool-using questions; Amazon Nova for simple ones; rule-based routing that falls back to Haiku when unsure
- **Invocation:** Converse API with `max_tokens` set by question type
- **Tools:** `get_forecast` (reads DynamoDB, `Limit=1`), `get_news` (labeled Fed and EIA releases)
- **Rules:** every number comes from a tool, every reason has a citation, no investment advice

#### Declaring a Tool and Calling Converse

```python
import os
import boto3

bedrock = boto3.client("bedrock-runtime", region_name="ap-southeast-1")
MODEL_ID = os.environ["CHAT_MODEL_ID"]  # global inference profile for Claude Haiku 4.5

TOOLS = [{"toolSpec": {
    "name": "get_forecast",
    "description": "Get the latest forecast (q10, q50, q90, probability of an increase) for an asset",
    "inputSchema": {"json": {
        "type": "object",
        "properties": {"asset": {"type": "string", "enum": ["gold", "silver", "brent", "wti"]}},
        "required": ["asset"],
    }},
}}]

resp = bedrock.converse(
    modelId=MODEL_ID,
    system=[{"text": "Only use numbers from tools. No investment advice."}],
    messages=[{"role": "user", "content": [{"text": "How will gold prices move next week?"}]}],
    toolConfig={"tools": TOOLS},
    inferenceConfig={"maxTokens": 1500},
)
```

When `stopReason` is `tool_use`, the Lambda runs the tool, sends the result back in a `toolResult` message, and calls Converse again so the model can write the answer.

#### Testing

- A fixed question set with in-scope and out-of-scope questions
- Scoring: numbers match the database, citations are present, out-of-scope questions are refused
- Run the official scoring **only once** on the locked set; never tweak the prompt and re-score on the same set

**Screenshots:** ...
