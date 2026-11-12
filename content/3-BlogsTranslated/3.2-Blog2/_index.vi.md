---
title: "Blog 2"
date: 2026-11-07
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Năm cái bẫy rò rỉ dữ liệu tương lai khi dự báo chuỗi thời gian tài chính

*Trịnh Tiến Khải · Nhóm CPAWS · Blog tự viết từ quá trình làm dự án*

Trong dự báo chuỗi thời gian, **rò rỉ dữ liệu tương lai** (look-ahead leakage) là khi mô hình vô tình được nhìn thấy thông tin mà ở thời điểm dự đoán thật sự chưa thể có. Kết quả là backtest đẹp bất thường, nhưng khi chạy thật thì mô hình sụp đổ. Khi làm phần mô hình cho dự án CPAWS, tôi gặp hoặc suýt gặp cả năm cái bẫy dưới đây. Bài này tổng hợp lại để các bạn làm dự án tương tự tránh được.

Nguyên tắc chung chỉ có một câu: **tại thời điểm dự đoán t, chỉ được dùng thông tin thực sự đã biết tại t.** Cái khó là thông tin tương lai thường lọt vào theo những cách rất kín đáo.

## Bẫy 1: Phân rã tín hiệu trên toàn bộ chuỗi

Mô hình chính của nhóm dùng **CEEMDAN** để tách chuỗi giá thành các thành phần dao động (IMF) và xu hướng. Cách làm phổ biến trong nhiều bài báo là chạy CEEMDAN một lần trên toàn bộ chuỗi, rồi mới chia train/test.

Vấn đề: CEEMDAN là phép phân rã toàn cục. Giá trị IMF tại ngày t phụ thuộc vào cả những ngày sau t. Dùng IMF này làm đặc trưng tức là gián tiếp nhìn thấy tương lai, và kết quả backtest sẽ tốt một cách giả tạo.

**Cách xử lý:** chạy CEEMDAN theo **cửa sổ trượt**: tại mỗi t, chỉ phân rã đoạn giá kết thúc tại t, và chỉ lấy giá trị cuối cùng của mỗi IMF làm đặc trưng. Cách này tốn tính toán hơn rất nhiều, nên nhóm cache kết quả IMF lên S3 để không phải chạy lại. Năm 2012–2013 được dành riêng để "khởi động" cửa sổ.

## Bẫy 2: Chuẩn hóa bằng thống kê của cả tập dữ liệu

`StandardScaler().fit(X)` trên toàn bộ dữ liệu rồi mới chia train/validation là lỗi kinh điển: trung bình và độ lệch chuẩn đã chứa thông tin của tương lai.

**Cách xử lý:** chỉ `fit` scaler trên tập train (2014–2018), lưu tham số vào artifact của mô hình (`scaler.json`), rồi dùng đúng tham số đó cho validation, test và khi dự đoán thật trong Lambda.

## Bẫy 3: Tin tức và chỉ số được gắn theo ngày sự kiện, không theo ngày công bố

Đây là bẫy kín đáo nhất. Ví dụ:
- Biên bản họp FOMC được công bố khoảng ba tuần sau cuộc họp. Nếu gắn biên bản vào ngày họp, mô hình đã đọc được nó sớm ba tuần.
- Chỉ số rủi ro địa chính trị GPR được cập nhật định kỳ; giá trị của một ngày chỉ có sau ngày công bố.
- Thông cáo được đăng sau giờ đóng cửa thị trường chỉ ảnh hưởng đến phiên giao dịch kế tiếp.

**Cách xử lý:** mỗi nguồn tín hiệu có một quy tắc về **thời điểm công bố** (trong code là `PUBLISHED_AT_RULES`). Tín hiệu chỉ được dùng cho phiên giao dịch đầu tiên sau thời điểm công bố. Quan trọng hơn: train và predict dùng **chung một hàm tạo đặc trưng** với chung bộ quy tắc, để không có chuyện backtest dùng một logic còn sản phẩm dùng logic khác.

## Bẫy 4: Lịch giao dịch và forward-fill

Các tài sản giao dịch trên những sàn khác nhau có lịch nghỉ khác nhau. Một ví dụ thực tế nhóm gặp: ngày 5/12/2018, sàn NYSE đóng cửa (quốc tang), trong khi sàn năng lượng CME vẫn giao dịch, nhưng nguồn dữ liệu lại thiếu phiên này của dầu Brent và WTI.

Phản xạ tự nhiên là forward-fill cho đủ ngày. Nhưng nếu forward-fill **target**, mô hình sẽ học được những ngày "return = 0" giả, và ngày sau đó lại có một return gộp hai phiên. Cả hai đều sai lệch phân phối.

**Cách xử lý:**
- Dùng `pandas_market_calendars` để xác định phiên hợp lệ của từng tài sản; t + h được tính theo lịch của chính tài sản đó
- **Không forward-fill target**; dòng thiếu được gắn cờ và loại khỏi đánh giá, chấp nhận mất một số mẫu (với lỗ hổng 5/12/2018, khoảng 301 dòng dầu trong validation 2019 bị gắn cờ cửa sổ đầu vào không hợp lệ)
- Bỏ thanh giá của ngày hiện tại (theo giờ New York) vì phiên chưa đóng cửa

## Bẫy 5: Dùng tập test nhiều lần

Rò rỉ không chỉ nằm trong code mà còn trong **quy trình**. Nếu sau mỗi lần xem kết quả test, ta quay lại chỉnh tham số, thì tập test đã trở thành tập validation thứ hai và con số báo cáo không còn là ước lượng trung thực.

**Cách xử lý của nhóm:**
- Chia cố định: 2014–2018 train, 2019 validation, 2020 → nay test
- Mọi lựa chọn (cửa sổ, kiến trúc, tín hiệu giữ lại) chỉ dựa trên validation
- Tập test **chỉ chạy một lần** sau khi khóa tham số, với cấu hình và commit được ghi lại trước khi chạy
- Nguyên tắc tương tự áp dụng cho bộ câu hỏi kiểm thử chatbot: không sửa prompt rồi chấm lại trên cùng bộ câu hỏi

## Kiểm tra bằng test tự động

Lời hứa "tôi cẩn thận rồi" là không đủ. Một kiểu test đơn giản nhưng hiệu quả mà tôi viết cho hàm tạo đặc trưng:

```python
def test_features_do_not_use_future(prices):
    t = prices.index[-30]
    full = build_features(prices).loc[t]
    # thay toàn bộ dữ liệu sau t bằng giá trị vô nghĩa
    tampered = prices.copy()
    tampered.loc[tampered.index > t] = 1e9
    cut = build_features(tampered).loc[t]
    # đặc trưng tại t không được thay đổi
    assert np.allclose(full, cut, equal_nan=True)
```

Nếu đặc trưng tại t thay đổi khi dữ liệu tương lai thay đổi, chắc chắn có rò rỉ.

## Kết luận

Rò rỉ dữ liệu tương lai hiếm khi đến từ một dòng code sai rõ ràng; nó đến từ những giả định ngầm về thời gian. Ba thói quen giúp tôi nhiều nhất là: luôn hỏi "thông tin này có được lúc nào?", dùng chung một hàm cho train và predict, và viết test cố tình làm hỏng dữ liệu tương lai.

## Tài liệu tham khảo

- Torres, M. E. et al. (2011). [A complete ensemble empirical mode decomposition with adaptive noise](https://doi.org/10.1109/ICASSP.2011.5947265). *ICASSP*.
- [PyEMD documentation (CEEMDAN)](https://pyemd.readthedocs.io/)
- [pandas_market_calendars](https://github.com/rsheftel/pandas_market_calendars)
- [Federal Reserve – FOMC meeting calendars, statements and minutes](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm)
- [Geopolitical Risk (GPR) Index](https://www.matteoiacoviello.com/gpr.htm)
- [scikit-learn – Common pitfalls: data leakage](https://scikit-learn.org/stable/common_pitfalls.html)
