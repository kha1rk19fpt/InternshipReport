---
title: "Blog 1"
date: 2026-10-24
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Đừng để "độ chính xác 99%" đánh lừa: dự báo phân phối return và pinball loss

*Trịnh Tiến Khải · Nhóm CPAWS · Blog tự viết từ quá trình làm dự án*

Khi bắt đầu dự án CPAWS (dự đoán giá vàng, bạc, dầu Brent và WTI trong 1–7 ngày tới), câu hỏi đầu tiên nhóm tôi phải trả lời không phải "dùng mô hình gì", mà là **"dự đoán cái gì và đo bằng cách nào"**. Bài này chia sẻ ba quyết định quan trọng nhất mà tôi rút ra: dự đoán return thay vì giá, dự đoán phân vị thay vì một con số, và luôn so với một mốc thống kê đủ mạnh.

## 1. Cái bẫy của việc dự đoán mức giá

Giả sử giá vàng hôm nay là 2.000 USD/ounce. Một "mô hình" lười nhất có thể nghĩ ra là: *giá ngày mai = giá hôm nay*. Vì giá vàng hiếm khi thay đổi quá 1–2% mỗi ngày, sai số phần trăm trung bình (MAPE) của mô hình này chỉ khoảng 1%. Báo cáo sẽ ghi "độ chính xác 99%", nghe rất ấn tượng, nhưng mô hình không biết gì về tương lai cả.

Nhiều bài toán dự báo giá trên mạng mắc đúng lỗi này: vẽ đường dự đoán gần như trùng đường giá thật, nhưng thực chất đường dự đoán chỉ là giá hôm qua dịch sang phải một ngày.

**Cách nhóm tôi xử lý:** dự đoán **log return h phiên**:

```text
r(t, h) = ln( P(t+h) / P(t) ),  h = 1..7
```

Với target này, mô hình "giá không đổi" tương ứng với dự đoán return = 0, và mọi mô hình khác phải chứng minh nó học được sự thay đổi thật. Khi cần hiển thị, chúng tôi chỉ việc quy đổi ngược: `giá dự đoán = giá hiện tại × exp(r)`.

Một chi tiết nhỏ nhưng quan trọng: *h phiên* tính theo **lịch giao dịch** của từng tài sản (NYSE cho quỹ GLD/SLV, CME cho dầu), không phải h ngày lịch. Nếu thiếu giá hợp lệ tại t hoặc t + h, hoặc giá ≤ 0 (như WTI ngày 20/4/2020), return được để là NaN thay vì tự điền.

## 2. Một con số là chưa đủ: dự đoán phân vị

Thị trường hàng hóa có độ bất định rất lớn. Nói "giá vàng tuần tới tăng 0,3%" là một thông tin gần như vô nghĩa nếu không kèm theo "nhưng có thể dao động từ −2% đến +2,6%". Vì vậy CPAWS dự đoán **7 phân vị** của return: 5%, 10%, 25%, 50%, 75%, 90%, 95%.

Từ 7 phân vị này, web hiển thị:
- **q50**: kịch bản trung tâm
- **q10 – q90**: khoảng giá mà mô hình cho là có khoảng 80% khả năng chứa giá thật
- **Xác suất tăng**: nội suy tuyến tính hàm phân phối tại return = 0, rồi kẹp trong [5%, 95%] để không bao giờ tuyên bố chắc chắn tuyệt đối

Có một lỗi kỹ thuật thường gặp: mô hình học máy có thể xuất ra phân vị bị "chéo" (q10 lớn hơn q50). Chúng tôi xử lý bằng một hàm `normalize_quantiles()` sắp xếp lại các phân vị **đúng một lần**, ngay sau khi mô hình xuất kết quả trong Lambda dự đoán.

## 3. Đo phân vị bằng pinball loss

Không thể dùng MAE hay RMSE để chấm một dự đoán phân vị. Công cụ đúng là **pinball loss** (quantile loss). Với phân vị τ, dự đoán q và giá trị thật y:

```text
L_τ(y, q) = τ · (y − q)        nếu y ≥ q
          = (1 − τ) · (q − y)  nếu y < q
```

Ý nghĩa trực quan: với τ = 0,9, nếu giá thật vượt q90 thì bị phạt nặng (hệ số 0,9), còn nếu giá thật thấp hơn q90 thì chỉ bị phạt nhẹ (hệ số 0,1). Nhờ vậy, q90 tối ưu sẽ là mức mà giá thật chỉ vượt qua khoảng 10% số lần. Pinball loss là một *proper scoring rule*: mô hình chỉ đạt điểm tốt nhất khi báo đúng niềm tin của nó.

Bên cạnh pinball loss, chúng tôi theo dõi các chỉ số hiệu chỉnh:
- **coverage_80**: tỷ lệ giá thật nằm trong khoảng q10–q90, lý tưởng là gần 80%
- **width_80**: độ rộng khoảng; khoảng rộng vô hạn thì coverage 100% nhưng vô dụng
- **Brier skill** cho xác suất tăng

## 4. Luôn so với một mốc đủ mạnh

Mốc "return = 0" quá yếu cho bài toán phân vị, vì nó không nói gì về độ biến động. Mốc chúng tôi chọn là **phân vị thực nghiệm của 252 return gần nhất** (khoảng một năm giao dịch), tính nhân quả: tại thời điểm t chỉ dùng các return đã kết thúc trước t.

Chỉ số chính là:

```text
pinball_skill = 1 − loss_mô_hình / loss_mốc_252
```

`pinball_skill > 0` nghĩa là mô hình tốt hơn mốc. Nhóm đặt quy tắc chặt hơn: mô hình học máy chỉ được thay mốc khi **cận dưới khoảng tin cậy 95%** của pinball skill lớn hơn 0, và khi so hai mô hình thì dùng thêm kiểm định Diebold–Mariano.

## 5. Kết quả thật và bài học

Trên tập validation năm 2019, DLinear (mô hình học máy đầu tiên của nhóm, chỉ dùng giá với cửa sổ 60 phiên) **thua mốc 252 phiên ở cả 28/28 nhóm** (4 tài sản × 7 tầm dự báo). Đây là một kết quả đáng thất vọng nhưng rất có giá trị. Nếu chúng tôi đo bằng MAPE trên giá, DLinear vẫn sẽ "đạt 99%" và có thể đã được đưa vào sản phẩm.

Vì vậy sản phẩm hiện dùng mốc thống kê 252 phiên, ghi rõ "Mốc thống kê 252 phiên" trên web và trong câu trả lời của chatbot; DLinear được giữ làm challenger.

**Ba điều tôi mang theo sau dự án:**
1. Chọn target và chỉ số đánh giá quan trọng hơn chọn mô hình.
2. Một mốc thống kê đơn giản, được đánh giá trung thực, đáng tin hơn một mô hình phức tạp không chứng minh được hiệu quả.
3. Hiển thị độ bất định (khoảng dự đoán) là một cách tôn trọng người dùng.

## Tài liệu tham khảo

- Gneiting, T. & Raftery, A. (2007). [Strictly Proper Scoring Rules, Prediction, and Estimation](https://doi.org/10.1198/016214506000001437). *JASA*.
- Hyndman, R. & Athanasopoulos, G. [Forecasting: Principles and Practice (3rd ed.)](https://otexts.com/fpp3/)
- Zeng, A. et al. (2022). [Are Transformers Effective for Time Series Forecasting? (DLinear)](https://arxiv.org/abs/2205.13504)
- Harvey, D., Leybourne, S. & Newbold, P. (1997). [Testing the equality of prediction mean squared errors](https://doi.org/10.1016/S0169-2070%2896%2900719-4). *IJF*.
