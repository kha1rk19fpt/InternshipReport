---
title: "Daily Flow"
date: 2026-11-20
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---
#### Schedule

EventBridge Scheduler runs `cron(15 12 ? * TUE-SAT *)` (UTC), i.e. early morning New York time after each Monday–Friday trading session.

#### Chaining Ingest and Predict with Lambda Destinations

```python
from aws_cdk import aws_iam as iam, aws_lambda_destinations as destinations, aws_scheduler as scheduler

# whether ingest succeeds or fails, predict runs; predict skips assets with missing data
ingest_fn.configure_async_invoke(
    on_success=destinations.LambdaDestination(predict_fn),
    on_failure=destinations.LambdaDestination(predict_fn),
)
predict_fn.configure_async_invoke(retry_attempts=0)

role = iam.Role(self, "SchedulerRole", assumed_by=iam.ServicePrincipal("scheduler.amazonaws.com"))
ingest_fn.grant_invoke(role)

scheduler.CfnSchedule(
    self, "DailyIngest",
    schedule_expression="cron(15 12 ? * TUE-SAT *)",
    flexible_time_window=scheduler.CfnSchedule.FlexibleTimeWindowProperty(mode="OFF"),
    target=scheduler.CfnSchedule.TargetProperty(arn=ingest_fn.function_arn, role_arn=role.role_arn),
)
```

#### Failure Behavior

- One failing data source: the remaining assets are still forecast
- Latest session missing: predict **does not fall back to an older date**, it skips that asset
- A CloudWatch Alarm fires on Lambda errors

#### Verify

```bash
aws dynamodb query --profile cpaws-dev --table-name cpaws-dev-forecasts \
  --key-condition-expression "asset = :a" \
  --expression-attribute-values '{":a": {"S": "gold"}}' \
  --no-scan-index-forward --limit 1
```

**Screenshots:** ...
