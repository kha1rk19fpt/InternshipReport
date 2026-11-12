---
title: "Mốc dự báo phân vị"
date: 2026-11-20
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---
#### Target: log return h phiên

```python
import numpy as np
import pandas as pd

QUANTILES = np.array([0.05, 0.10, 0.25, 0.50, 0.75, 0.90, 0.95])

def log_return(close: pd.Series, h: int) -> pd.Series:
    # return từ phiên t đến t+h theo lịch giao dịch; NaN khi thiếu giá hoặc giá <= 0
    p = close.where(close > 0)
    return np.log(p.shift(-h) / p)
```

#### Mốc phân vị 252 phiên (nhân quả)

Return h phiên bắt đầu tại phiên s chỉ được biết ở phiên s + h, nên tại thời điểm t chỉ dùng các return đã kết thúc.

```python
def baseline_q252(r_h: pd.Series, t: pd.Timestamp, h: int) -> np.ndarray:
    # bỏ h giá trị cuối vì chưa kết thúc tại t, lấy 252 giá trị gần nhất
    known = r_h.loc[:t].iloc[:-h].dropna()
    return np.quantile(known.iloc[-252:], QUANTILES)
```

#### Chuẩn hóa phân vị và xác suất tăng

```python
def normalize_quantiles(q: np.ndarray) -> np.ndarray:
    # sắp xếp đúng một lần sau khi mô hình xuất kết quả, tránh phân vị bị chéo
    return np.sort(q, axis=-1)

def prob_up(q: np.ndarray) -> float:
    # nội suy tuyến tính hàm phân phối tại 0, kẹp trong [0.05, 0.95]
    f0 = np.interp(0.0, q, QUANTILES, left=0.0, right=1.0)
    return float(np.clip(1.0 - f0, 0.05, 0.95))
```

#### Quy đổi ra khoảng giá

`price_q = last_price × exp(q)`. Web hiển thị q10, q50, q90 và ghi rõ nguồn "Mốc thống kê 252 phiên".

#### Đánh giá bằng pinball loss

```python
def pinball_loss(y: np.ndarray, q_pred: np.ndarray) -> float:
    # y: (n,), q_pred: (n, 7)
    diff = y[:, None] - q_pred
    return float(np.mean(np.maximum(QUANTILES * diff, (QUANTILES - 1) * diff)))
```

`pinball_skill = 1 - loss_model / loss_baseline`. Một mô hình học máy chỉ thay mốc khi cận dưới khoảng tin cậy 95% của pinball skill lớn hơn 0.

**Ảnh minh họa:** ...
