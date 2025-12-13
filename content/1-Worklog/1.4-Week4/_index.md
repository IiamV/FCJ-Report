---
title: "Week 4 Worklog"
date: "2025-09-29T09:00:00+07:00"
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives

- Consolidate knowledge gained during the first month of AWS learning.
- Apply AWS fundamentals to build a small environment from scratch.
- Understand how networking and compute components work together.
- Practice accessing and managing resources in different network layers.

---

### Tasks Carried Out This Week

| Day | Task | Start Date | Completion Date | Reference Material |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Planning:**<br>- Review concepts learned in Weeks 1–3.<br>- Plan a simple architecture with public and private components. | 29/09/2025 | 29/09/2025 | |
| 2 | **Network Setup:**<br>- Create a custom VPC.<br>- Configure Public and Private Subnets.<br>- Set up Route Tables and Internet Gateway. | 30/09/2025 | 30/09/2025 | |
| 3 | **Compute Setup:**<br>- Launch EC2 instance in Public Subnet (Web / Access Point).<br>- Launch EC2 instance in Private Subnet (Backend simulation). | 01/10/2025 | 01/10/2025 | |
| 4 | **Connectivity & Access Testing:**<br>- SSH into Public EC2 instance.<br>- Test connectivity from Public EC2 to Private EC2.<br>- Validate Security Group rules. | 02/10/2025 | 02/10/2025 | |
| 5 | **Review & Cleanup:**<br>- Review architecture behavior and security boundaries.<br>- Terminate EC2 instances and clean up network resources. | 03/10/2025 | 03/10/2025 | |

---

### Extra Knowledge: Public vs Private Access Design

Through hands-on practice, I gained a clearer understanding of how **public and private subnets** are used in AWS:

- Resources in a **Public Subnet** can communicate with the Internet through an Internet Gateway.
- Resources in a **Private Subnet** do not have direct Internet access and must be accessed indirectly.
- This separation is critical for security and is commonly used in real-world architectures where backend services should not be exposed publicly.

This concept later became the foundation for understanding multi-tier architectures and secure backend deployments.

---

### Week 4 Achievements

- Successfully built a custom AWS environment without using default configurations.
- Created and configured a VPC with both public and private subnets.
- Launched EC2 instances in different network tiers and validated access rules.
- Demonstrated controlled access to private resources through a public entry point.
- Strengthened overall understanding of AWS networking, security boundaries, and architecture design.

---

