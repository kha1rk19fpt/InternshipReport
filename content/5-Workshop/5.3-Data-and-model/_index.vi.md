---
title: "Dữ liệu và mô hình"
date: 2026-11-20
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---
#### Tổng quan

Phần này xây hai khối logic chạy trong Lambda:

1. [Thu thập dữ liệu giá](5.3.1-Ingest-prices/): tải giá theo lịch giao dịch, làm sạch, gắn cờ và ghi vào S3
2. [Mốc dự báo phân vị](5.3.2-Baseline-forecast/): tính target log return, mốc 252 phiên, chuẩn hóa phân vị và xác suất tăng

#### Cấu trúc thư mục

```text
src/
├── data/        # ingest_prices.py: CLI + hàm run() cho Lambda ingest
├── features/    # build.py: hàm tạo đặc trưng dùng chung train/predict
├── models/      # baselines.py, dlinear_numpy.py
├── eval/        # pinball loss, coverage, kiểm định DM
└── serving/     # forecast_record.py: định dạng bản ghi DynamoDB
infra/           # AWS CDK (Python)
web/             # React + TypeScript + Vite
tests/
```

#### Nguyên tắc chống rò rỉ dữ liệu

- Chỉ dùng thông tin **đã biết tại thời điểm dự đoán t**
- Train và predict dùng **cùng một hàm** tạo đặc trưng
- Tín hiệu tin tức căn theo **thời điểm công bố**, không theo ngày của sự kiện
- Tập kiểm tra 2020 → nay **chỉ chạy một lần** sau khi khóa tham số
