---
title: "Blog kỹ thuật"
date: 2026-09-08
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Ba bài blog dưới đây do tôi tự viết, tổng hợp kiến thức đã học và nghiên cứu trong quá trình làm phần mô hình và hạ tầng của dự án CPAWS.

### [Blog 1 - Đừng để "độ chính xác 99%" đánh lừa: dự báo phân phối return và pinball loss](3.1-Blog1/)

Vì sao dự đoán mức giá tạo ra độ chính xác ảo, cách chuyển sang dự đoán 7 phân vị của log return, chấm điểm bằng pinball loss và so với mốc phân vị 252 phiên. Bài kết thúc bằng kết quả thật: DLinear thua mốc thống kê ở 28/28 nhóm trên tập validation 2019 và bài học rút ra.

### [Blog 2 - Năm cái bẫy rò rỉ dữ liệu tương lai khi dự báo chuỗi thời gian tài chính](3.2-Blog2/)

Tổng hợp năm cách thông tin tương lai lọt vào mô hình: CEEMDAN trên toàn chuỗi, chuẩn hóa sai, gắn tin tức theo ngày sự kiện thay vì ngày công bố, forward-fill target qua ngày nghỉ của sàn, và dùng tập test nhiều lần. Mỗi bẫy kèm cách xử lý và một mẫu test tự động.

### [Blog 3 - Dự đoán theo lô serverless trên AWS: EventBridge Scheduler, Lambda container và DynamoDB](3.3-Blog3/)

Cách xây luồng dự đoán hằng ngày không có tài nguyên chạy 24/7: lịch chạy bằng EventBridge Scheduler, nối Lambda bằng Lambda Destinations, suy luận NumPy trong container image, thiết kế bảng DynamoDB theo truy vấn và ước tính chi phí.
