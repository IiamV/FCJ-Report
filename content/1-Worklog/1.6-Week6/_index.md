---
title: "Week 6 Worklog"
date: "2025-10-13T09:00:00+07:00"
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives

- Learn how managed database services work on AWS.
- Understand high availability and performance concepts for databases.
- Gain hands-on experience with Amazon RDS.
- Learn basic caching concepts using Amazon ElastiCache.

---

### Tasks Carried Out This Week

| Day | Task | Start Date | Completion Date | Reference Material |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **RDS Fundamentals:**<br>- Learn supported database engines.<br>- Understand Multi-AZ deployments and failover behavior. | 13/10/2025 | 13/10/2025 | |
| 2 | **RDS Security & Performance:**<br>- Learn about Security Group configuration for RDS.<br>- Understand Read Replicas and their use cases. | 14/10/2025 | 14/10/2025 | |
| 3 | **ElastiCache Basics:**<br>- Learn in-memory caching concepts.<br>- Understand Redis use cases and caching strategies. | 15/10/2025 | 15/10/2025 | |
| 4 | **Practice Lab:**<br>- Launch an Amazon RDS database instance.<br>- Configure connectivity from EC2 to RDS.<br>- Validate network and security configurations. | 16/10/2025 | 16/10/2025 | |
| 5 | **Review & Backup Concepts:**<br>- Understand automated backups and snapshots.<br>- Review disaster recovery basics for databases. | 17/10/2025 | 17/10/2025 | |

---

### Extra Knowledge: Availability vs Performance in Databases

This week clarified an important distinction in database design:

- **Multi-AZ deployments** focus on high availability by synchronously replicating data to a standby instance.
- **Read Replicas** focus on improving read performance by asynchronously replicating data to additional instances.

Understanding this difference helped reinforce how databases are designed to meet different application requirements in production environments.

---

### Week 6 Achievements

- Successfully deployed a managed relational database using Amazon RDS.
- Gained experience connecting EC2 instances to RDS securely using Security Groups.
- Understood how AWS manages backups, patching, and failover for databases.
- Learned how caching with ElastiCache can improve application performance.
- Built a stronger foundation for designing reliable and scalable data layers in AWS architectures.

---

