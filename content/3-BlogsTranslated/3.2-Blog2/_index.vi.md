---
title: "Hiện đại hóa Workload MES bằng Containers: Bộ Werum PAS-X MES của Körber trên AWS Outposts"
date: "2025-08-14T10:00:00+07:00"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

> by Vera Xia and Steve Kim on 31 MAR 2025 in [Announcements](https://aws.amazon.com/blogs/developer/category/post-types/announcements/), AWS IoT Core, AWS SDK for Swift, Developer Tools, Intermediate (200), Internet of Things Permalink  Share
>
> Brianna Rosentrater, Hybrid Edge Specialist SA – AWS
> Doruk Ozturk, Sr. Containers Specialist SA – AWS
> Rohit Jagetia, Sr. GLS Solutions Architect – AWS
> Krishna Rajeesh, Principal Cloud Consultant – Körber Pharma Software GmbH

Các nhà sản xuất trong lĩnh vực khoa học sự sống đang đối mặt với thách thức trong việc duy trì hoạt động sản xuất ổn định đồng thời đáp ứng các yêu cầu tuân thủ nghiêm ngặt. Nhiều doanh nghiệp cần vận hành hệ thống Manufacturing Execution Systems (MES) tại chỗ (on-premises), nhưng vẫn mong muốn tận dụng các lợi ích vận hành từ các dịch vụ cloud được quản lý và duy trì một software stack nhất quán giữa môi trường on-premises và cloud. Bài viết này tập trung vào việc triển khai Werum PAS-X MES của Körber trên [AWS Outposts racks](https://aws.amazon.com/outposts/rack/?nc=sn&loc=2). Bạn có thể chạy MES trên một Outpost logic duy nhất, sử dụng nhiều Outposts (nhiều Outposts [Amazon Resource Names (ARN)](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference-arns.html)), hoặc một multi-rack Outpost (nhiều rack dưới một Outposts ARN duy nhất) nhằm đảm bảo high availability. Outposts mở rộng một hoặc nhiều Amazon VPC vào mạng on-premises, đồng thời cho phép kết nối với nhiều dịch vụ AWS hơn trong [AWS home Region](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/) được liên kết. Khách hàng có thể sử dụng AWS APIs, công cụ và các cơ chế bảo mật để vận hành, quản lý và bảo vệ ứng dụng. AWS chịu trách nhiệm bảo trì hạ tầng Outposts, các dịch vụ AWS và việc vá lỗi phần mềm tương tự như trong môi trường Region. Outposts được thiết kế như một giải pháp có kết nối, tuy nhiên bằng cách sử dụng nhiều Outposts với các kết nối [service link](https://docs.aws.amazon.com/outposts/latest/userguide/region-connectivity.html) dự phòng, bạn có thể lập kế hoạch ứng phó với sự cố mạng hoặc dịch vụ nhằm tránh downtime.

---

## Tổng quan

[Bộ Körber’s Werum PAS-X MES Suite](https://www.koerber-pharma.com/en/solutions/software/werum-pas-x-mes-suite#:~:text=PAS%2DX%20MES%20covers%20the,and%20commercial%20production%20to%20packaging.) là một hệ thống quản lý sản xuất cho phép các khách hàng trong lĩnh vực khoa học sự sống giảm tỷ lệ lỗi và chi phí sản xuất. PAS-X MES bao phủ toàn bộ chu trình sản xuất của các ngành dược phẩm, công nghệ sinh học, cũng như cell & gene therapy — từ phát triển quy trình, sản xuất thương mại cho đến đóng gói. MES Suite bao gồm đầy đủ các chức năng quan trọng để hỗ trợ hoạt động sản xuất một cách linh hoạt. Khi triển khai PAS-X MES trên Outposts, khách hàng có thể rút ngắn time-to-market, nâng cao hiệu quả, duy trì tính nhất quán trong vận hành và giảm thiểu nguy cơ gián đoạn workload do mất kết nối mạng với AWS Region. Giải pháp này mang lại một cách tiếp cận tinh gọn cho việc triển khai MES, đồng thời tận dụng các lợi ích từ hạ tầng AWS.

## Kiến trúc tham chiếu

**Single multi-rack Outpost**
<img src="/images/blogs/PAS-X-on-Outposts-Application-Architecture-1x-multi-rack-Outposts-3.png" alt="" width="50%">

> *Hình 1 – PAS-X chạy trên một multi-rack Outpost dưới một ARN duy nhất*

Outposts hỗ trợ việc nhóm nhiều Outpost racks lại với nhau một cách logic dưới một ARN duy nhất để quản lý. Amazon [EKS Local Cluster](https://docs.aws.amazon.com/eks/latest/userguide/eks-outposts.html) và [EC2 Placement groups on AWS Outposts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups-outpost.html) được sử dụng để phân bổ các EKS worker nodes trên nhiều Outpost racks vật lý trong kiến trúc tham chiếu này. [Amazon Relational Database Service (Amazon RDS)](https://aws.amazon.com/rds/) và [read replicas for Amazon RDS on AWS Outposts](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-on-outposts.rr.html) cũng được sử dụng nhằm tăng cường khả năng phục hồi của database. RDS read replica được duy trì thông qua asynchronous replication. Trong trường hợp một rack đơn lẻ gặp sự cố hoặc bị mất kết nối với Region, các worker nodes trên Outpost thứ cấp vẫn tiếp tục hoạt động, và RDS read replica có thể được promote lên thành primary instance để đảm bảo tính liên tục. Kiến trúc tham chiếu này phù hợp với các tổ chức có đủ không gian để triển khai multi-rack Outpost tại một địa điểm duy nhất, và coi trọng việc đặt EKS Kubernetes control plane cục bộ trên Outpost. Việc host Kubernetes control plane tại chỗ giúp giảm thiểu rủi ro downtime của ứng dụng do mất kết nối mạng tạm thời với AWS home Region của Outposts, chẳng hạn như do đứt cáp quang hoặc sự kiện thời tiết. Vì toàn bộ Kubernetes cluster chạy cục bộ trên Outpost, các ứng dụng vẫn duy trì khả năng truy cập. Bạn cũng có thể thực hiện các thao tác quản lý cluster trong thời gian mất kết nối với cloud. Để biết thêm chi tiết, tham khảo [Prepare local Amazon EKS clusters on AWS Outposts for network disconnects](https://docs.aws.amazon.com/eks/latest/userguide/eks-outposts-network-disconnects.html).

**Two Outpost racks**
<img src="/images/blogs/PAS-X-on-Outposts-Application-Architecture-2-logical-Outposts-4.png" alt="" width="50%">

> *Hình 2 – PAS-X chạy trên hai Outposts racks*

Kiến trúc tham chiếu này sử dụng hai Outpost racks logic, mỗi rack có một Outpost ARN riêng. Trong trường hợp này, chúng ta có thể sử dụng [Amazon EKS Extended Cluster](https://docs.aws.amazon.com/eks/latest/userguide/eks-outposts.html) cùng với [Intra-VPC Communication Across Multiple Outposts with Direct VPC Routing](https://aws.amazon.com/blogs/compute/introducing-intra-vpc-communication-across-multiple-outposts-with-direct-vpc-routing/) để phân bổ worker nodes giữa hai Outposts; tuy nhiên Kubernetes control plane sẽ được host trong AWS home Region. Tham khảo [Deploy an Amazon EKS cluster across AWS Outposts with Intra-VPC communication](https://aws.amazon.com/blogs/containers/deploy-an-amazon-eks-cluster-across-aws-outposts-with-intra-vpc-communication/) để biết thêm thông tin và hướng dẫn triển khai. Thiết kế này cũng sử dụng [Multi-AZ Amazon RDS on Outposts](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-on-outposts.maz.html), trong đó synchronous replication được áp dụng giữa primary và standby RDS instances được host trên các Outpost racks riêng biệt nhằm đảm bảo database high availability. Để biết thêm chi tiết, xem [Deploy Amazon RDS on AWS Outposts with Multi-AZ high availability](https://aws.amazon.com/blogs/database/deploy-amazon-rds-on-aws-outposts-with-multi-az-high-availability/). Trong trường hợp một rack đơn lẻ gặp sự cố hoặc bị mất kết nối với Region, các EKS worker nodes trên Outpost thứ cấp vẫn tiếp tục chạy; tuy nhiên các thao tác quản lý và orchestration cục bộ sẽ không khả dụng cho đến khi kết nối service link được khôi phục. Trong thời gian gián đoạn mạng, Amazon RDS vẫn tiếp tục phục vụ database traffic, và khi service link được tái thiết lập, hệ thống sẽ tự động promote standby database instance lên vai trò primary. Các DNS records sẽ được cập nhật tự động để trỏ đến primary mới. Kiến trúc tham chiếu này phù hợp với các tổ chức coi trọng synchronous replication để duy trì standby database instance, đồng thời mong muốn cơ chế automated failover khi primary instance không khả dụng. Thiết kế này cũng cung cấp khả năng phục hồi multi-AZ, vì mỗi Outpost được gắn với một AZ riêng biệt trong Region đã chọn.

Trong thời gian xảy ra Outposts service link disconnects, các thao tác quản lý dịch vụ và orchestration đối với các tài nguyên được host trên Outpost sẽ không khả dụng, do control plane của các dịch vụ này nằm trong Region. Các tài nguyên đã được triển khai vẫn sẽ tiếp tục chạy miễn là chúng không phụ thuộc vào một dịch vụ AWS cấp Region để hoàn tất workflow. Liên hệ với AWS account team của bạn để biết thêm thông tin về cách các dịch vụ cụ thể trên Outposts xử lý tình huống service link disconnects.

## Các yếu tố cần cân nhắc

Nếu workload PAS-X MES của bạn yêu cầu high availability, hãy xem thêm tài liệu [AWS Outposts High Availability Design and Architecture Considerations](https://docs.aws.amazon.com/whitepapers/latest/aws-outposts-high-availability-design/aws-outposts-high-availability-design.html) bên cạnh các điểm sau:

* NFS File Share cho PAS-X MES có thể được host trên một EC2 instance chạy trên Outposts rack của bạn, hoặc trên một server bên ngoài được kết nối với Outpost và chạy trong datacenter của bạn. Nhiều giải pháp NFS của bên thứ ba cũng được hỗ trợ cho Outposts.
* Bạn có thể host PAS-X MES container images bằng cách sử dụng [Amazon Elastic Container Registry (ECR)](https://aws.amazon.com/ecr/) trong home Region, cục bộ trên một EC2 instance trên Outposts, hoặc trên một server bên ngoài trong datacenter của bạn.
* Outposts sử dụng [AWS Nitro System](https://aws.amazon.com/ec2/nitro/), bao gồm Nitro Hypervisor nhẹ — đây cũng là hypervisor được sử dụng trong các AWS Regions. Nitro System cung cấp khả năng bảo mật nâng cao bằng cách liên tục giám sát, bảo vệ và xác thực phần cứng cũng như firmware của instance. Các tài nguyên ảo hóa được offload sang phần cứng và phần mềm chuyên dụng, giúp giảm thiểu attack surface. Mô hình bảo mật của Nitro System cũng mặc định không cho phép administrative access, loại bỏ nguy cơ sai sót con người và hành vi can thiệp trái phép. Outposts (và Nitro Hypervisor) không cho phép over-provisioning capacity. Các tùy chọn instance size sẽ khả dụng theo đúng các instance sizes được hỗ trợ bởi instance family tương ứng trên Outposts.
* Compute và storage capacity trên Outpost của bạn là hữu hạn; do đó cần cân nhắc kỹ lưỡng về mức độ host resiliency mà bạn mong muốn hỗ trợ khi đặt hàng Outpost. Nếu bạn muốn thiết kế failover giữa hai Outposts, mỗi Outpost cần được cấu hình đủ compute và storage capacity để đáp ứng yêu cầu này. AWS account team của bạn có thể hỗ trợ lập kế hoạch capacity và high availability.

## Giám sát workload trên Outposts

AWS Outposts tích hợp với [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) và [AWS CloudTrail](https://aws.amazon.com/cloudtrail/) để giám sát và quản trị môi trường Outposts thông qua AWS console và APIs.

Amazon EKS hỗ trợ [Amazon CloudWatch Container Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html) nhằm tăng cường khả năng quan sát các containerized workloads, cũng như các công cụ mã nguồn mở như [Prometheus monitoring on Amazon EKS](https://docs.aws.amazon.com/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/prometheus-monitoring-eks.html) và [Grafana in Amazon EKS](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-onboard-query-grafana-7.3.html). Để biết thêm thông tin, hãy xem [Monitoring best practices for AWS Outposts](https://aws.amazon.com/blogs/mt/monitoring-best-practices-for-aws-outposts/). Đối với cả hai kiến trúc, chúng tôi sử dụng CloudWatch Container Insights làm giải pháp giám sát chính, nhờ khả năng tích hợp liền mạch với Amazon EKS và đặc tính managed service, giúp giảm đáng kể chi phí vận hành.

CloudWatch Container Insights cung cấp năng lực giám sát toàn diện trên nhiều lớp của hạ tầng Kubernetes, bao gồm các metrics chi tiết ở mức cluster, node, namespace và workload. Khả năng hiển thị đa chiều này cho phép vừa chủ động cảnh báo, vừa hỗ trợ troubleshooting chuyên sâu khi xảy ra sự cố. Việc tích hợp chặt chẽ với Amazon EKS cho phép triển khai dưới dạng managed add-on với cấu hình tối thiểu, khiến đây trở thành lựa chọn hiệu quả để duy trì observability mạnh mẽ trong các môi trường production.

<img src="/images/blogs/Figure3-5.png" alt="" width="50%">

> *Hình 3 – CloudWatch Container Insights: metrics ở mức cluster*

<img src="/images/blogs/Figure4-4.png" alt="" width="50%">

> *Hình 4 – CloudWatch Container Insights: metrics ở mức workload*

## Kết luận

Bài viết này cung cấp tổng quan về các kiến trúc tham chiếu để chạy Werum PAS-X MES của Körber trên AWS Outposts, hướng dẫn giám sát Amazon EKS workload trên Outposts, cũng như các yếu tố bổ sung cần cân nhắc để tùy chỉnh triển khai theo môi trường và yêu cầu của bạn. Outposts cho phép bạn chạy workload cục bộ trong khi vẫn sử dụng cùng AWS APIs, dịch vụ và tooling quen thuộc, qua đó giảm thiểu nguy cơ gián đoạn MES do mất kết nối mạng với AWS Region. Hãy liên hệ với AWS account team của bạn để [kết nối với một Outposts specialist](https://pages.awscloud.com/GLOBAL_PM_LN_outposts-features_2020084_7010z000001Lpcl_01.LandingPage.html) và tìm hiểu thêm.

---
## Körber – AWS Partner Spotlight

**[Körber Pharma Software GmbH](https://partners.amazonaws.com/partners/0018a00001hHE6iAAG/K%C3%B6rber%20Pharma%20Software%20GmbH) là một AWS Technology partner**, với các giải pháp phần mềm Werum PAS-X giúp các nhà sản xuất dược phẩm số hóa nhà máy pharma, biotech và cell & gene. Thông qua việc sử dụng các dịch vụ AWS, chúng tôi có thể hỗ trợ khách hàng hiện đại hóa MES của họ thông qua containerization.

[Liên hệ Körber](https://partners.amazonaws.com/contactpartner?partnerId=0018a00001hHE6iAAG&partnerName=K%C3%B6rber%20Pharma%20Software%20GmbH) | [Tổng quan Partner](https://partners.amazonaws.com/partners/0018a00001hHE6iAAG/K%C3%B6rber%20Pharma%20Software%20GmbH)

TAGS: [Amazon EKS](https://aws.amazon.com/blogs/apn/tag/amazon-eks/), [Amazon RDS](https://aws.amazon.com/blogs/apn/tag/amazon-rds/), [AWS Outposts](https://aws.amazon.com/blogs/apn/tag/aws-outposts/), [AWS Partner Solution](https://aws.amazon.com/blogs/apn/tag/aws-partner-solution/), [Körber Pharma Software](https://aws.amazon.com/blogs/apn/tag/korber-pharma-software/)