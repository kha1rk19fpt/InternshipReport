---
title: "Phụ lục: chi tiết dự toán"
date: 2026-11-25
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---
Trang này ghi đầy đủ giả định và 34 dòng của dự toán chi phí do Long lập ngày 24/11/2026 (tóm tắt ở mục [5.7](../)). Dự toán áp dụng cho một môi trường `cpaws-dev-*` tại Singapore; số liệu quan sát tháng 11 gồm cả hoạt động phát triển, kiểm thử và demo, chưa có một tháng production trọn vẹn. Đây là chi phí dự toán, không phải hóa đơn hay giới hạn chi tiêu.

Pricing Calculator xuất ngày 24/11/2026 lúc 23:57 (UTC+7), mốc giá 23/11/2026 23:33, chế độ `BEFORE_DISCOUNTS`, 34 dòng, tất cả ở `ap-southeast-1`. Bedrock và Scheduler lập bằng Pricing Calculator public (link ở mục 5.7); SSM Parameter Store tính theo bảng giá Standard. File CSV xuất từ Console có Account ID đầy đủ nên không công bố.

#### 1. Phương pháp và kịch bản tải

Dữ liệu được phân loại: **quan sát** (usage/metrics lịch sử), **cấu hình** (inventory/source), **giả định** (khối lượng tương lai), **dự toán** (giá Calculator). Không đồng nhất Historical cost = 0 của các dòng mới với hóa đơn thực tế. Dữ liệu Cost Explorer cấp tài khoản/region chưa hoàn toàn tách tag CPAWS được dùng làm proxy có ghi chú.

Với usage tích lũy trong d ngày, dự phóng tháng 30 ngày: `U_month = U_observed × 30 / d`, làm tròn hợp lý theo đơn vị. Với tài nguyên tồn tại cả tháng (MAU, alarms, storage) hoặc cron đã biết, dùng cấu hình và mức hiện tại thay vì nhân cơ học. Chi phí năm = 12 tháng tương đương, không giả định tăng trưởng, chưa gồm thuế và thay đổi giá.

##### 1.1. Tải ứng dụng và các giả định chính

| Thông số | Cơ sở | Baseline / tháng |
|---|---|---:|
| Cognito Essentials MAU | Cost Explorer: 3 tháng 10, 4 từ 01–23/11; giả định 5 người hoạt động | 5 |
| Forecast API | Giả định 60 request/ngày, không phải số đo traffic | 1.800 |
| Accuracy API | Giả định 20 request/ngày | 600 |
| Chat API | Giả định 10 lượt/ngày | 300 |
| API Gateway | 1.800 + 600 + 300; chưa cộng riêng health/preflight/retry | 2.700 |
| Scheduler | 2 lịch × 5 ngày/tuần; làm tròn 22/lịch | 44 |
| Lambda jobs | 22 ingest + 22 predict + 22 accuracy-job; một predict/ingest giả định | 66 |
| Lambda tổng | 2.700 API + 66 job | 2.766 |
| Bedrock | 7 chat thành công quan sát 22–23/11; 13 model calls | ~557 model calls |
| Amplify requests | 679 requests/2 ngày × 15; chỉ là chỉ báo tải hosting | 10.185 |

Giới hạn chatbot trong source là 20 lượt/người/ngày và 100 lượt/toàn hệ thống/ngày; baseline 300 lượt/tháng thấp hơn giới hạn thiết kế 3.000 lượt/tháng. Giới hạn lượt hỏi không phải hạn mức tiền/token.

##### 1.2. Lambda compute — đo thực tế và dự phóng

Nguồn: Lambda/CloudWatch, 17–23/11/2026. Tất cả x86_64; ephemeral storage 512 MB. Duration là thời gian chạy quan sát, không phải timeout. Compute = `memory_GB × duration_seconds × invocations`. Bảng dùng đúng duration đầu vào đã lưu trong Calculator (chat dùng billed duration; các function khác dùng runtime trung bình làm proxy, riêng predict có billed duration đối chiếu).

| Function | RAM (MB) | Avg runtime quan sát (ms) | Invocations quan sát/7 ngày | Duration dự toán (s) | Lượt/tháng | GB-s/tháng |
|---|---:|---:|---:|---:|---:|---:|
| ingest | 1024 | 12610 | 5 | 12.610 | 22 | 277.420 |
| predict | 1024 | 5392 | 5 | 5.392* | 22 | 118.624 |
| accuracy-job | 1024 | 49621 | 5 | 49.621 | 22 | 1091.662 |
| forecasts-api | 256 | 1299 | 71 | 1.299 | 1.800 | 584.550 |
| accuracy-api | 256 | 314 | 48 | 0.314 | 600 | 47.100 |
| chat-api | 512 | 4596 | 6 | 4.652 | 300 | 697.800 |
| **Tổng** | | | | | **2.766** | **2817.156** |

*Dòng predict khớp giá trị Calculator 118.624 GB-s; trung bình billed duration từ log khoảng 5.393 s, khác nhẹ do làm tròn. Dự toán chưa tính thêm retry, cold-start khác biệt hoặc concurrency bất thường. Hai lịch Scheduler không phải ba lịch: predict là Lambda Destination. Source đặt Scheduler retry = 0 và ingest có Lambda async retry = 1; Console hiển thị retry policy `-`, nên không xác nhận live bằng trường này. Baseline giả định một lần gọi thành công cho mỗi tác vụ.

##### 1.3. Dung lượng lưu trữ, requests và băng thông

| Hạng mục | Dữ liệu/căn cứ | Monthly assumption |
| --- | --- | --- |
| S3 storage | ~89,25 MB lake + ~1,06 MB CDK assets; snapshot inventory | 0.10 GB-Mo |
| S3 Tier 1 / Tier 2 | 218 / 3381 requests, 17–23/11 → ×30/7 | 934 / 14490 requests |
| DynamoDB on-demand | 4 bảng; đầu vào Calculator đã lưu; chưa có đủ chi tiết phép đo lịch sử trong bộ hồ sơ này | 6381 RRU; 11310 WRU; 0.00158 GB-Mo |
| Amplify storage | 0.0038870168 GB usage 01–23/11; 20 ngày app hoạt động (04–23/11) → ×30/20 | 0.006 GB |
| Amplify outbound | 5.547.694 bytes/2 ngày (22–23/11) → 83.215.410 bytes/30 ngày | 0.084 GB |
| ECR storage | 0.7476365831 GB-Mo/23 ngày → ×30/23; 8 image hiển thị trong 2 repo | 0.98 GB-Mo |
| ECR outbound | 0.1287299758 GB/23 ngày → ×30/23 | 0.168 GB |
| CloudWatch storage | 0.0000616697 GB-Mo/23 ngày; mức ngày 23/11 0.0000047902 ×30 | 0.00015 GB-Mo |
| CloudWatch vended logs | 0.0002119546 GB/23 ngày → ×30/23 | 0.00028 GB |
| CloudWatch alarms | 4.0626139866 alarm-month usage/23 ngày; 6 alarms hiện hữu | 6 standard alarms |
| CloudWatch custom metrics | 2 metric filters đã cấu hình; chưa xác nhận usage tính phí thực tế | 2 metrics (dự phòng) |
| CloudWatch GMD / API / query | 8 metrics / 56 requests / 0.0001027301 GB quét, 23 ngày → ×30/23 | 11 metrics / 74 requests / 0.00014 GB |
| SNS | 9 API requests/23 ngày; 1 email subscription Confirmed | 12 requests / 12 emails (giả định) |
| KMS | 107 requests/23 ngày; 0 customer-managed keys, 3 AWS-managed keys | 140 symmetric requests (giả định) |
| SSM | 2 Standard parameters; Standard throughput 40 TPS | 2 parameters; $0 phí SSM |

**Băng thông:** Hai dòng truyền ra đã đưa vào dự toán là Amplify 0.084 GB và ECR 0.168 GB, tổng 0.252 GB/tháng. Đây là lượng dữ liệu truyền, không phải tốc độ đường truyền. S3 không ghi nhận transfer in/out trong khung quan sát 17–23/11; chưa có lượng egress riêng cho API Gateway/SNS, không khẳng định bằng 0 tuyệt đối. Amplify không ghi nhận billable build minutes; dự toán 0 build/SSR/WAF vì là static hosting, không giả định chi phí build từ thời gian deployment hiển thị. ECR dùng usage billing, không cộng kích thước từng image vì có layer dùng chung. S3 storage là giả định duy trì snapshot, chưa mô hình tăng dung lượng/version theo thời gian. CloudWatch `IncomingBytes` 227.588 bytes trên 6 log groups dùng để đối chiếu; không cộng thêm vào Vended Logs để tránh tính trùng. Log group cũ thứ 7 không hiện metric; non-vended ingestion tạm tính 0.

##### 1.4. Amazon Bedrock — tính theo token

Source `src/chatbot/chat.py` đặt router mặc định `complex`, sử dụng `global.anthropic.claude-haiku-4-5-20251001-v1:0`, tối đa 3 vòng tool và 1500 output tokens mỗi call cho nhánh complex. Nova 2 Lite có trong code nhưng chưa có usage được xác nhận; không đưa thêm dòng Nova. Không giả định prompt caching, image input hoặc Guardrails đang tính phí.

| Thông số | Giá trị |
|---|---:|
| CloudWatch 17–23/11 | 15 model calls; 82.198 input / 3.708 output tokens |
| Mẫu chọn 22–23/11 (đối chiếu log chat) | 7 chat thành công; 13 calls; 71.904 input / 3.191 output |
| Trung bình model calls/chat | 13/7 = 1,857 |
| Trung bình input/chat | 10.272 tokens |
| Trung bình output/chat | 455,857 tokens |
| 300 chat/tháng | ~557 calls; 3.081.600 input / ~136.757 output tokens |
| Giá Global On-Demand Standard trong Calculator | $1/1M input; $5/1M output |
| Đầu vào Public Calculator | 1 request/min, 1 giờ/ngày, 1712 input / 76 output tokens/request |
| Token tương đương 30 ngày | 3.081.600 input; 136.800 output |
| **Chi phí** | **3.081600 + 0.684000 = $3.765600/tháng** |

Public Calculator dùng 1.800 **request tương đương để biểu diễn tổng token**, không phải số call thực tế dự kiến. Chênh lệch output token do giới hạn nhập số nguyên. Mẫu 7 chat nhỏ, có 6 chat dùng tool, chưa đại diện mọi tình huống production. Nếu người dùng hỏi dài hoặc nhiều vòng tool, chi phí tăng.

##### 1.5. EventBridge Scheduler

Hai schedules `cpaws-dev-ingest-daily`, `cpaws-dev-accuracy-daily` đều Enabled, `TUE-SAT`. CloudWatch `InvocationAttemptCount` group `default` = 23 trong 01–23/11. Accuracy schedule được tạo 11/11 nên không ngoại suy trực tiếp 23; dự toán 44 lượt/tháng theo cron hiện hành. Public Calculator ghi 44 invocations/month, 1 KB payload, Free Tier 14 triệu lượt/tháng, 0 billable invocations và $0.00. Giá giả định trước ưu đãi là $0.000044 nếu áp $1/triệu lượt. Retry ngoài baseline chưa được tính.

#### 2. Đủ 34 dòng dự toán

Số lượng và chi phí lấy trực tiếp từ bảng xuất của Pricing Calculator, không làm tròn trung gian; group Lambda viết rút gọn theo tên hàm.

| Service code | Group / function | Usage type | Operation | Monthly quantity | USD/month |
| --- | --- | --- | --- | --- | --- |
| AWSLambda | ingest | APS1-Lambda-GB-Second | — | 277.42 Lambda-GB-Second | 0.004623676 |
| AWSLambda | ingest | APS1-Request | — | 22 Request | 0.0000044 |
| AWSLambda | accuracy-api | APS1-Lambda-GB-Second | — | 47.1 Lambda-GB-Second | 0.000785002 |
| AWSLambda | accuracy-api | APS1-Request | — | 600 Request | 0.00012 |
| AWSLambda | accuracy-job | APS1-Lambda-GB-Second | — | 1091.662 Lambda-GB-Second | 0.018194403 |
| AWSLambda | accuracy-job | APS1-Request | — | 22 Request | 0.0000044 |
| AWSLambda | chat-api | APS1-Lambda-GB-Second | — | 697.8 Lambda-GB-Second | 0.011630023 |
| AWSLambda | chat-api | APS1-Request | — | 300 Request | 0.00006 |
| AWSLambda | forecasts-api | APS1-Lambda-GB-Second | — | 584.55 Lambda-GB-Second | 0.009742519 |
| AWSLambda | forecasts-api | APS1-Request | — | 1800 Request | 0.00036 |
| AWSLambda | predict | APS1-Lambda-GB-Second | — | 118.624 Lambda-GB-Second | 0.001977071 |
| AWSLambda | predict | APS1-Request | — | 22 Request | 0.0000044 |
| AmazonApiGateway | CPAWS-API-Gateway | APS1-ApiGatewayRequest | ApiGatewayRequest | 2700 APS1-ApiGatewayRequest | 0.011475 |
| AmazonDynamoDB | CPAWS-DynamoDB | APS1-ReadRequestUnits | PayPerRequestThroughput | 6381 ReadRequestUnits | 0.000909292 |
| AmazonDynamoDB | CPAWS-DynamoDB | APS1-TimedStorage-ByteHrs | — | 0.00158 GB-Mo | 0 |
| AmazonDynamoDB | CPAWS-DynamoDB | APS1-WriteRequestUnits | PayPerRequestThroughput | 11310 WriteRequestUnits | 0.0080301 |
| AmazonS3 | CPAWS-S3 | APS1-Requests-Tier1 | — | 934 Requests | 0.00467 |
| AmazonS3 | CPAWS-S3 | APS1-Requests-Tier2 | — | 14490 Requests | 0.005796 |
| AmazonS3 | CPAWS-S3 | APS1-TimedStorage-ByteHrs | — | 0.1 GB-Mo | 0.0025 |
| AWSAmplify | CPAWS-Amplify | APS1-DataStorage | — | 0.006 GigaBytes | 0.000138 |
| AWSAmplify | CPAWS-Amplify | APS1-DataTransferOut | — | 0.084 GigaBytes | 0.0126 |
| AmazonCognito | CPAWS-Cognito | APS1-CognitoEssentialsMAU | CognitoEssentialsOperation | 5 CognitoUserPoolsMAU | 0.075 |
| AmazonECR | CPAWS-ECR | APS1-TimedStorage-ByteHrs | — | 0.98 GB-Mo | 0.098 |
| AWSDataTransfer | CPAWS-ECR | APS1-DataTransfer-Out-Bytes | — | 0.168 GB | 0.02016 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:AlarmMonitorUsage | — | 6 Alarms | 0.6 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:GMD-Metrics | GetMetricData | 11 Metrics | 0.00011 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:MetricMonitorUsage | — | 2 Metrics | 0.6 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-CW:Requests | — | 74 Requests | 0.00074 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-DataScanned-Bytes | StartQuery | 0.00014 GB | 0.00000098 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-TimedStorage-ByteHrs | — | 0.00015 GB-Mo | 0.0000045 |
| AmazonCloudWatch | CPAWS-CloudWatch | APS1-VendedLog-Bytes | PutLogEvents | 0.00028 GB | 0.000196 |
| AmazonSNS | CPAWS-SNS | APS1-DeliveryAttempts-SMTP | — | 12 Notifications | 0 |
| AmazonSNS | CPAWS-SNS | APS1-Requests-Tier1 | — | 12 Requests | 0 |
| awskms | CPAWS-KMS | ap-southeast-1-KMS-Requests | — | 140 Requests | 0.00042 |
| **Console subtotal** |  |  |  | 34 lines | 1.488255766 |

#### Tài liệu tham khảo

- **R1.** [FCAJ Workshop standards](https://hcm-rules.awsfcaj.com/3-project/) — sections 4.3 (COST), 4.5 (VI/EN).
- **R2.** [FCAJ Workshop scoring criteria](https://hcm-rules.awsfcaj.com/5-scoring/).
- **R3.** [AWS Pricing Calculator](https://calculator.aws/) and [AWS Well-Architected — Cost Optimization Pillar](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/welcome.html).
- **R4.** [Amazon Bedrock Pricing](https://aws.amazon.com/bedrock/pricing/) and [Claude Haiku 4.5 model card](https://docs.aws.amazon.com/bedrock/latest/userguide/model-card-anthropic-claude-haiku-4-5.html). Model unit rates in this report come from the dated Calculator setup.
- **R5.** [Amazon EventBridge Pricing](https://aws.amazon.com/eventbridge/pricing/).
- **R6.** [AWS Systems Manager Pricing](https://aws.amazon.com/systems-manager/pricing/) and [AWS KMS Pricing](https://aws.amazon.com/kms/pricing/).
- **R7.** [AWS CDK destroy](https://docs.aws.amazon.com/cdk/v2/guide/ref-cli-cmd-destroy.html) and [CDK retained-resource troubleshooting](https://docs.aws.amazon.com/cdk/v2/guide/troubleshooting.html).
- **R8.** [Emptying a general purpose S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/empty-bucket.html) and [Deleting a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-bucket.html).
- **R9.** Additional pricing references: [Lambda](https://aws.amazon.com/lambda/pricing/), [API Gateway](https://aws.amazon.com/api-gateway/pricing/), [DynamoDB](https://aws.amazon.com/dynamodb/pricing/), [S3](https://aws.amazon.com/s3/pricing/), [Amplify](https://aws.amazon.com/amplify/pricing/), [Cognito](https://aws.amazon.com/cognito/pricing/), [ECR](https://aws.amazon.com/ecr/pricing/), [CloudWatch](https://aws.amazon.com/cloudwatch/pricing/), [SNS](https://aws.amazon.com/sns/pricing/).
- **R10.** [AWS Budgets](https://aws.amazon.com/aws-cost-management/aws-budgets/) and [AWS Cost Anomaly Detection](https://aws.amazon.com/aws-cost-management/aws-cost-anomaly-detection/).

*Pricing snapshot is dated Nov 23–24, 2026; linked prices and benefits can change. / Mốc giá 23–24/11/2026; bảng giá và ưu đãi có thể thay đổi.*
