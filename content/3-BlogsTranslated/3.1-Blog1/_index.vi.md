---
title: "Tối ưu hóa tiết kiệm chi phí: Lợi thế của Amazon Aurora so với các cơ sở dữ liệu mã nguồn mở tự quản lý"
date: "2025-08-14T10:00:00+07:00"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

> bởi Chris Riggin và Cees Molenaar vào ngày 31 MAR 2025 trong [Advanced (300)](https://aws.amazon.com/blogs/database/category/learning-levels/advanced-300/), [Amazon Aurora](https://aws.amazon.com/blogs/database/category/database/amazon-aurora/), [Best Practices](https://aws.amazon.com/blogs/database/category/post-types/best-practices/), [Permalink](https://aws.amazon.com/blogs/database/optimizing-cost-savings-the-advantage-of-amazon-aurora-over-self-managed-open-source-databases/)

[Amazon Aurora](https://aws.amazon.com/rds/aurora/), một dịch vụ cơ sở dữ liệu quan hệ từ [Amazon Web Services (AWS)](https://aws.amazon.com/), cung cấp hiệu năng cao và độ sẵn sàng vượt trội ở quy mô toàn cầu, khiến nó trở thành lựa chọn ưu tiên cho các tổ chức đang tìm kiếm một giải pháp cơ sở dữ liệu có tính cạnh tranh. Là một cơ sở dữ liệu được xây dựng cho môi trường đám mây, Aurora được thiết kế để tận dụng các khả năng của hạ tầng cloud, mang lại khả năng phục hồi, hiệu năng và khả năng mở rộng ở cấp độ doanh nghiệp cho hai engine cơ sở dữ liệu mã nguồn mở đáng tin cậy nhất: MySQL và PostgreSQL. Một yếu tố then chốt trong thành công của Aurora là kiến trúc đổi mới, với cụm lưu trữ được tách rời và phân phối trên nhiều [Availability Zones](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/), tạo ra một giải pháp độc đáo và có khả năng chịu lỗi cao đồng thời giảm tổng chi phí. Trong khi khách hàng thường kỳ vọng tiết kiệm chi phí khi di chuyển từ các cơ sở dữ liệu thương mại sang Aurora, nhiều tổ chức còn ngạc nhiên khi nhận thấy những lợi ích tài chính tương tự khi chuyển đổi từ MySQL và PostgreSQL tự quản lý.

Trong bài viết này, chúng tôi làm nổi bật các thiết kế kiến trúc thường bị bỏ qua và những tính năng sẵn có của Aurora giúp tối ưu chi phí khi triển khai cơ sở dữ liệu mã nguồn mở. Các phần tiếp theo xem xét nhiều trường hợp sử dụng khác nhau, so sánh các cấu hình cơ sở dữ liệu tự quản lý điển hình và chi phí liên quan với giải pháp tương đương trên Aurora, đồng thời nhấn mạnh các khoản tiết kiệm chi phí tiềm năng và hiệu quả vận hành. Chúng tôi cung cấp các so sánh chi phí chi tiết dựa trên các tiêu chí cụ thể được xác định trong từng trường hợp sử dụng, sử dụng mức giá on-demand tại khu vực US East (Ohio), và các số liệu này có hiệu lực tại thời điểm bài viết được xuất bản. Chúng tôi khuyến khích độc giả tham khảo [AWS pricing calculator](https://calculator.aws/#/) hoặc liên hệ với đội ngũ phụ trách tài khoản của họ để có mức giá hiện tại và phân tích chi phí được cá nhân hóa.

---

## Use case: Fundamental resilient database architecture with scaled reads

Một trong những trường hợp sử dụng phổ biến nhất dựa trên một kiến trúc chịu lỗi cơ bản với khả năng mở rộng cho các truy vấn đọc. Kiến trúc tự quản lý dưới đây bao gồm một primary instance xử lý lưu lượng đọc/ghi, một secondary logical replica đảm nhiệm hai mục đích – cung cấp khả năng high availability với recovery point objective (RPO) thấp đồng thời đóng vai trò là nguồn replication cho các read replica phía sau – và hai instance chỉ đọc, nơi lưu lượng đọc có thể được chuyển hướng và mở rộng theo chiều ngang. Topology này tối ưu khả năng mở rộng đọc bằng cách phân phối workload đọc trên nhiều replica trong khi tránh overhead replication trên primary, vì secondary replica xử lý luồng replication đến các read replica phía sau. Secondary replica có thể được promote thành primary trong các kịch bản failover, duy trì tính liên tục của hoạt động kinh doanh, trong khi các read replica tiếp tục phục vụ lưu lượng đọc, lúc này sẽ replicate từ primary mới được promote. Kiến trúc này chủ động sử dụng secondary instance làm mục tiêu failover, bởi các thực tiễn trong ngành thường tránh sử dụng read replica cho failover trong MySQL và PostgreSQL do nguy cơ replication lag và mất dữ liệu trong quá trình promote. Hạn chế này đòi hỏi phải có thêm một secondary instance, qua đó làm tăng tổng chi phí của giải pháp.

**Self-managed architecture:**
<img src="/images/blogs/DBBLOG-2824_UC1-SM.png" alt="" width="55%">

Kiến trúc [Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html) dưới đây minh họa cách cùng một workload có thể được duy trì với hiệu năng tương đương hoặc tốt hơn, đồng thời đơn giản hóa kiến trúc và giảm tài nguyên, từ đó hạ thấp chi phí. Kiến trúc của Aurora tích hợp các tính năng chuyên biệt được thiết kế để nâng cao độ sẵn sàng và hỗ trợ disaster recovery. Không giống các cơ sở dữ liệu mã nguồn mở tự quản lý truyền thống, nơi việc đạt được RPO bằng 0 thường làm tăng cả chi phí và overhead hiệu năng, Aurora vốn đã cung cấp khả năng failover với RPO bằng 0. Điều này đạt được thông qua thiết kế đổi mới, cho phép các [read replicas](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Replication.html#Aurora.Replication.Replicas) đóng vai trò là [high availability failover targets](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraHighAvailability.html#Concepts.AuroraHighAvailability.Instances) mà không cần cấu hình bổ sung hoặc tác động đến hiệu năng.

**Aurora architecture:**
<img src="/images/blogs/DBBLOG-2824_UC1-AA.png" alt="" width="55%">

Trong trường hợp sử dụng này, lợi thế kiến trúc loại bỏ nhu cầu về một secondary instance chuyên biệt chỉ dành cho mục đích replication khi failover. Do đó, khi di chuyển từ kiến trúc tự quản lý sang Aurora, footprint của cluster được giảm từ bốn node xuống còn ba. Mặc dù việc giảm tài nguyên compute mang lại một số lợi ích về chi phí, đây không phải là nguồn lợi ích kinh tế chính trong kịch bản này. Phần lớn hiệu quả chi phí đến từ [kiến trúc lưu trữ mang tính cách mạng của Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html).

Trái ngược với các thiết lập cơ sở dữ liệu truyền thống, nơi mỗi node thường có hệ thống lưu trữ độc lập chứa một bản sao dữ liệu riêng được replicate từ instance nguồn, Aurora áp dụng một cách tiếp cận hoàn toàn khác. Trong khi kiến trúc tự quản lý không chỉ làm giảm năng lực hiệu năng do overhead replication mà còn làm chi phí lưu trữ tăng gấp bốn lần trong trường hợp sử dụng này, thì tất cả các instance Aurora — cả primary và read replicas — đều kết nối đến cùng một shared storage volume. [Aurora storage cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html) này vốn đã cung cấp khả năng chịu lỗi bằng cách phân phối sáu bản sao dữ liệu trên ba Availability Zones. Khách hàng chỉ bị tính phí cho một bản sao dữ liệu duy nhất, qua đó giảm chi phí lưu trữ trong kiến trúc này từ bốn xuống còn một.

Giả định sử dụng các loại instance tương đương (r6g.xlarge, db.r6g.xlarge) giữa kiến trúc tự quản lý và kiến trúc Aurora, với 2 TB lưu trữ [io2 provisioned input/output operations per second (IOPS) SSD](https://docs.aws.amazon.com/ebs/latest/userguide/provisioned-iops.html#io2-block-express) ở mức 64,000 IOPS trong kiến trúc tự quản lý và [Aurora I/O-Optimized](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html#aurora-storage-type) (một tùy chọn lưu trữ được thiết kế nhằm cải thiện hiệu quả chi phí và tính dự đoán cho các ứng dụng I/O-intensive) cho kiến trúc Aurora, sự khác biệt về chi phí trở nên rõ ràng. Hãy so sánh chi phí hàng tháng của kiến trúc tự quản lý với giải pháp Aurora:

| Architecture    | # DB Nodes (w/ writer) | Instance Type | Storage Size per Volume | # Storage Volumes | Total Cost |
|-----------------|------------------------|---------------|--------------------------|-------------------|------------|
| Self-Managed    | 4                      | r6g.xlarge   | 2 TB                     | 4                 | $15,756.67 |
| Amazon Aurora   | 3                      | db.r6g.xlarge| 2 TB                     | 1                 | $1,939.05  |

Như có thể thấy, kiến trúc tự quản lý, yêu cầu bốn node compute và lưu trữ riêng cho từng node, có tổng chi phí $15,756.67 mỗi tháng. Ngược lại, giải pháp Aurora với ba node compute và lưu trữ hợp nhất có chi phí $1,939.05 mỗi tháng, tương đương mức giảm chi phí 87.7%. Mức giảm này được thúc đẩy bởi kiến trúc lưu trữ hiệu quả của Aurora, loại bỏ nhu cầu về các bản sao lưu trữ dư thừa trong khi vẫn duy trì độ sẵn sàng và hiệu năng cao.

Mặc dù không phải là trọng tâm của blog này, cũng cần đề cập đến [Amazon Relational Database](https://aws.amazon.com/rds/) Service (Amazon RDS), một dịch vụ cơ sở dữ liệu được quản lý giúp đơn giản hóa các tác vụ quản trị. RDS [Multi-AZ with two readable standbys](https://aws.amazon.com/rds/features/multi-az/) loại bỏ nhu cầu về một secondary/standby instance chuyên biệt. Mặc dù tùy chọn triển khai này yêu cầu các storage volume độc lập, mỗi volume chứa một bản sao dữ liệu riêng, nó vẫn cung cấp một giải pháp high-availability mạnh mẽ. Kiến trúc lưu trữ độc đáo của Aurora mang lại các lợi ích tối ưu chi phí bổ sung thông qua mô hình shared storage, đó là lý do chúng tôi tập trung vào các khả năng của Aurora trong bài viết này.

| Architecture    | # DB Nodes (w/ writer) | Instance Type   | Storage Size per Volume | # Storage Volumes | Total Cost   |
|-----------------|------------------------|-----------------|--------------------------|-------------------|--------------|
| Self-Managed    | 4                      | r6g.xlarge      | 2 Tb                     | 4                 | $15,756.67   |
| Amazon Aurora   | 3                      | db.r6g.xlarge   | 2 Tb                     | 1                 | $1,939.05    |

Như có thể thấy, kiến trúc tự quản lý, yêu cầu bốn node compute và lưu trữ riêng cho từng node, có tổng chi phí $15,756.67 mỗi tháng. Ngược lại, giải pháp Aurora với ba node compute và lưu trữ hợp nhất có chi phí $1,939.05 mỗi tháng, tương đương mức giảm chi phí 87.7%. Mức giảm này được thúc đẩy bởi kiến trúc lưu trữ hiệu quả của Aurora, loại bỏ nhu cầu về các bản sao lưu trữ dư thừa trong khi vẫn duy trì độ sẵn sàng và hiệu năng cao.

Mặc dù không phải là trọng tâm của blog này, cũng cần đề cập đến [Amazon Relational Database](https://aws.amazon.com/rds/) Service (Amazon RDS), một dịch vụ cơ sở dữ liệu được quản lý giúp đơn giản hóa các tác vụ quản trị. RDS [Multi-AZ with two readable standbys](https://aws.amazon.com/rds/features/multi-az/) loại bỏ nhu cầu về một secondary/standby instance chuyên biệt. Mặc dù tùy chọn triển khai này yêu cầu các storage volume độc lập, mỗi volume chứa một bản sao dữ liệu riêng, nó vẫn cung cấp một giải pháp high-availability mạnh mẽ. Kiến trúc lưu trữ độc đáo của Aurora mang lại các lợi ích tối ưu chi phí bổ sung thông qua mô hình shared storage, đó là lý do chúng tôi tập trung vào các khả năng của Aurora trong bài viết này.

## Use case: Variable read loads

Bây giờ, hãy xem xét một kịch bản trong đó các ứng dụng trải qua sự biến động về lưu lượng đọc, với các mức tải đỉnh có thể yêu cầu dung lượng lớn hơn đáng kể so với những gì kiến trúc chịu lỗi cơ bản có thể cung cấp một cách hiệu quả. Thiết kế kiến trúc tự quản lý dưới đây xử lý lưu lượng đọc biến động thông qua bốn instance chỉ đọc. Cấu hình này được sizing cho các giai đoạn nhu cầu cao nhất, một thực tiễn tiêu chuẩn trong các kiến trúc truyền thống. Hệ thống sử dụng các tài nguyên được provision sẵn để xử lý các đợt tăng đột biến định kỳ của lưu lượng đọc nhưng lại bị sử dụng không hết trong các giai đoạn vận hành bình thường. Cách tiếp cận tĩnh này đảm bảo khả năng sẵn sàng cho các kịch bản lưu lượng cao, nhưng phải đánh đổi bằng dung lượng nhàn rỗi và chi phí gia tăng trong các giai đoạn không cao điểm, từ đó tạo ra cơ hội để tối ưu hóa chiến lược quản lý cơ sở dữ liệu.

**Self-managed architecture:**
<img src="/images/blogs/DBBLOG-2824_UC2-SM.png" alt="" width="55%">

Trong trường hợp sử dụng này, lợi thế kiến trúc loại bỏ nhu cầu về một secondary instance chuyên biệt chỉ dành cho mục đích replication khi failover. Do đó, khi di chuyển từ kiến trúc tự quản lý sang Aurora, footprint của cluster được giảm từ bốn node xuống còn ba. Mặc dù việc giảm tài nguyên compute mang lại một số lợi ích về chi phí, đây không phải là nguồn lợi ích kinh tế chính trong kịch bản này. Phần lớn hiệu quả chi phí đến từ [kiến trúc lưu trữ mang tính cách mạng của Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html).

Trái ngược với các thiết lập cơ sở dữ liệu truyền thống, nơi mỗi node thường có hệ thống lưu trữ độc lập chứa một bản sao dữ liệu riêng được replicate từ instance nguồn, Aurora áp dụng một cách tiếp cận hoàn toàn khác. Trong khi kiến trúc tự quản lý không chỉ làm giảm năng lực hiệu năng do overhead replication mà còn làm chi phí lưu trữ tăng gấp bốn lần trong trường hợp sử dụng này, thì tất cả các instance Aurora — cả primary và read replicas — đều kết nối đến cùng một shared storage volume. [Aurora storage cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html) này vốn đã cung cấp khả năng chịu lỗi bằng cách phân phối sáu bản sao dữ liệu trên ba Availability Zones. Khách hàng chỉ bị tính phí cho một bản sao dữ liệu duy nhất, qua đó giảm chi phí lưu trữ trong kiến trúc này từ bốn xuống còn một.

Giả định sử dụng các loại instance tương đương (r6g.xlarge, db.r6g.xlarge) giữa kiến trúc tự quản lý và kiến trúc Aurora, với 2 TB lưu trữ [io2 provisioned input/output operations per second (IOPS)](https://docs.aws.amazon.com/ebs/latest/userguide/provisioned-iops.html#io2-block-express) SSD ở mức 64,000 IOPS trong kiến trúc tự quản lý và [Aurora I/O-Optimized](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html#aurora-storage-type) (một tùy chọn lưu trữ được thiết kế nhằm cải thiện hiệu quả chi phí và tính dự đoán cho các ứng dụng I/O-intensive) cho kiến trúc Aurora, sự khác biệt về chi phí trở nên rõ ràng. Hãy so sánh chi phí hàng tháng của kiến trúc tự quản lý với giải pháp Aurora:

| Architecture    | # DB Nodes (w/ writer) | Instance Type | Storage Size per Volume | # Storage Volumes | Total Cost |
|-----------------|------------------------|---------------|--------------------------|-------------------|------------|
| Self-Managed    | 4                      | r6g.xlarge    | 2 Tb                     | 4                 | $15,756.67 |
| Amazon Aurora   | 3                      | db.r6g.xlarge | 2 Tb                     | 1                 | $1,939.05  |


Như chúng ta có thể thấy, kiến trúc tự quản lý, yêu cầu bốn node compute và hệ thống lưu trữ riêng cho từng node, có tổng chi phí là $15,756.67 mỗi tháng. Ngược lại, giải pháp Aurora với ba node compute và lưu trữ hợp nhất chỉ có chi phí $1,939.05 mỗi tháng, tương đương mức giảm chi phí 87.7%. Mức giảm này đến từ kiến trúc lưu trữ hiệu quả của Aurora, giúp loại bỏ nhu cầu về các bản sao lưu trữ dư thừa trong khi vẫn duy trì độ sẵn sàng cao và hiệu năng ổn định.

Mặc dù không phải là trọng tâm của blog này, cũng cần đề cập đến [Amazon Relational Database Service](https://aws.amazon.com/rds/) (Amazon RDS), một dịch vụ cơ sở dữ liệu được quản lý giúp đơn giản hóa các tác vụ quản trị. RDS [Multi-AZ with two readable standbys](https://aws.amazon.com/rds/features/multi-az/) loại bỏ nhu cầu về một instance secondary/standby chuyên biệt. Mặc dù tùy chọn triển khai này yêu cầu các storage volume độc lập, mỗi volume chứa một bản sao dữ liệu riêng, nó vẫn cung cấp một giải pháp high-availability vững chắc. Kiến trúc lưu trữ độc đáo của Aurora mang lại các lợi ích tối ưu chi phí bổ sung thông qua mô hình shared storage, đó là lý do chúng tôi tập trung vào các khả năng của Aurora trong bài viết này.

## Use case: Variable read loads

Bây giờ, hãy xem xét một kịch bản trong đó các ứng dụng trải qua sự biến động về lưu lượng đọc, với các mức tải đỉnh có thể yêu cầu dung lượng lớn hơn đáng kể so với những gì kiến trúc chịu lỗi cơ bản có thể cung cấp một cách hiệu quả. Thiết kế kiến trúc tự quản lý dưới đây xử lý lưu lượng đọc biến động thông qua bốn instance chỉ đọc. Cấu hình này được sizing cho các giai đoạn nhu cầu cao nhất, một thực tiễn tiêu chuẩn trong các kiến trúc truyền thống. Hệ thống sử dụng các tài nguyên được provision sẵn để xử lý các đợt tăng đột biến định kỳ của lưu lượng đọc nhưng lại bị sử dụng không hết trong các giai đoạn vận hành bình thường. Cách tiếp cận tĩnh này đảm bảo khả năng sẵn sàng cho các kịch bản lưu lượng cao, nhưng phải đánh đổi bằng dung lượng nhàn rỗi và chi phí gia tăng trong các giai đoạn không cao điểm, từ đó tạo ra cơ hội để tối ưu hóa chiến lược quản lý cơ sở dữ liệu.

<img src="/images/blogs/DBBLOG-2824_UC2-SM (1).png" alt="" width="55%">

Hình dưới đây minh họa kiến trúc Aurora cho trường hợp sử dụng này. Giống như trường hợp sử dụng đầu tiên, chúng ta có thể chứng minh tiết kiệm chi phí thông qua những thay đổi chiến lược trong thiết kế kiến trúc vốn đã có sẵn trong Aurora. Nhờ kiến trúc shared storage của Aurora và khả năng bất kỳ replica nào cũng có thể được promote thành writer mà không mất dữ liệu (zero RPO), chúng ta có thể loại bỏ instance secondary chuyên biệt vốn cần thiết cho high availability. Sau khi failover giữa các Availability Zone, Aurora tự động tạo một read replica thay thế để thay thế primary instance trước đó, duy trì dung lượng mong muốn cho cluster.

Giống như hầu hết các trường hợp sử dụng, kiến trúc read replica của Aurora cho phép mở rộng đọc hiệu quả hơn thông qua mô hình shared storage, loại bỏ nhu cầu replication dữ liệu giữa các instance. Trong trường hợp sử dụng này, hiệu quả được bổ sung bởi tính năng [Amazon Aurora Auto Scaling với Aurora Replicas](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Integrating.AutoScaling.html), một giải pháp động thay thế cho việc duy trì các instance chỉ đọc liên tục. Khi auto-scaling được cấu hình, hệ thống tự động provision và deprovision các instance dựa trên nhu cầu workload, và có thể scale down xuống một read replica duy nhất trong các giai đoạn hoạt động thấp. Cách tiếp cận thích ứng này tối ưu hóa việc sử dụng tài nguyên, giảm chi phí liên quan đến dung lượng nhàn rỗi, và đảm bảo bạn chỉ trả cho những tài nguyên cần thiết.

**Aurora architecture:**
<img src="/images/blogs/DBBLOG-2824_UC2-AAv1.png" alt="" width="55%">

Hãy áp dụng các giả định tương tự như trong trường hợp sử dụng đầu tiên: loại và kích thước instance tương đương (r6g.xlarge, db.r6g.xlarge), với kiến trúc tự quản lý sử dụng 2 TB lưu trữ [io2 provisioned IOPS SSD](https://docs.aws.amazon.com/ebs/latest/userguide/provisioned-iops.html#io2-block-express) ở mức 64,000 IOPS, và kiến trúc Aurora sử dụng [Aurora I/O-Optimized](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html#aurora-storage-type). Chúng tôi chọn Aurora I/O-Optimized cho kịch bản này vì khả năng xử lý các workload giao dịch hiệu năng cao với I/O độ trễ thấp ổn định, phù hợp với yêu cầu mà lưu trữ io2 trong cấu hình tự quản lý gợi ý.

Hãy so sánh chi phí hàng tháng của cluster tự quản lý với các instance cố định và kiến trúc Aurora với auto-scaling được bật:

| Architecture    | Avg # DB Nodes (w/ writer) | Instance Type                         | Storage Size per Volume | # Storage Volumes | Total Cost   |
|-----------------|----------------------------|---------------------------------------|--------------------------|-------------------|--------------|
| Self-Managed    | 6                          | r6g.xlarge                            | 2 Tb                     | 6                 | $23,635.01   |
| Amazon Aurora   | 4                          | db.r6g.xlarge (w/ Auto-Scaled Reads)  | 2 Tb                     | 1                 | $2,431.80    |

Như minh họa ở trên, cluster truyền thống với 6 instance r6g.xlarge và các chi phí lưu trữ liên quan có tổng chi phí $23,635.01 mỗi tháng. Ngược lại, kiến trúc Aurora, ngay cả khi duy trì một writer cộng với trung bình ba trong bốn read replica được provision chủ động trong suốt một tháng, chỉ có chi phí $2,431.80 mỗi tháng—tương đương giảm 89.7%. Điều này chứng minh lợi ích tài chính của khả năng auto-scaling của Aurora. Những tiết kiệm này tăng lên khi giảm số trung bình read replica hoạt động hàng tháng. Ví dụ, scale down xuống trung bình hai read replica làm giảm chi phí thêm, với mức tiết kiệm vượt quá 90%. Trong các giai đoạn hoạt động thấp, scale xuống trung bình một read replica giảm chi phí hơn nữa, chứng tỏ tính linh hoạt và hiệu quả chi phí của kiến trúc Aurora.

Như một kiến trúc thay thế cho trường hợp sử dụng này, [Amazon Aurora Serverless v2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.html) cung cấp giải pháp linh hoạt để xử lý các mô hình lưu lượng không dự đoán trước, tự động điều chỉnh cả dung lượng đọc và ghi theo thời gian thực để đáp ứng nhu cầu biến động.

**Alternative Aurora architecture:**
<img src="/images/blogs/DBBLOG-2824_UC2-AAv2.png" alt="" width="55%">

[Aurora Serverless v2](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless-v2.html) cung cấp giải pháp tối ưu để quản lý các workload biến động, đảm bảo sử dụng tài nguyên hiệu quả mà không ảnh hưởng đến hiệu năng. Kiến trúc đổi mới này, được minh họa trong hình trên, phân bổ chỉ những tài nguyên cần thiết, duy trì tốc độ và hiệu năng nhất quán bất kể biến động về nhu cầu. Trái ngược với kiến trúc tự quản lý với sáu instance provision sẵn, cấu hình Aurora tối ưu này có thể giảm lượng tài nguyên trung bình hàng tháng cho cả read replica và primary instance. Cấu hình tinh gọn này không chỉ đơn giản hóa quản lý mà còn tăng hiệu quả chi phí. Trong các giai đoạn ít hoặc không có nhu cầu, Aurora Serverless v2 hỗ trợ [scale capacity xuống 0](https://aws.amazon.com/blogs/database/introducing-scaling-to-0-capacity-with-amazon-aurora-serverless-v2/), giảm chi phí. Hơn nữa, Aurora cho phép kết hợp serverless và provisioned instance trong cùng một cluster, cho phép điều chỉnh chính xác tài nguyên để đáp ứng các yêu cầu workload khác nhau. Cách tiếp cận thích ứng này giúp cân bằng giữa hiệu năng và chi phí, tiết kiệm đáng kể ngân sách đồng thời đảm bảo ứng dụng luôn phản hồi nhanh và hiệu quả.

## Use case: Optimized reads

Trong trường hợp sử dụng này, chúng tôi sẽ làm nổi bật các workload đọc nặng liên tục, nơi auto-scaling có thể không phải là giải pháp hiệu quả về chi phí do tính chất tĩnh của workload. Trong kiến trúc tự quản lý cho trường hợp này (hình dưới đây), ứng dụng yêu cầu các instance đọc lớn (ví dụ: r6g.8xlarge) để đáp ứng yêu cầu hiệu năng cụ thể. Mỗi instance cung cấp 256 GB bộ nhớ, đủ khả năng đáp ứng yêu cầu cache dữ liệu độ trễ thấp và join các bảng lớn trong bộ nhớ của trường hợp sử dụng này. Dung lượng bộ nhớ 256 GB cho phép giữ dữ liệu truy cập thường xuyên trong RAM, giảm I/O operations và cải thiện thời gian phản hồi truy vấn. Ngoài ra, nó cung cấp không gian để thực hiện các truy vấn phức tạp liên quan đến join bảng lớn mà không phải dùng đến disk-based operations, vốn làm chậm hiệu năng. Cấu hình này đảm bảo cơ sở dữ liệu có thể xử lý các thao tác đọc nặng, duy trì khả năng phản hồi ngay cả dưới workload phân tích lớn.

**Self-managed architecture:**
<img src="/images/blogs/DBBLOG-2824_UC3-SM.png" alt="" width="55%">

AWS đã giới thiệu [Amazon Aurora Optimized Reads](https://aws.amazon.com/blogs/database/new-amazon-aurora-optimized-reads-for-aurora-postgresql-with-up-to-8x-query-latency-improvement-for-i-o-intensive-applications/) để giải quyết các thách thức về hiệu năng cho các thao tác cơ sở dữ liệu in-memory, đọc nhiều. Tính năng này được sử dụng trong kiến trúc minh họa dưới đây và cung cấp cải thiện cho Aurora, giúp giảm độ trễ truy vấn lên đến tám lần và tiềm năng tiết kiệm chi phí cho các ứng dụng xử lý tập dữ liệu lớn vượt quá dung lượng bộ nhớ của instance. Ngoài ra, Aurora hỗ trợ Optimized Reads trên các instance được trang bị bộ nhớ NVMe gắn cục bộ, cho phép bật tính năng này để nâng cao hiệu năng đọc. Tham khảo [Overview of Aurora Optimized Reads in PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.optimized.reads.html#AuroraPostgreSQL.optimized.reads.overview) để biết thêm chi tiết, và [Use cases for Aurora Optimized Reads](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/AuroraPostgreSQL.optimized.reads.html#AuroraPostgreSQL.optimized.reads.usecases) để xem danh sách ví dụ ứng dụng.

**Aurora architecture:**
<img src="/images/blogs/DBBLOG-2824_UC3-AA.png" alt="" width="55%">

Trong ví dụ trường hợp sử dụng này, chúng tôi minh họa cách Aurora Optimized Reads với bộ nhớ NVMe cục bộ có thể hỗ trợ giảm kích thước read-only instance. Mặc dù Aurora hỗ trợ các kích thước instance tương đương kiến trúc tự quản lý (db.r6g.8xlarge, db.r6gd.8xlarge), ví dụ này tập trung vào tối ưu chi phí bằng cách sử dụng các instance nhỏ hơn. Cụ thể, chúng tôi chuyển từ r6g.8xlarge (32 vCPU, 256 GB bộ nhớ) sang db.r6gd.2xlarge (8 vCPU, 64 GB bộ nhớ). Thay đổi loại instance này khả thi nhờ Aurora Optimized Reads cung cấp 64 GB bộ nhớ chính cộng với 474 GB bộ nhớ cache dựa trên NVMe—tổng cộng 538 GB, hơn gấp đôi 256 GB có sẵn của instance 8xlarge ban đầu. Cách tiếp cận này đặc biệt hiệu quả với các cơ sở dữ liệu mã nguồn mở đọc nhiều khi bộ dữ liệu làm việc có các đặc điểm sau:

1. Dữ liệu "hot" (truy cập thường xuyên) chiếm 20-30% dung lượng bộ nhớ ban đầu (khoảng 50-75 GB trong trường hợp này).  
2. Bộ dữ liệu làm việc toàn bộ lớn gấp 2-3 lần bộ nhớ ban đầu (500-750 GB).  
3. Mẫu đọc thể hiện tính local theo thời gian, với 80-90% đọc tập trung vào dữ liệu hot.

Trong các trường hợp như vậy, 64 GB bộ nhớ chính có thể cache hiệu quả dữ liệu quan trọng nhất, trong khi 474 GB NVMe hoạt động như một phần mở rộng bộ nhớ hiệu năng cao, bù đắp cho việc giảm bộ nhớ chính. Tuy nhiên, cần lưu ý rằng cấu hình này giảm số lượng CPU từ 32 vCPU xuống còn 8 vCPU cho mỗi instance. Mặc dù kiến trúc bộ nhớ và lưu trữ có thể đáp ứng yêu cầu workload, nên thực hiện kiểm tra hiệu năng cẩn thận để đảm bảo CPU giảm vẫn đáp ứng yêu cầu workload cụ thể của bạn.

Hãy xem xét chi phí hàng tháng khi so sánh kiến trúc truyền thống với Aurora có bật Optimized Reads, duy trì các giả định cấu hình giống các ví dụ trước:

| Architecture    | # DB Nodes (w/ writer) | Instance Type                           | Storage Size per Volume | # Storage Volumes | Total Cost   |
|-----------------|------------------------|-----------------------------------------|--------------------------|-------------------|--------------|
| Self-Managed    | 5                      | r6g.8xlarge                             | 2 Tb                     | 5                 | $24,846.72   |
| Amazon Aurora   | 4                      | db.r6gd.2xlarge (w/ Optimized Reads)    | 2 Tb                     | 1                 | $5,197.04    |

Như đã trình bày ở trên, kiến trúc truyền thống với các instance r6g.8xlarge có chi phí $24,846.72 mỗi tháng, trong khi kiến trúc Aurora với các instance db.r6gd.2xlarge và tính năng Optimized Reads chỉ có chi phí $5,197.04 mỗi tháng — tương đương mức giảm chi phí 79.1%. Những tiết kiệm này đạt được trong khi vẫn duy trì hoặc cải thiện hiệu năng và mức độ trễ yêu cầu nhờ vào cơ chế phân tầng bộ nhớ hiệu quả và khả năng đọc tối ưu của Aurora, đặc biệt với các workload phù hợp với các mẫu truy cập được mô tả ở trên.

## Các tính năng tối ưu chi phí bổ sung của Amazon Aurora

Khi kết thúc phần thảo luận về khả năng tối ưu chi phí của Aurora, việc xem xét một số tính năng bổ sung là rất giá trị. Trong phần này, chúng tôi sẽ giới thiệu từng chức năng, nhấn mạnh lợi ích và ưu điểm vận hành. Những tính năng này không chỉ thể hiện cách tiếp cận toàn diện của Aurora trong quản lý cơ sở dữ liệu mà còn đóng góp vào tiết kiệm chi phí so với các giải pháp tự quản lý.

**Aurora Global Database**

[Aurora Global Database](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html) mở rộng khả năng của Aurora trên nhiều AWS Region, cho phép các ứng dụng đọc dữ liệu với độ trễ thấp tại địa phương và cung cấp khả năng phục hồi thảm họa cho các sự cố trên toàn vùng. Trong các triển khai MySQL và PostgreSQL truyền thống, thiết lập đa vùng yêu cầu giải pháp sao chép tùy chỉnh, nhiều bản sao dữ liệu tại mỗi vùng, và bảo trì hạ tầng phức tạp liên tục. Aurora Global Database loại bỏ các yêu cầu này thông qua sao chép liên vùng tích hợp với thời gian trễ dưới 1 giây, trong khi chỉ tính phí cho khối lượng dữ liệu thực tế một lần mỗi vùng.

Aurora tự động hóa sao chép liên vùng và cung cấp failover vùng hoàn toàn tự động khi được người dùng khởi tạo. Tự động hóa này, kết hợp với khả năng mở rộng độc lập dung lượng đọc tại mỗi vùng, giảm cả chi phí vận hành và hạ tầng so với các giải pháp tự quản lý. Các thiết lập truyền thống thường yêu cầu hạ tầng đồng bộ giữa các vùng và nhiều bản sao dữ liệu để đảm bảo high availability, dẫn đến over-provisioning và tăng chi phí. Kiến trúc của Aurora Global Database loại bỏ những chi phí bổ sung này trong khi vẫn duy trì độ sẵn sàng cao và khả năng phục hồi thảm họa.

## Aurora cloning và triển khai blue/green

Cloning cơ sở dữ liệu và triển khai blue/green là các thực tiễn trong ngành để tạo bản sao cơ sở dữ liệu và quản lý cập nhật với rủi ro tối thiểu. Aurora thực hiện các khái niệm này với các ưu điểm riêng so với môi trường MySQL và PostgreSQL tự quản lý.

[Aurora cloning](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Managing.Clone.html) tạo các instance cơ sở dữ liệu mới bằng giao thức copy-on-write ở lớp lưu trữ, nơi các instance mới ban đầu trỏ đến cùng dữ liệu gốc với instance nguồn. Cách tiếp cận này giảm chi phí lưu trữ bổ sung bằng cách chỉ lưu các thay đổi gia tăng, đồng thời loại bỏ tác động hiệu năng đến cơ sở dữ liệu nguồn. Ngược lại, các giải pháp tự quản lý thường yêu cầu sao chép toàn bộ dữ liệu tốn thời gian và phát sinh chi phí lưu trữ bổ sung.

[Tính năng triển khai Aurora blue/green](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/blue-green-deployments.html) cho phép cập nhật cơ sở dữ liệu và thay đổi schema với gián đoạn tối thiểu, thường chỉ vài giây trong quá trình cutover cuối cùng. Nếu phát hiện vấn đề trong cửa sổ chuyển đổi ngăn cản việc hoàn tất chuyển sang môi trường green, tính năng này cho phép rollback ngay lập tức về môi trường blue. Mặc dù triển khai tạo ra cơ sở hạ tầng compute trùng lặp, nó sử dụng cùng giao thức copy-on-write như Aurora cloning, nghĩa là môi trường green ban đầu trỏ đến cùng dữ liệu gốc với môi trường blue và chỉ lưu các thay đổi gia tăng. Cập nhật cơ sở dữ liệu trong môi trường tự quản lý truyền thống thường yêu cầu downtime kéo dài hoặc cấu hình replication phức tạp, dẫn đến chi phí bổ sung.

Aurora cloning và triển khai blue/green cùng nhau nâng cao hiệu quả quản lý, tăng tốc chu kỳ phát triển và tối ưu hóa sử dụng tài nguyên, giảm rủi ro vận hành so với triển khai tự quản lý đồng thời cung cấp giải pháp quản lý cho cập nhật cơ sở dữ liệu.

## Aurora zero-ETL integration

Quy trình Extract, Transform, and Load (ETL) tích hợp dữ liệu giữa cơ sở dữ liệu vận hành và các nền tảng phân tích. Các triển khai MySQL và PostgreSQL truyền thống yêu cầu hạ tầng capture dữ liệu thay đổi (CDC) tùy chỉnh, đội ngũ phát triển chuyên trách cho pipeline, tài nguyên compute bổ sung để trích xuất dữ liệu, giám sát liên tục dòng dữ liệu, và can thiệp thủ công cho thay đổi schema.

[Aurora zero-ETL integration](https://aws.amazon.com/rds/aurora/zero-etl/) tự động hóa việc di chuyển dữ liệu giữa Aurora và các dịch vụ phân tích AWS được hỗ trợ (như [Amazon Redshift](https://aws.amazon.com/pm/redshift/)), cung cấp CDC tích hợp, đồng bộ schema, cập nhật dữ liệu gần như thời gian thực, và tích hợp gốc với các dịch vụ phân tích AWS. Cách tiếp cận này giảm chi phí hạ tầng CDC, loại bỏ chi phí phát triển pipeline, giảm tài nguyên compute cho việc di chuyển dữ liệu, và giảm chi phí vận hành trên Aurora. Tích hợp này duy trì sự tách biệt rõ ràng giữa workload vận hành và phân tích, với các truy vấn phân tích chạy trên nền tảng đích thay vì Aurora, bảo toàn hiệu năng cơ sở dữ liệu trong khi cho phép truy cập phân tích kịp thời. Những tính năng này kết hợp giúp giảm cả chi phí hạ tầng và vận hành so với việc duy trì các khả năng tương tự trong môi trường tự quản lý.

## Kết luận

Trong bài viết này, chúng tôi đã tiết lộ những lợi thế về chi phí của việc triển khai và di chuyển sang Amazon Aurora. Phân tích của chúng tôi đã thách thức giả định phổ biến rằng các giải pháp mã nguồn mở tự quản lý cung cấp giá trị kinh tế tối đa. Thông qua các trường hợp sử dụng cụ thể, chúng tôi chứng minh cách các tính năng độc đáo của Aurora giảm chi phí trên nhiều kịch bản. Thiết kế sáng tạo kết hợp khả năng linh hoạt cấp thương mại với mức giá cạnh tranh cho các tổ chức tối ưu hóa hoạt động cơ sở dữ liệu. Khi nhu cầu cơ sở dữ liệu phát triển, Aurora cung cấp cả hiệu năng và hiệu quả chi phí.

Chúng tôi khuyến khích các doanh nghiệp xem xét lại thiết kế và hoạt động cơ sở dữ liệu hiện tại, và khám phá cách Aurora có thể nâng cao hiệu quả vận hành và lợi nhuận. Để được đánh giá cá nhân hóa về tiềm năng tiết kiệm khi di chuyển sang Aurora, liên hệ với [AWS account teams và Partners](https://aws.amazon.com/premiumsupport/).

Để biết thêm thông tin về giá cả và xem ví dụ ước tính chi phí, hãy tham khảo [Amazon Aurora Pricing](https://aws.amazon.com/rds/aurora/pricing/).

Bạn có thể bắt đầu sử dụng Amazon Aurora ngay hôm nay bằng cách truy cập [Amazon RDS console](https://console.aws.amazon.com/rds/home). Để biết thêm chi tiết, xem [Aurora User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html).

---

## Về tác giả

| ![Chris Riggin](/images/blogs/DBBLOG-2824_Chris_Bio-1.jpg) | **Chris Riggin** là Chuyên gia Kiến trúc giải pháp cấp cao tại Amazon Web Services. Ông cam kết giúp khách hàng tối đa hóa tiết kiệm chi phí bằng cách thiết kế các kiến trúc hiệu năng cao, chịu lỗi và có khả năng mở rộng sử dụng dịch vụ AWS. |
| --- | --- |

| ![Cees Molenaar](/images/blogs/DBBLOG-2824_Cees_Bio.jpg) | **Cees Molenaar** là Đại diện Bán hàng cấp cao tại Amazon Web Services. Ông đam mê xây dựng quan hệ đối tác lâu dài với khách hàng bằng cách cung cấp hướng dẫn chuyên môn và hỗ trợ đáng tin cậy trong việc sử dụng dịch vụ AWS. |
| --- | --- |
