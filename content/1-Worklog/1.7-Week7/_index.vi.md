---
title: "Nhật ký công việc Tuần 7"
date: "2025-10-20T09:00:00+07:00"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu Tuần 7

- Bảo mật quyền truy cập mạng ra ngoài cho các tài nguyên private.  
- Học cách quản lý secrets ứng dụng một cách an toàn.  
- Hiểu các biện pháp bảo vệ cơ bản ở lớp ứng dụng bằng AWS WAF.  
- Áp dụng các best practice về bảo mật vào kiến trúc cloud.

---

### Công việc thực hiện trong tuần này

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Khái niệm NAT Gateway:**<br>- Hiểu lý do private subnet cần truy cập internet ra ngoài.<br>- Tìm hiểu kiến trúc và routing của NAT Gateway. | 20/10/2025 | 20/10/2025 | |
| 2 | **AWS Secrets Manager:**<br>- Lưu trữ thông tin đăng nhập cơ sở dữ liệu an toàn.<br>- Học về secret rotation và quản lý quyền truy cập. | 21/10/2025 | 21/10/2025 | |
| 3 | **AWS WAF cơ bản:**<br>- Hiểu các trường hợp sử dụng Web Application Firewall.<br>- Tìm hiểu các biện pháp bảo vệ phổ biến như lọc SQL injection. | 22/10/2025 | 22/10/2025 | |
| 4 | **Thực hành:**<br>- Tạo và cấu hình NAT Gateway.<br>- Cập nhật Route Tables để enable internet cho private subnet.<br>- Xác thực kết nối ra ngoài từ các EC2 private. | 23/10/2025 | 23/10/2025 | |
| 5 | **Ôn tập & Nhận thức chi phí:**<br>- Ôn lại mô hình định giá NAT Gateway.<br>- Phân tích tác động chi phí cho môi trường chạy dài hạn. | 24/10/2025 | 24/10/2025 | |

---

### Kiến thức bổ sung: Bảo mật lưu lượng subnet private

Tuần này nhấn mạnh tầm quan trọng của **quyền truy cập ra ngoài được kiểm soát**:

- Các instance private không nên truy cập trực tiếp từ Internet.  
- NAT Gateway cho phép kết nối ra ngoài trong khi vẫn giữ cách ly inbound.  
- Các dịch vụ bảo mật như Secrets Manager và WAF giảm rủi ro liên quan đến lộ thông tin đăng nhập và tấn công web.

Các mẫu thiết kế này quan trọng để xây dựng hệ thống sản xuất an toàn.

---

### Thành tựu Tuần 7

- Kích hoạt thành công quyền truy cập internet ra ngoài cho các tài nguyên private subnet bằng NAT Gateway.  
- Xác nhận các EC2 private có thể truy cập tài nguyên bên ngoài mà không bị lộ công khai.  
- Lưu trữ thông tin đăng nhập nhạy cảm an toàn bằng AWS Secrets Manager thay vì hardcode.  
- Áp dụng các quy tắc Web Application Firewall cơ bản để bảo vệ các thành phần web-facing.  
- Cải thiện hiểu biết tổng thể về cân bằng bảo mật và chi phí trong kiến trúc AWS.

---
