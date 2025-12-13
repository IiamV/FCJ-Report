---
title: "Nhật ký công việc Tuần 5"
date: "2025-10-06T09:00:00+07:00"
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu Tuần 5

- Hiểu về khả năng mở rộng ứng dụng và độ khả dụng cao trên AWS.  
- Tìm hiểu cách Load Balancer phân phối lưu lượng.  
- Tìm hiểu cách Auto Scaling Groups giúp hệ thống tự phục hồi.  
- Áp dụng khái niệm ứng dụng không trạng thái (stateless) trong kiến trúc cloud.

---

### Công việc thực hiện trong tuần này

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Elastic Load Balancing:**<br>- Tìm hiểu khái niệm Application Load Balancer (ALB).<br>- Hiểu về Target Groups và Health Checks. | 06/10/2025 | 06/10/2025 | |
| 2 | **Auto Scaling Groups (ASG):**<br>- Tìm hiểu Launch Templates.<br>- Hiểu về chính sách scaling (dựa trên CPU, theo lịch). | 07/10/2025 | 07/10/2025 | |
| 3 | **Thực hành:**<br>- Triển khai nhiều EC2 instance phía sau ALB.<br>- Xác minh phân phối lưu lượng giữa các instance. | 08/10/2025 | 08/10/2025 | |
| 4 | **Thực hành:**<br>- Tạo một Auto Scaling Group.<br>- Mô phỏng tải để kích hoạt các sự kiện scale-out và scale-in. | 09/10/2025 | 09/10/2025 | |
| 5 | **Ôn tập & Khái niệm:**<br>- Ôn lại thiết kế ứng dụng không trạng thái.<br>- Hiểu lý do cần stateless cho việc mở rộng theo chiều ngang. | 10/10/2025 | 10/10/2025 | |

---

### Kiến thức bổ sung: Health Checks và Tự phục hồi

Tuần này giới thiệu khái niệm **hệ thống tự phục hồi** trên AWS:

- Load Balancer liên tục kiểm tra sức khỏe các target đã đăng ký.  
- Khi một instance EC2 trở nên không khỏe, lưu lượng tự động được chuyển hướng.  
- Auto Scaling Groups có thể terminate các instance không khỏe và thay thế bằng các instance mới.  

Cơ chế này đảm bảo độ khả dụng cao hơn mà không cần can thiệp thủ công, là nguyên tắc cốt lõi của kiến trúc cloud-native hiện đại.

---

### Thành tựu Tuần 5

- Cấu hình thành công Application Load Balancer để phân phối lưu lượng qua nhiều instance EC2.  
- Tạo và quản lý Target Groups với cấu hình Health Check chính xác.  
- Xây dựng Auto Scaling Group có khả năng điều chỉnh tự động theo tải hệ thống.  
- Quan sát việc thay thế instance tự động trong các kịch bản mô phỏng lỗi.  
- Hiểu tầm quan trọng của thiết kế ứng dụng không trạng thái cho hệ thống frontend và backend có khả năng mở rộng.

---
