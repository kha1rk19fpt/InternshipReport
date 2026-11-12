---
title: "Luồng chạy hằng ngày"
date: 2026-11-20
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---
#### Lịch chạy

EventBridge Scheduler chạy `cron(15 12 ? * TUE-SAT *)` (UTC), tức sáng sớm giờ New York sau mỗi phiên giao dịch từ thứ Hai đến thứ Sáu.

#### Nối ingest với predict bằng Lambda Destinations

```python
from aws_cdk import aws_iam as iam, aws_lambda_destinations as destinations, aws_scheduler as scheduler

# ingest xong (thành công hay lỗi) đều gọi predict; predict tự bỏ qua tài sản thiếu dữ liệu
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

#### Hành vi khi có lỗi

- Một nguồn dữ liệu lỗi: các tài sản còn lại vẫn được dự đoán
- Thiếu phiên mới nhất: predict **không lùi về ngày cũ**, chỉ bỏ qua tài sản đó
- CloudWatch Alarm cảnh báo khi Lambda lỗi

#### Kiểm tra

```bash
aws dynamodb query --profile cpaws-dev --table-name cpaws-dev-forecasts \
  --key-condition-expression "asset = :a" \
  --expression-attribute-values '{":a": {"S": "gold"}}' \
  --no-scan-index-forward --limit 1
```

**Ảnh minh họa:** ...
