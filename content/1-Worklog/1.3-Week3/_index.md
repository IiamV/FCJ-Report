---
title: "Week 3 Worklog"
date: "2025-09-22T09:00:00+07:00"
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives

- Strengthen understanding of AWS Compute and Storage core services.
- Learn how data persistence works on AWS.
- Practice deploying simple workloads using EC2 and S3.
- Understand storage choices for different application use cases.

---

### Tasks Carried Out This Week

| Day | Task | Start Date | Completion Date | Reference Material |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **EC2 Fundamentals (Deeper Study):**<br>- Review EC2 instance families and use cases.<br>- Understand AMI selection.<br>- Learn about key pairs and basic User Data concepts. | 22/09/2025 | 22/09/2025 | https://docs.aws.amazon.com/ec2/ |
| 2 | **EBS – Block Storage:**<br>- Learn EBS volume types and performance characteristics.<br>- Practice attaching and detaching EBS volumes.<br>- Understand snapshots and basic backup concepts. | 23/09/2025 | 23/09/2025 | |
| 3 | **S3 – Object Storage:**<br>- Learn S3 buckets and objects.<br>- Understand storage classes and lifecycle policies.<br>- Learn about bucket policies and basic permissions. | 24/09/2025 | 24/09/2025 | |
| 4 | **Practice Lab:**<br>- Launch an EC2 instance and deploy a simple web service.<br>- Create an S3 bucket and upload static files.<br>- Test access to S3-hosted static content. | 25/09/2025 | 25/09/2025 | https://cloudjourney.awsstudygroup.com/ |
| 5 | **Review & Comparison:**<br>- Compare EC2 + EBS vs S3 use cases.<br>- Identify suitable storage choices for frontend assets and backend data. | 26/09/2025 | 26/09/2025 | |

---

### Extra Knowledge: Choosing the Right Storage Type

This week helped me clearly distinguish between **block storage and object storage**:

- **EBS** is designed for workloads that require persistent disks attached to EC2 instances, such as operating systems and databases.
- **S3** is designed for scalable, durable object storage, making it ideal for static assets such as frontend files, images, and backups.

This distinction later became important when planning how frontend applications can be hosted and delivered efficiently on AWS.

---

### Week 3 Achievements

- Gained deeper understanding of Amazon EC2, including instance selection and AMI usage.
- Successfully attached and managed EBS volumes, reinforcing the concept of persistent storage.
- Learned how snapshots can be used for backup and recovery purposes.
- Created and managed Amazon S3 buckets and uploaded static content.
- Understood practical differences between **EBS and S3**, especially in the context of hosting frontend assets versus backend application data.
- Built confidence in deploying simple workloads using AWS core compute and storage services.

---

