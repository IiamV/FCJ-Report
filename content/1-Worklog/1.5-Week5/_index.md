---
title: "Week 5 Worklog"
date: "2025-10-06T09:00:00+07:00"
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives

- Understand application scalability and high availability on AWS.
- Learn how Load Balancers distribute traffic.
- Learn how Auto Scaling Groups enable self-healing systems.
- Apply stateless application concepts in cloud architectures.

---

### Tasks Carried Out This Week

| Day | Task | Start Date | Completion Date | Reference Material |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Elastic Load Balancing:**<br>- Learn Application Load Balancer (ALB) concepts.<br>- Understand Target Groups and Health Checks. | 06/10/2025 | 06/10/2025 | |
| 2 | **Auto Scaling Groups (ASG):**<br>- Learn Launch Templates.<br>- Understand scaling policies (CPU-based, scheduled scaling). | 07/10/2025 | 07/10/2025 | |
| 3 | **Practice Lab:**<br>- Deploy multiple EC2 instances behind an ALB.<br>- Verify traffic distribution across instances. | 08/10/2025 | 08/10/2025 | |
| 4 | **Practice Lab:**<br>- Create an Auto Scaling Group.<br>- Simulate load to trigger scale-out and scale-in events. | 09/10/2025 | 09/10/2025 | |
| 5 | **Review & Concepts:**<br>- Review stateless application design.<br>- Understand why statelessness is required for horizontal scaling. | 10/10/2025 | 10/10/2025 | |

---

### Extra Knowledge: Health Checks and Self-Healing

This week introduced the concept of **self-healing systems** on AWS:

- Load Balancers continuously perform health checks on registered targets.
- When an EC2 instance becomes unhealthy, traffic is automatically routed away.
- Auto Scaling Groups can terminate unhealthy instances and replace them with new ones.

This mechanism ensures higher availability without manual intervention and is a core principle of modern cloud-native architectures.

---

### Week 5 Achievements

- Successfully configured an Application Load Balancer to distribute traffic across multiple EC2 instances.
- Created and managed Target Groups with proper health check configurations.
- Built an Auto Scaling Group capable of automatically adjusting capacity based on system load.
- Observed automatic instance replacement during simulated failure scenarios.
- Understood the importance of stateless application design for scalable frontend and backend systems.

---

