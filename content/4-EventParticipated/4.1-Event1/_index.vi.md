---
title: "Sự Kiện 1"
date: 2026-09-29
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Báo Cáo Tóm Tắt: "AWS Cloud & AI Day 2026"

### Tổng Quan Sự Kiện

- **Tên Sự Kiện:** AWS Cloud & AI Day 2026 (điểm cầu TP.HCM)  
- **Ngày:** 29 tháng 9, 2026  
- **Vai Trò:** Người tham dự  
- **Hình Thức:** Hội nghị trọn ngày, theo dõi keynote và các phiên chuyên đề  
- **Địa Điểm:** Tầng 26, Tòa nhà Bitexco Financial Tower, Quận 1, TP.HCM  
- **Chương trình:** [Agenda sự kiện](https://aws.amazon.com/events/cloud-day-hanoi/agenda/)

AWS Cloud & AI Day là sự kiện thường niên của AWS tại Việt Nam, quy tụ chuyên gia AWS, đối tác và doanh nghiệp để chia sẻ xu hướng mới về cloud và AI. Năm 2026, chủ đề nổi bật nhất là agentic AI: làm sao đưa các AI agent từ bản demo ra vận hành thật, và nền tảng dữ liệu cần có để AI hoạt động đáng tin cậy. Cùng các bạn thực tập sinh FCAJ, tôi theo dõi sự kiện tại tầng 26, văn phòng AWS TP.HCM.

### Các Điểm Nổi Bật Trong Chương Trình

#### Keynote khai mạc
Keynote xoay quanh câu hỏi: cần gì để giải những bài toán mà ai cũng nghĩ là không thể? Các ý chính tôi ghi lại:
- Đổi mới bắt đầu từ vấn đề của khách hàng, không bắt đầu từ công nghệ
- Generative AI và agentic AI đang chuyển từ giai đoạn thử nghiệm sang giai đoạn tạo giá trị thật trong doanh nghiệp
- Hạ tầng toàn cầu và các dịch vụ được quản lý giúp đội nhỏ cũng xây được sản phẩm lớn
- Các doanh nghiệp Việt Nam đang tăng tốc chuyển đổi số trên AWS

#### Agentic AI: từ demo đến triển khai
- **Khoảng cách giữa demo và production:** một agent chạy tốt trong demo chưa chắc đáng tin khi gặp dữ liệu thật; cần kiểm thử, giám sát và giới hạn quyền hạn của agent
- **Agent hỗ trợ phát triển phần mềm:** AI có thể viết code, viết test, review và tài liệu hóa, nhưng con người vẫn chịu trách nhiệm cuối cùng về chất lượng
- **Di chuyển hệ thống quy mô lớn với AI:** giới thiệu cách AWS Transform dùng agent để hỗ trợ hiện đại hóa và di chuyển ứng dụng cũ

#### Nền tảng dữ liệu sẵn sàng cho AI
- Dữ liệu phân tán, thiếu chuẩn hóa là rào cản lớn nhất khi doanh nghiệp đưa AI vào sản phẩm
- Data lake trên Amazon S3 làm nền tảng, kết hợp quản trị dữ liệu (catalog, phân quyền, chất lượng dữ liệu)
- Mô hình AI chỉ tốt bằng dữ liệu đầu vào: cần truy vết được dữ liệu đến từ đâu và được xử lý ra sao

#### Hiện đại hóa ứng dụng và nền tảng kỹ thuật
- Câu chuyện xây dựng Internal Developer Platform trên Amazon EKS của một ngân hàng Việt Nam
- Chọn đúng công cụ cho đúng bài toán: không phải lúc nào cũng cần kiến trúc phức tạp, đôi khi giải pháp đơn giản lại hiệu quả và rẻ hơn
- Giới thiệu Amazon Quick với phần thi tạo trợ lý AI dành cho người tham dự

### Kiến Thức Kỹ Thuật Quan Trọng

- **Agent cần "hàng rào":** quyền IAM tối thiểu, giới hạn phạm vi công cụ (tool), ghi log mọi hành động để kiểm tra lại
- **Đánh giá trước khi tin:** cần bộ câu hỏi kiểm thử cố định để đo chất lượng agent, không chỉ thử vài câu thủ công
- **Data lake nhiều tầng:** tách dữ liệu thô, dữ liệu đã làm sạch và dữ liệu đặc trưng, mỗi tầng có quy tắc rõ ràng
- **Serverless và dịch vụ được quản lý:** giúp đội nhỏ tập trung vào sản phẩm thay vì vận hành máy chủ

### Bài Học Cá Nhân

#### Phát Triển Kỹ Thuật
- Tôi hiểu rõ hơn rằng phần khó của AI trong thực tế nằm ở dữ liệu, kiểm thử và vận hành, không chỉ ở mô hình
- Phần trình bày về đánh giá agent bằng bộ câu hỏi cố định giúp tôi hình dung cách nhóm có thể kiểm thử chatbot của dự án

#### Insights Ngành
- Doanh nghiệp Việt Nam, đặc biệt là ngân hàng và tài chính, đang đầu tư mạnh vào nền tảng dữ liệu và AI
- Nhu cầu về kỹ sư hiểu cả dữ liệu, mô hình và cloud ngày càng lớn

#### Ứng Dụng Cho Dự Án CPAWS
- Thiết kế data lake trên S3 theo tầng `raw/`, `clean/`, `features/`, `models/`, `eval/` để mọi con số trong báo cáo đều truy ngược được
- Chatbot chỉ được lấy con số từ tool, không tự tạo số liệu; đây chính là tinh thần "agent có hàng rào" được nhắc nhiều trong sự kiện
- Chuẩn bị bộ câu hỏi kiểm thử cố định cho chatbot và chỉ chạy chấm điểm chính thức một lần

### Trải Nghiệm Sự Kiện

Đây là sự kiện AWS lớn đầu tiên tôi tham dự kể từ khi bắt đầu thực tập. Không khí rất sôi nổi, và việc được nghe trực tiếp các câu chuyện thực tế giúp tôi thấy những gì đang học trong các Lab có giá trị như thế nào khi áp dụng vào doanh nghiệp. Tôi chủ yếu lắng nghe, ghi chép và theo dõi các phần trình bày; nhiều ghi chú sau đó được tôi đem về thảo luận với nhóm khi thiết kế kiến trúc CPAWS.

Sự kiện cũng giúp tôi tự tin hơn với hướng đi của nhóm: một sản phẩm AI có ích không nhất thiết phải dùng mô hình phức tạp nhất, mà cần dữ liệu đáng tin, cách đánh giá trung thực và hệ thống vận hành ổn định.

### Ảnh Sự Kiện

...
