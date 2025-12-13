---
title: "Week 8 Worklog"
date: "2025-10-27T09:00:00+07:00"
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives

- Finalize the overall architecture for the final project.
- Document network design, IP ranges, and security rules.
- Prepare a clear deployment plan before implementation.

---

### Tasks Carried Out This Week

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 1 | **Architecture Diagramming:**<br>- Design full High Availability (HA) architecture.<br>- Ensure Multi-AZ deployment for critical components. | 27/10/2025 | 27/10/2025 | |
| 2 | **IP Address Planning:**<br>- Define VPC CIDR range (e.g., 10.0.0.0/16).<br>- Allocate subnets for Public and Private tiers. | 28/10/2025 | 28/10/2025 | |
| 3 | **Security Design:**<br>- Document Security Group rules.<br>- Apply Principle of Least Privilege. | 29/10/2025 | 29/10/2025 | |
| 4 | **Design Review:**<br>- Review architecture for bottlenecks and risks.<br>- Validate scalability and fault tolerance. | 30/10/2025 | 30/10/2025 | |
| 5 | **Final Preparation:**<br>- Create deployment checklist.<br>- Confirm readiness for implementation phase. | 31/10/2025 | 31/10/2025 | |

---

### Extra Knowledge: Reducing Blast Radius

A key design principle applied this week was **Blast Radius Reduction**. By distributing application components across multiple Availability Zones:

- A failure in one AZ does not bring down the entire system.
- Load Balancers can route traffic to healthy instances automatically.
- The system aligns with AWS Well-Architected Framework reliability best practices.

This approach significantly increases resilience for production workloads.

---

### Week 8 Achievements

- Completed a detailed High Availability architecture diagram for the final project.
- Clearly defined IP address ranges and subnet boundaries.
- Documented strict and minimal Security Group rules to improve security posture.
- Prepared a structured deployment checklist to ensure smooth implementation in upcoming weeks.

---
