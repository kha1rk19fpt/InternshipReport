---
title: "API, xác thực và web"
date: 2026-11-20
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---
#### Các endpoint

REST API stage `dev`, định nghĩa trong `infra/stacks/cpaws_api_stack.py`. Hợp đồng chi tiết nằm ở `docs/api/contract.md`.

| Endpoint | Lambda | Mô tả |
|---|---|---|
| `GET /health` | `forecasts-api` | Kiểm tra API, không cần đăng nhập |
| `GET /forecasts` | `forecasts-api` | Dự báo mới nhất của cả 4 tài sản |
| `GET /forecasts/{asset}` | `forecasts-api` | Dự báo mới nhất của một tài sản (`gold`, `silver`, `brent`, `wti`); thêm `?history=N` (N = 1–520) để lấy giá cuối phiên của N phiên gần nhất |
| `GET /accuracy/{asset}` | `accuracy-api` | Độ tin cậy `rolling_8w`, chỉ đọc bảng `cpaws-dev-accuracy` |
| `POST /chat` | `chat-api` | Chatbot (mục [5.5](../../5.5-chatbot/)) |

- **Xác thực:** mọi route trừ `/health` dùng **Cognito authorizer**. Client gửi ID token trong header `Authorization`.
- **Throttling** ở mức stage: 10 request/giây, burst 20.
- **CORS** chỉ cho phép **một** origin, truyền bằng `-c allowed_origin`. Giá trị này được dùng ở ba chỗ: preflight `OPTIONS`, header do Lambda trả về, và `GatewayResponse` cho lỗi 4xx/5xx (để trình duyệt vẫn đọc được mã 401, 429, 504).
- **Mỗi Lambda chỉ có quyền trên đúng bảng mình cần:** `forecasts-api` được `dynamodb:Query` trên bảng `forecasts` và `s3:GetObject` trên đúng prefix `serving/price-history/*`; `accuracy-api` được `dynamodb:GetItem`, `dynamodb:Query` trên bảng `accuracy`; `chat-api` được đọc `forecasts`, `news-labels` (hiện tin được đọc từ file JSON đóng gói trong Lambda) và cập nhật bộ đếm trong bảng `chat-usage` (chi tiết ở mục 5.5).

#### Lịch sử giá 2 năm (`?history=`)

Từ bản `v1.0.0`, trang sản phẩm vẽ được lịch sử giá tới 520 phiên (khoảng 2 năm giao dịch). `forecasts-api` ghép hai nguồn:

- **Bảng `Forecasts`:** giá cuối phiên của các ngày đã có dự báo (từ 27/10/2026). Trùng ngày thì nguồn này được dùng.
- **File JSON trên S3:** `serving/price-history/<asset>.json`, xuất **một lần** bằng `scripts/export_price_history.py` từ giá clean, dùng cùng hàm quy đổi đơn vị với Lambda `predict`. Lần xuất ngày 22/11/2026 gồm 520 phiên × 4 tài sản, tới phiên 20/11. Phiên mới hơn lấy từ bảng `Forecasts`, nên không cần xuất lại hằng ngày.

Lambda chỉ đọc S3 khi bảng chưa đủ N điểm và giữ file trong bộ nhớ 30 phút. Nếu đọc S3 lỗi, API ghi log `PRICE_HISTORY_FILE_UNAVAILABLE` và vẫn trả `200` với phần lấy từ bảng.

![S3 serving/price-history](/images/5-Workshop/5.3.1-s3-price-history.png)

*Thư mục `serving/price-history/` trên bucket `cpaws-dev-lake`: 4 file JSON (khoảng 24 KB mỗi file), xuất ngày 22/11/2026.*

#### Deploy

```bash
cdk deploy CpawsServing --profile cpaws-dev
cdk deploy CpawsAuth --profile cpaws-dev
cdk deploy CpawsApi --profile cpaws-dev -c allowed_origin=https://<nhanh>.<app-id>.amplifyapp.com
```

Output của `CpawsAuth` có `ForecastsUserPoolId` và `ForecastsUserPoolClientId`. Output của `CpawsApi` có URL của API. Web cần cả ba giá trị này lúc build.

#### Tài khoản người dùng

Từ ngày 22/11/2026, User Pool `cpaws-dev-users` **mở tự đăng ký có xác minh email** để người chấm tự tạo tài khoản (ADR-0014). Đây là cấu hình tạm thời, có các giới hạn sau:

- tự đăng ký đóng lại **chậm nhất ngày 10/12/2026**: PR CDK đặt lại `self_sign_up_enabled=False`, build web với `VITE_ALLOW_SIGNUP=false`, rồi vô hiệu hóa các tài khoản ngoài danh sách được phép;
- mỗi thành viên mời tối đa 8 người dùng thử, gửi link qua tin nhắn riêng, không đăng công khai;
- chatbot vẫn giới hạn 20 lượt/người/ngày và 100 lượt/ngày cho cả hệ thống, nên thêm người dùng không làm chi phí Bedrock vượt trần;
- web không có tính năng tải dữ liệu giá.

`VITE_ALLOW_SIGNUP` chỉ ẩn hoặc hiện nút đăng ký trên web, không phải ranh giới bảo mật: ai có App Client ID cũng gọi được API `SignUp` khi User Pool cho phép. Ranh giới thật là cấu hình User Pool.

Khi cần, nhóm vẫn tạo tài khoản tay, ví dụ khi người chấm không nhận được email xác minh do hạn mức email `COGNITO_DEFAULT`:

```bash
aws cognito-idp admin-create-user --profile cpaws-dev \
  --user-pool-id <USER_POOL_ID> --username <EMAIL_NGUOI_DUNG> \
  --user-attributes Name=email,Value=<EMAIL_NGUOI_DUNG> Name=email_verified,Value=true
```

Cognito gửi mật khẩu tạm qua email. Lần đăng nhập đầu, web yêu cầu người dùng đổi mật khẩu.

#### Lấy ID token để thử API

App client cho phép luồng `USER_PASSWORD_AUTH`, nên lấy được token ngay từ CLI:

```bash
read -rs PW   # nhập mật khẩu, không hiện trên màn hình và không vào lịch sử shell
ID_TOKEN=$(aws cognito-idp initiate-auth --profile cpaws-dev \
  --client-id <USER_POOL_CLIENT_ID> --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=<EMAIL_NGUOI_DUNG>,PASSWORD="$PW" \
  --query "AuthenticationResult.IdToken" --output text)
unset PW

API=https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev
curl -s -o /dev/null -w "%{http_code}\n" "$API/forecasts/gold"                            # 401
curl -s -o /dev/null -w "%{http_code}\n" -H "Authorization: $ID_TOKEN" "$API/forecasts/gold"  # 200
```

Bản ghi trả về có:
- `base_date`, `model_version` (`baseline-q252`);
- các phân vị theo kỳ hạn h = 1–7, xác suất tăng;
- tỷ giá `fx.usd_vnd` kèm `fx.date`;
- cờ `is_sample` để web gắn nhãn khi đó là dữ liệu mẫu.

#### Web trên Amplify Hosting

Web viết bằng React + TypeScript + Vite trong thư mục `web/`. Các biến `VITE_*` được đọc **lúc build**, nên muốn đổi cờ thì build lại web, không cần deploy API.

| Biến | Ý nghĩa |
|---|---|
| `VITE_API_URL` | URL của API (để trống thì web chạy bằng dữ liệu mẫu) |
| `VITE_USE_SAMPLE` | Đặt `false` khi build bản thật (file mẫu để `true`); để `true` hoặc để trống `VITE_API_URL` thì web chạy bằng dữ liệu mẫu |
| `VITE_AUTH_ENABLED`, `VITE_REGION`, `VITE_USER_POOL_ID`, `VITE_USER_POOL_CLIENT_ID` | Đăng nhập Cognito. ID của User Pool và app client là giá trị công khai của SPA, không phải bí mật |
| `VITE_SHOW_METAL_PRICES` | Hiện giá vàng/bạc quy đổi từ GLD/SLV, kèm dòng ghi nguồn |
| `VITE_ACCURACY_API` | Hiện khối "Độ tin cậy" từ `/accuracy/{asset}` |
| `VITE_ALLOW_SIGNUP` | Hiện nút **Tạo tài khoản** (bản nộp đặt `true` trong thời gian chấm) |

```bash
cd web
cp .env.example .env.local   # điền các giá trị ở trên
npm ci
npm run build                # kết quả nằm trong web/dist
```

Nhóm deploy web bằng **zip upload tay**:
1. Nén nội dung `web/dist` thành file zip.
2. Mở Amplify Console, app `cpaws-dev-web`, nhánh `dev`, chọn **Deploy updates** rồi kéo file zip vào.
3. Thêm rewrite để mọi đường dẫn của SPA trả về `index.html`.

Zip upload tay không gắn với commit nào, nên nhóm ghi lại commit của từng lần deploy. Deployment 6 (22/11/2026 21:27) build từ nhánh `production`, tag `v1.0.0`. Deployment 7 (23/11/2026 22:07) là bản `v1.0.1` đang chạy. Repo đã có sẵn `amplify.yml` để sau này nối Amplify với GitHub cho tự build.

#### Giao diện bản nộp (v1.0.1)

- **Trang công khai:** trang giới thiệu (`/`) và `/about`, không có số giá và không gọi API dự báo. Các trang tổng quan, sản phẩm, `/accuracy` và `/guide` (Cách đọc dự báo) cần đăng nhập; chưa đăng nhập thì chuyển về `/dang-nhap?next=…`.
- **Tài khoản:** form đăng ký có Họ tên; sidebar hiện Họ tên, nút Đăng xuất và thu gọn được.
- **Biểu đồ trang sản phẩm:** chọn mốc từ 1 tháng tới 2 năm (lấy bằng `?history=`), kéo thanh Brush để phóng to một đoạn.
- **Chatbot:** lịch sử chat giữ theo phiên trình duyệt và mất khi đóng trang; server vẫn không lưu hội thoại.
- **Hiển thị:** giao diện sáng/tối; menu và các trang dùng được trên điện thoại.

Chạy web trên máy với dữ liệu mẫu, không cần AWS:

```bash
cd web && npm ci && npm run dev   # http://localhost:5173
```

![API Gateway](/images/5-Workshop/5.4.3-api-gateway.png)

*API Gateway `cpaws-dev-forecasts-api`: cây resource gồm `/health`, `/forecasts`, `/forecasts/{asset}`, `/accuracy/{asset}` và `/chat`; mỗi resource có `OPTIONS` cho CORS.*

![Cognito sign-up](/images/5-Workshop/5.4.3-cognito.png)

*User Pool `cpaws-dev-users`, mục Sign-up: Cognito tự gửi email xác minh, Self-registration đang **Enabled** (tạm thời, đóng chậm nhất 10/12/2026).*

![Amplify deployments](/images/5-Workshop/5.4.3-amplify.png)

*Amplify app `cpaws-dev-web`, nhánh `dev`: 7 lần deploy bằng zip; Deployment 7 (23/11/2026 22:07) là bản v1.0.1.*

![Trang giới thiệu](/images/5-Workshop/5.4.3-login1.png)

*Trang giới thiệu công khai của web: nút Đăng nhập và Tạo tài khoản; biểu đồ ở đây chỉ là hình minh họa, không phải dữ liệu thật.*

![Trang giới thiệu (phần dưới)](/images/5-Workshop/5.4.3-login2.png)

*Phần dưới trang giới thiệu: 4 sản phẩm theo dõi, 4 bước tạo dự báo, dòng nguồn dữ liệu (Yahoo Finance, U.S. EIA) và câu "không phải lời khuyên đầu tư".*
