---
title: "Nhật ký công việc Tuần 4"
date: "2025-09-29T09:00:00+07:00"
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu Tuần 4

- Củng cố kiến thức đã học trong tháng đầu tiên về AWS.  
- Áp dụng các kiến thức cơ bản của AWS để xây dựng một môi trường nhỏ từ đầu.  
- Hiểu cách các thành phần mạng và compute hoạt động phối hợp với nhau.  
- Thực hành truy cập và quản lý tài nguyên ở các lớp mạng khác nhau.

---

### Công việc thực hiện trong tuần này

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Lập kế hoạch:**<br>- Ôn lại các khái niệm đã học từ Tuần 1–3.<br>- Lập kế hoạch kiến trúc đơn giản với thành phần công khai và riêng tư. | 29/09/2025 | 29/09/2025 | |
| 2 | **Thiết lập mạng:**<br>- Tạo VPC tùy chỉnh.<br>- Cấu hình Public và Private Subnet.<br>- Thiết lập Route Tables và Internet Gateway. | 30/09/2025 | 30/09/2025 | |
| 3 | **Thiết lập Compute:**<br>- Khởi chạy instance EC2 trong Public Subnet (Web / Access Point).<br>- Khởi chạy instance EC2 trong Private Subnet (mô phỏng Backend). | 01/10/2025 | 01/10/2025 | |
| 4 | **Kiểm tra kết nối & truy cập:**<br>- SSH vào instance EC2 công khai.<br>- Kiểm tra kết nối từ Public EC2 đến Private EC2.<br>- Xác thực các quy tắc của Security Group. | 02/10/2025 | 02/10/2025 | |
| 5 | **Ôn tập & Dọn dẹp:**<br>- Xem xét hoạt động của kiến trúc và ranh giới bảo mật.<br>- Terminate các instance EC2 và dọn dẹp tài nguyên mạng. | 03/10/2025 | 03/10/2025 | |

---

### Kiến thức bổ sung: Thiết kế truy cập Public vs Private

Qua thực hành trực tiếp, tôi có hiểu biết rõ hơn về cách sử dụng **public và private subnet** trong AWS:

- Tài nguyên trong **Public Subnet** có thể giao tiếp với Internet thông qua Internet Gateway.  
- Tài nguyên trong **Private Subnet** không có truy cập trực tiếp Internet và phải được truy cập gián tiếp.  
- Sự phân tách này rất quan trọng cho bảo mật và thường được sử dụng trong các kiến trúc thực tế, nơi các dịch vụ backend không nên được phơi bày công khai.  

Khái niệm này sau đó trở thành nền tảng để hiểu kiến trúc nhiều tầng (multi-tier) và triển khai backend an toàn.

---

### Thành tựu Tuần 4

- Thành công xây dựng môi trường AWS tùy chỉnh mà không dùng cấu hình mặc định.  
- Tạo và cấu hình VPC với cả subnet công khai và riêng tư.  
- Khởi chạy các instance EC2 trong các lớp mạng khác nhau và xác thực các quy tắc truy cập.  
- Thể hiện khả năng kiểm soát truy cập vào tài nguyên riêng tư thông qua điểm truy cập công khai.  
- Củng cố hiểu biết tổng quan về mạng AWS, ranh giới bảo mật và thiết kế kiến trúc.

---
