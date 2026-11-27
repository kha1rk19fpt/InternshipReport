---
title: "Chatbot with Amazon Bedrock"
date: 2026-11-20
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---
#### Goals and constraints

The chatbot helps users read the forecasts in Vietnamese: questions about gold, silver, Brent and WTI at 1–7 session horizons, about concepts (the 80% range, the probability of a rise) and about related news. The chatbot **does not forecast on its own**: every number comes from a published record, so it matches the forecast page. The chatbot has three constraints:

1. **No investment advice.** It never recommends buying, selling, holding or allocating capital.
2. **Every number has a source.** No numbers outside tool results, no self-computed differences or conversions.
3. **Fit within API Gateway:** API Gateway cuts requests at 29 seconds, so the answer must finish before that.

#### The `POST /chat` flow

1. API Gateway checks the ID token with the Cognito authorizer. The user is identified by the `sub` claim, not by email.
2. Lambda `cpaws-dev-chat-api` (Python 3.12, 512 MB, 28-second timeout) validates the request: a question of 1–500 characters and at most 6 turns of history sent by the client. The server does not store conversations.
3. **Deadline check.** The Lambda computes a deadline from the time API Gateway received the request (29 seconds minus 4 seconds of margin). If there is not enough time left for one model call, it returns `504` before counting the question.
4. **Count the question** with one conditional DynamoDB write to table `chat-usage`. The limits are 20 questions per user per day and 100 per day for the whole system; beyond that it returns `429`. The counters reset at 00:00 Vietnam time, and the web app shows the remaining questions below the input box. The table only stores counters, never questions.
5. **Call Bedrock Converse** with system prompt `chatbot-haiku-v2`, two tools, and at most 3 tool rounds. Since `v1.0.1`, the Lambda also sends a second `system` block with the current date and time in Vietnam time (UTC+7).
6. **Return the result:**
   - the answer as plain text;
   - `citations` (the news items cited);
   - `forecasts_used` (asset, base date, model version, sample-data flag);
   - the remaining question quota and the disclaimer.
7. **Write one JSON log line:** branch, tools called, token counts, latency. The question text is not logged.

#### Model and invocation

| Item | Configuration |
|---|---|
| Model | Claude Haiku 4.5 through the **global** inference profile (cross-Region inference), called from `ap-southeast-1` |
| API | Converse (no streaming), `temperature` 0, `maxTokens` 1500, `toolChoice: auto` |
| IAM permissions | `bedrock:InvokeModel` for Claude Haiku 4.5 and Nova 2 Lite (Nova is granted for the router but not used yet), three statements per model following the global cross-Region inference guide: the inference profile, the in-Region model and the global model, the last two conditioned on `bedrock:InferenceProfileArn`; `dynamodb:Query` on the `forecasts` and `news-labels` tables; `dynamodb:UpdateItem` and `dynamodb:GetItem` on the `chat-usage` table (question counter) |
| Not used | Knowledge Bases, embeddings, fine-tuning. News is read directly from labelled data |

With the global inference profile, Bedrock may process a request in another region. ADR-0004 states that the team does not guarantee every request is processed entirely in Singapore.

#### The chatbot knows today's date (v1.0.1)

Up to `v1.0.0`, the model did not know the current date. In a test on 23/11/2026, "from 9/11 to 20/11 this year" was read as 2024, and "today" was confused with the forecast base date. Version `v1.0.1` (PR #146) adds a second `system` block, built when the request arrives, with the weekday, date and time in UTC+7. The text of prompt `chatbot-haiku-v2` is unchanged because it was locked after the benchmark. The change came after the benchmark, so it was checked only by unit tests and by asking on Production on 24/11/2026: "Hiện tại đang là năm bao nhiêu?" (what year is it now?) was answered correctly as "Tuesday, 24/11/2026", and the question about 9/11–20/11 found the right 2026 news.

#### Router: a controlled negative result

The original design (ADR-0004) split questions into two branches. Simple questions (greetings, concepts) would go to **Amazon Nova 2 Lite** to save cost. Questions needing data would go to **Claude Haiku 4.5** with tools. The router chooses a branch by rules, without calling an LLM. It was tested as follows:

- 9 rules were **locked before** measuring.
- A held-out set of 30 questions was written by someone else; the rule author did not see it beforehand.
- Thresholds fixed in advance: accuracy ≥ 90%, and at most 1 question that needs Haiku wrongly sent to Nova.
- It was run exactly once.

Result: accuracy **26/30 (86.7%), below threshold**. No question needing Haiku went to Nova; all 4 errors were simple questions sent to Haiku, which only costs more. The team decided **not to put the router into `/chat`: every question goes to Haiku**, and not to tweak the rules and re-score on a set already seen. Cost is about 0.012 USD per question. With the cap of 100 questions per day, the chatbot costs at most about 1.2 USD per day.

#### Tools v2

| Tool | Source | Returns |
|---|---|---|
| `get_forecast(asset, horizon?)` | DynamoDB `cpaws-dev-forecasts`, `Query` with `Limit=1`. Formatted by the same function as the `forecasts-api` Lambda | q10, q50, q90 in %, probability of a rise, width of the 80% range, base date, `model_version`, sample-data flag. No price levels for gold/silver |
| `get_news(asset, from_date?, to_date?, min_impact?)` | Fed and EIA releases labelled by hand | Up to 10 items, each with title, team-written summary, link, source, date, impact 0–3 and direction |

Each simplification in tools v2 fixes an error seen in an earlier benchmark. For example, the probability of a rise is returned ready in % because the model once multiplied by 100 incorrectly; only q10/q90 are returned because the model once called the q05/q95 pair "the 80% range".

#### Calling Converse with tool use

```python
import boto3

bedrock = boto3.client("bedrock-runtime", region_name="ap-southeast-1")

TOOLS = [{"toolSpec": {
    "name": "get_forecast",
    "description": "Latest forecast (q10, q50, q90, probability of a rise) for one asset",
    "inputSchema": {"json": {
        "type": "object",
        "properties": {
            "asset": {"type": "string", "enum": ["gold", "silver", "brent", "wti"]},
            "horizon": {"type": "integer", "minimum": 1, "maximum": 7},
        },
        "required": ["asset"],
    }},
}}]  # get_news is declared the same way

def ask(question: str, model_id: str, system_prompt: str, run_tool) -> str:
    messages = [{"role": "user", "content": [{"text": question}]}]
    for _ in range(3):  # at most 3 tool rounds
        resp = bedrock.converse(
            modelId=model_id, system=[{"text": system_prompt}], messages=messages,
            toolConfig={"tools": TOOLS},
            inferenceConfig={"maxTokens": 1500, "temperature": 0},
        )
        msg = resp["output"]["message"]
        messages.append(msg)
        if resp["stopReason"] != "tool_use":
            return "".join(b.get("text", "") for b in msg["content"])
        # run every tool the model asked for and send the results back as toolResult
        results = [{"toolResult": {"toolUseId": b["toolUse"]["toolUseId"],
                                   "content": [{"json": run_tool(b["toolUse"]["name"], b["toolUse"]["input"])}]}}
                   for b in msg["content"] if "toolUse" in b]
        messages.append({"role": "user", "content": results})
    return "Mình chưa trả lời được câu này, bạn thử hỏi ngắn gọn hơn nhé."
```

#### Server-side safeguards

The model can ignore its prompt, so some rules are enforced again in code:

- `citations` only include news returned by `get_news` **in this turn** whose link appears in the answer. The web app only builds links from `citations` and never turns text in the answer into links.
- An answer containing a link that does not belong to a tool result is logged as `citation_link_mismatch` for review.
- Answers are displayed as plain text: no Markdown or HTML rendering.
- The disclaimer and `forecasts_used` are always in the response, even if the model forgets.
- Responses and error messages never contain the Account ID, ARNs, table names, model ID or system prompt.
- News content is **untrusted data**: the prompt tells the model to ignore any instruction found inside news.

The team **did not enable Bedrock Guardrails** within the project scope (decision of 11/11/2026). Investment-advice requests and prompt attacks are handled by prompt rules, the checks above and a benchmark that includes policy questions. Guardrails are listed as an improvement after the project.

#### Evaluation

- The benchmark calls the real Bedrock, with tools running on fixed data.
- Before a run, the questions, prompt, data and tool code are **locked with SHA-256**.
- The official run is done **once**; the prompt is never edited and re-scored on the same set.
- Prompt v4 failed its official run on 11/11/2026, so the Lambda keeps prompt v2. The whole benchmark run cost about 0.73 USD.

#### The chatbot on the web

![Forecast question](/images/5-Workshop/5.5-chat-forecast-news1.png)

*Asking "Dự báo vàng 7 ngày tới thế nào?" (gold forecast for the next 7 days): the chatbot gives the 80% interval (−5.59% to 5.45%), median −0.18% and probability of a rise 48%, and states the GLD proxy series and the 252-session baseline. Each answer has a lookup code for tracing the log.*

![News question](/images/5-Workshop/5.5-chat-forecast-news2.png)

*Asking "Có tin gì mới của Fed không?" (any new Fed news?): the chatbot replied that it found no Fed news in the 7 days 17/11–24/11/2026, although the labelled set contains the FOMC minutes published on 18/11 (impact 2). This is a missed result, listed under "Known limitations".*

![Refusing advice](/images/5-Workshop/5.5-chat-refuse-advice.png)

*Asking "có nên mua vàng không?" (should I buy gold?): the chatbot declines to give investment advice and only repeats the forecast figures. Below the input box are the remaining questions (13/20) and the reset time.*

#### Known limitations

The issues below were found when the team tried the chatbot on Production after `v1.0.1`. After the feature freeze on 22/11/2026 the team changes neither prompt nor code; the issues are recorded here and in `docs/defense/chatbot.md` (section 8), and the fixes are collected in issue #84 for after the project.

- **Cannot answer questions about past moves**, such as "why did gold fall from 9/11 to 20/11". The chatbot has no price-history tool, so it talks about the coming sessions instead and sometimes cites a Fed release as if it caused the drop.
- **Naming a trend when the probability of a rise is near 50%:** at 47–51% the chatbot sometimes writes "downtrend", when it should say the direction is unclear. The web app only shows a direction label at 60% and above or 40% and below.
- **News dates off by one day:** the answer uses the UTC date of the release (for example, FOMC minutes published on 18/11), while the Source card on the web converts to Vietnam time (19/11).
- **Reading the interval:** some answers call q10/q90 the "maximum fall/maximum rise", when they are only the bounds of the 80% interval.
- **Adding generic commentary** that is not in the news, such as "high interest rates make gold less attractive because the opportunity cost rises".
- **Missing news that exists:** a question that names no asset, such as "Có tin gì mới của Fed không?" (any new Fed news?), sometimes gets a "found nothing" answer, although the FOMC minutes of 18/11 fall inside the 7-day window and have impact 2. When the asset and date range are given, the chatbot returned this release correctly (tested on 23–24/11).
