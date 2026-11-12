---
title: "Thu thập dữ liệu giá"
date: 2026-11-20
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---
#### Nguồn dữ liệu

| Tài sản | Mã chuỗi | Lịch giao dịch |
|---|---|---|
| Vàng | GLD | NYSE |
| Bạc | SLV | NYSE |
| Dầu Brent | BZ=F | CME |
| Dầu WTI | CL=F | CME |
| Tỷ giá USD/VND | VND=X | chỉ dùng quy đổi hiển thị |

Giá Brent và WTI được đối chiếu với EIA Open Data API.

#### Chạy thử trên máy

```bash
python -m src.data.ingest_prices --start 2012-01-01 --dest local
```

Lambda ingest gọi cùng hàm `run()` với `--dest s3`.

#### Quy tắc làm sạch

- Dùng `pandas_market_calendars` để biết phiên giao dịch hợp lệ của từng tài sản
- **Bỏ thanh của ngày hiện tại** theo giờ New York (phiên chưa đóng cửa)
- Mỗi lần chạy tải lại **5 phiên gần nhất** và ghi đè theo khóa, để sửa dữ liệu bị nhà cung cấp cập nhật muộn
- Bảng clean dạng dài, mỗi dòng có cột `flag`; dòng bị loại ghi vào `rejected.parquet`
- **Không forward-fill target**; giá ≤ 0 (ví dụ WTI ngày 20/4/2020) được giữ nhưng return là NaN

#### Bố cục trên S3

```text
s3://cpaws-dev-lake/
├── raw/
├── clean/prices/dt=<ngày>/
├── features/
├── models/
└── eval/
```

#### Kiểm tra

```bash
pytest tests/test_ingest_prices.py -q
```

**Ảnh minh họa:** ...
