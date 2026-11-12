---
title: "API và xác thực"
date: 2026-11-20
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---
#### Các endpoint

| Endpoint | Mô tả |
|---|---|
| `GET /health` | Kiểm tra API |
| `GET /forecasts` | Dự đoán mới nhất của cả 4 tài sản |
| `GET /forecasts/{asset}` | Dự đoán mới nhất của một tài sản (`gold`, `silver`, `brent`, `wti`) |

Mọi endpoint dữ liệu dùng **Cognito authorizer**; client gửi ID token trong header `Authorization`.

#### Deploy

```bash
cdk deploy CpawsServing CpawsAuth CpawsApi --profile cpaws-dev \
  -c allowed_origin=http://localhost:5173
```

User Pool **không cho tự đăng ký**; quản trị viên tạo tài khoản cho người dùng thử nghiệm.

#### Gọi thử API

Đăng nhập trên web để lấy ID token, rồi:

```bash
curl -H "Authorization: $ID_TOKEN" \
  https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/<stage>/forecasts/gold
```

Kết quả gồm `base_date`, `model_version` (ví dụ `baseline-q252`), các phân vị theo h = 1–7, xác suất tăng, tỷ giá `fx.usd_vnd` và cờ `is_sample` cho dữ liệu mẫu.

#### Chạy web trên máy

```bash
cd web
npm install
npm run dev
```

**Ảnh minh họa:** ...
