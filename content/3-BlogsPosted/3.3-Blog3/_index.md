---
title: "Blog 3"
date: 2026-11-16
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Designing a DynamoDB Table: Start from the Questions, Not from the Data

*Trinh Tien Khai · Posted in the AWS Study Group: [LINK TO BE ADDED AFTER POSTING]*

Being used to SQL, when I first learned Amazon DynamoDB I designed tables out of old habit: normalize the data, one number per row, filter for whatever you need. After reading the documentation carefully I realized DynamoDB requires the opposite mindset: you must know what the application will ask before choosing the keys. This post walks through that approach with an example, a diagram that numbers each flow, and a feature I think is well worth knowing: conditional writes.

![DynamoDB: choosing keys from the questions and conditional writes](/images/3-Blog/blog3-dynamodb-key-design.png)

*Figure 1. Example diagram of a weather forecast app reading and writing a DynamoDB table, drawn by me in draw.io based on AWS documentation.*

**Scenario:** a weather app runs a nightly job that computes a 7-day forecast for each city. The web app and API need to show the latest forecast. If the data were stored SQL-style (one row per city, per day, per metric), showing one city's forecast on the home page would mean reading dozens of items or scanning the table. With DynamoDB, that design is both slow and expensive.

## 1. List the questions first

Write down the questions the application will ask:

- "What is the latest forecast for Hanoi?" (home page)
- "What was the forecast created on 2026-08-01 for Da Nang?" (history)
- Does any question need a full table scan? Here, no.

## 2. Choose the keys

- **Partition key:** `city`
- **Sort key:** `forecast_date`, as a `YYYY-MM-DD` string. Sorting this format alphabetically is the same as sorting by time.

The first question becomes a reverse Query that returns exactly 1 item:

```python
# Simplified illustration
import boto3
from boto3.dynamodb.conditions import Key

table = boto3.resource("dynamodb").Table("Forecasts")

def latest(city: str) -> dict | None:
    resp = table.query(
        KeyConditionExpression=Key("city").eq(city),
        ScanIndexForward=False,  # forecast_date descending
        Limit=1,
    )
    items = resp.get("Items", [])
    return items[0] if items else None
```

The second question is a GetItem with both keys. No GSI, no Scan.

## 3. What an item contains

Instead of one number per row, put one whole forecast run for a city into a single item: the next 7 days, each with temperature, rainfall and humidity, plus the model version and creation time. Each read costs a single request, and the application does not have to stitch rows together. Just keep in mind the 400 KB item size limit.

A small detail: with the boto3 resource, numbers must be passed as `Decimal`; passing a `float` raises an error. If you round numbers before writing, remember that the values read back may differ very slightly from those computed in memory, so compare with a tolerance, never with exact equality.

## 4. Following the 7 flows in the diagram

1. The user opens the web app on AWS Amplify Hosting and signs in with Amazon Cognito to get an ID token.
2. The web app calls API Gateway (REST) with the header `Authorization: <ID token>`. The Cognito authorizer checks the token before letting the request through.
3. The api Lambda reads the table with Query `Limit=1` (latest forecast) or GetItem (by date).
4. EventBridge Scheduler runs the forecast job every night.
5. The batch Lambda writes the results with a conditional PutItem.
6. If the job runs again (run by hand, or retried by Lambda), the second write is rejected with `ConditionalCheckFailedException`. The code catches this error and treats it as "already there", not as an incident.
7. An experimental model runs in parallel on the same data but writes to S3, not to the serving table. Users only see the main model's results, while the new model is still scored on real data.

## 5. Conditional writes: never overwrite an existing record

DynamoDB has no "insert" command. PutItem overwrites by default if an item with the same key exists. To make each (`city`, `forecast_date`) pair writable only once, add a `ConditionExpression`:

```python
# Simplified illustration
from botocore.exceptions import ClientError

def put_once(item: dict) -> bool:
    try:
        table.put_item(
            Item=item,
            ConditionExpression="attribute_not_exists(city) AND attribute_not_exists(forecast_date)",
        )
        return True
    except ClientError as e:
        if e.response["Error"]["Code"] == "ConditionalCheckFailedException":
            return False  # a record for this date already exists
        raise
```

Three things I found while reading the documentation:

- The condition is checked on that item and applied atomically with the write, so there is no need to "read first, then write". Reading first and then writing can still be interrupted by another process in between.
- A rejected write still consumes write capacity, depending on the item size.
- The `ReturnValuesOnConditionCheckFailure` parameter makes DynamoDB return the existing item when the condition fails, saving an extra read to see what happened.

## Checklist for designing a DynamoDB table

- Have you listed every question the application will ask? Is each one a Query or a GetItem?
- Does the sort key sort in the order you need (for example dates as `YYYY-MM-DD`)?
- Does any question require a Scan? If so, revisit the keys or add a GSI.
- Could an item exceed 400 KB?
- Is overwriting the behavior you want? If not, use a `ConditionExpression`.
- Do comparisons of numbers read from DynamoDB use a tolerance?

## Conclusion

With DynamoDB, choosing the right keys matters more than anything else. Start from the questions and every read becomes a Query or a GetItem, with no GSI or Scan. Conditional writes are the cheapest and safest way to get "write only once" semantics, which suits batch jobs that may be rerun.

*Note: service prices and limits may change, so check the latest AWS documentation. The code is a simplified illustration.*

### References

- [Condition expressions](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html)
- [DynamoDB read and write operations (capacity on failed conditions)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/read-write-operations.html)
- [PutItem API](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_PutItem.html)
- [Query](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html)
- [Best practices for designing partition keys](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-partition-key-design.html)
- [Handle conditional write errors in high concurrency scenarios](https://aws.amazon.com/blogs/database/handle-conditional-write-errors-in-high-concurrency-scenarios-with-amazon-dynamodb)

`#AWS #AmazonDynamoDB #APIGateway #AmazonCognito #Serverless #NoSQL #AWSStudyGroup #FCAJ`
