---
title: "Nhật ký công việc Tuần 3"
date: "2025-09-22T09:00:00+07:00"
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu Tuần 3

- Củng cố hiểu biết về các dịch vụ cốt lõi Compute và Storage của AWS.  
- Tìm hiểu cách dữ liệu được lưu trữ và duy trì trên AWS.  
- Thực hành triển khai các workload đơn giản sử dụng EC2 và S3.  
- Hiểu các lựa chọn lưu trữ phù hợp cho từng trường hợp sử dụng ứng dụng.

---

### Công việc thực hiện trong tuần này

| Ngày | Công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Kiến thức cơ bản EC2 (Nghiên cứu chuyên sâu):**<br>- Ôn lại các nhóm instance EC2 và trường hợp sử dụng.<br>- Hiểu cách lựa chọn AMI.<br>- Tìm hiểu về key pair và khái niệm cơ bản User Data. | 22/09/2025 | 22/09/2025 | https://docs.aws.amazon.com/ec2/ |
| 2 | **EBS – Lưu trữ block:**<br>- Tìm hiểu các loại volume EBS và đặc tính hiệu suất.<br>- Thực hành gắn và tháo volume EBS.<br>- Hiểu về snapshots và khái niệm backup cơ bản. | 23/09/2025 | 23/09/2025 | |
| 3 | **S3 – Lưu trữ đối tượng:**<br>- Tìm hiểu về bucket và object trong S3.<br>- Hiểu các storage class và lifecycle policy.<br>- Tìm hiểu về bucket policy và quyền truy cập cơ bản. | 24/09/2025 | 24/09/2025 | |
| 4 | **Thực hành Lab:**<br>- Khởi chạy một instance EC2 và triển khai dịch vụ web đơn giản.<br>- Tạo một S3 bucket và upload file tĩnh.<br>- Kiểm tra truy cập nội dung tĩnh trên S3. | 25/09/2025 | 25/09/2025 | https://cloudjourney.awsstudygroup.com/ |
| 5 | **Ôn tập & So sánh:**<br>- So sánh các trường hợp sử dụng EC2 + EBS và S3.<br>- Xác định lựa chọn lưu trữ phù hợp cho frontend assets và dữ liệu backend. | 26/09/2025 | 26/09/2025 | |

---

### Kiến thức bổ sung: Lựa chọn loại lưu trữ phù hợp

Tuần này giúp tôi phân biệt rõ ràng giữa **block storage và object storage**:

- **EBS** được thiết kế cho các workload cần đĩa lưu trữ gắn với instance EC2, chẳng hạn như hệ điều hành và cơ sở dữ liệu.  
- **S3** được thiết kế cho lưu trữ đối tượng có khả năng mở rộng và bền vững, lý tưởng cho các tài nguyên tĩnh như file frontend, hình ảnh, và backup.  

Sự phân biệt này trở nên quan trọng khi lập kế hoạch cách ứng dụng frontend có thể được hosting và phân phối hiệu quả trên AWS.

---

### Thành tựu Tuần 3

- Nắm vững hơn về Amazon EC2, bao gồm việc lựa chọn instance và sử dụng AMI.  
- Thành công gắn và quản lý các volume EBS, củng cố khái niệm lưu trữ bền vững.  
- Học cách sử dụng snapshots cho mục đích backup và phục hồi.  
- Tạo và quản lý các bucket Amazon S3 và upload nội dung tĩnh.  
- Hiểu sự khác biệt thực tiễn giữa **EBS và S3**, đặc biệt trong bối cảnh hosting frontend assets so với dữ liệu backend.  
- Tăng sự tự tin trong việc triển khai các workload đơn giản sử dụng dịch vụ compute và storage cốt lõi của AWS.

---
