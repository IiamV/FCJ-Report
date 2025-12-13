---
title: "Nhật ký công việc Tuần 8"
date: "2025-10-27T09:00:00+07:00"
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu Tuần 8

- Hoàn thiện kiến trúc tổng thể cho dự án cuối kỳ.  
- Ghi chép thiết kế mạng, phạm vi IP và các quy tắc bảo mật.  
- Chuẩn bị kế hoạch triển khai rõ ràng trước khi thực hiện.

---

### Công việc thực hiện trong tuần này

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 1 | **Vẽ sơ đồ kiến trúc:**<br>- Thiết kế kiến trúc High Availability (HA) đầy đủ.<br>- Đảm bảo triển khai Multi-AZ cho các thành phần quan trọng. | 27/10/2025 | 27/10/2025 | |
| 2 | **Lập kế hoạch địa chỉ IP:**<br>- Xác định phạm vi CIDR của VPC (ví dụ: 10.0.0.0/16).<br>- Phân bổ subnets cho các lớp Public và Private. | 28/10/2025 | 28/10/2025 | |
| 3 | **Thiết kế bảo mật:**<br>- Ghi chép các quy tắc của Security Group.<br>- Áp dụng Nguyên tắc Quyền ít nhất (Principle of Least Privilege). | 29/10/2025 | 29/10/2025 | |
| 4 | **Rà soát thiết kế:**<br>- Kiểm tra kiến trúc để phát hiện bottleneck và rủi ro.<br>- Xác thực khả năng mở rộng và chịu lỗi. | 30/10/2025 | 30/10/2025 | |
| 5 | **Chuẩn bị cuối cùng:**<br>- Tạo checklist triển khai.<br>- Xác nhận sẵn sàng cho giai đoạn thực hiện. | 31/10/2025 | 31/10/2025 | |

---

### Kiến thức bổ sung: Giảm rủi ro lan truyền (Blast Radius)

Một nguyên tắc thiết kế quan trọng áp dụng tuần này là **Giảm Blast Radius**. Bằng cách phân tán các thành phần ứng dụng qua nhiều Availability Zone:

- Sự cố ở một AZ không làm sập toàn bộ hệ thống.  
- Load Balancer tự động định tuyến traffic tới các instance còn hoạt động.  
- Hệ thống tuân thủ best practice về độ tin cậy của AWS Well-Architected Framework.

Cách tiếp cận này tăng đáng kể khả năng chịu lỗi cho các workload sản xuất.

---

### Thành tựu Tuần 8

- Hoàn thành sơ đồ kiến trúc High Availability chi tiết cho dự án cuối kỳ.  
- Xác định rõ ràng phạm vi địa chỉ IP và ranh giới subnet.  
- Ghi chép các quy tắc Security Group nghiêm ngặt và tối thiểu để cải thiện bảo mật.  
- Chuẩn bị checklist triển khai có cấu trúc để đảm bảo thực hiện suôn sẻ trong các tuần tiếp theo.

---
