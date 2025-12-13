---
title: "Nhật ký công việc Tuần 6"
date: "2025-10-13T09:00:00+07:00"
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu Tuần 6

- Tìm hiểu cách hoạt động của các dịch vụ cơ sở dữ liệu được quản lý trên AWS.  
- Hiểu các khái niệm về độ khả dụng cao và hiệu năng cho cơ sở dữ liệu.  
- Thực hành trực tiếp với Amazon RDS.  
- Tìm hiểu các khái niệm cơ bản về caching sử dụng Amazon ElastiCache.

---

### Công việc thực hiện trong tuần này

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **RDS Fundamentals:**<br>- Tìm hiểu các engine cơ sở dữ liệu được hỗ trợ.<br>- Hiểu về triển khai Multi-AZ và cơ chế failover. | 13/10/2025 | 13/10/2025 | |
| 2 | **RDS Security & Performance:**<br>- Tìm hiểu cấu hình Security Group cho RDS.<br>- Hiểu về Read Replicas và các trường hợp sử dụng. | 14/10/2025 | 14/10/2025 | |
| 3 | **ElastiCache Basics:**<br>- Tìm hiểu các khái niệm caching trong bộ nhớ.<br>- Hiểu về Redis và các chiến lược caching. | 15/10/2025 | 15/10/2025 | |
| 4 | **Thực hành:**<br>- Khởi tạo một database instance trên Amazon RDS.<br>- Cấu hình kết nối từ EC2 tới RDS.<br>- Xác thực cấu hình mạng và bảo mật. | 16/10/2025 | 16/10/2025 | |
| 5 | **Ôn tập & Khái niệm Backup:**<br>- Hiểu về backup tự động và snapshots.<br>- Ôn lại kiến thức cơ bản về disaster recovery cho cơ sở dữ liệu. | 17/10/2025 | 17/10/2025 | |

---

### Kiến thức bổ sung: Độ khả dụng vs Hiệu năng trong cơ sở dữ liệu

Tuần này làm rõ sự khác biệt quan trọng trong thiết kế cơ sở dữ liệu:

- **Multi-AZ deployments** tập trung vào độ khả dụng cao bằng cách sao chép dữ liệu đồng bộ tới một instance dự phòng.  
- **Read Replicas** tập trung cải thiện hiệu năng đọc bằng cách sao chép dữ liệu không đồng bộ tới các instance bổ sung.  

Hiểu được sự khác biệt này giúp củng cố cách thiết kế cơ sở dữ liệu để đáp ứng các yêu cầu ứng dụng khác nhau trong môi trường production.

---

### Thành tựu Tuần 6

- Triển khai thành công cơ sở dữ liệu quan hệ được quản lý bằng Amazon RDS.  
- Có kinh nghiệm kết nối EC2 tới RDS an toàn thông qua Security Groups.  
- Hiểu cách AWS quản lý backup, patching và failover cho cơ sở dữ liệu.  
- Học được cách caching với ElastiCache cải thiện hiệu năng ứng dụng.  
- Xây dựng nền tảng vững chắc hơn để thiết kế các lớp dữ liệu tin cậy và có khả năng mở rộng trong kiến trúc AWS.

---
