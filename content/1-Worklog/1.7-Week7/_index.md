---
title: "Week 7 Worklog"
date: "2025-10-20T09:00:00+07:00"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives

- Secure outbound network access for private resources.
- Learn how to manage application secrets securely.
- Understand basic application-layer protection using AWS WAF.
- Apply security best practices to cloud architectures.

---

### Tasks Carried Out This Week

| Day | Task | Start Date | Completion Date | Reference Material |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **NAT Gateway Concepts:**<br>- Understand why private subnets need outbound internet access.<br>- Learn NAT Gateway architecture and routing. | 20/10/2025 | 20/10/2025 | |
| 2 | **AWS Secrets Manager:**<br>- Store database credentials securely.<br>- Learn secret rotation concepts and access control. | 21/10/2025 | 21/10/2025 | |
| 3 | **AWS WAF Basics:**<br>- Understand Web Application Firewall use cases.<br>- Learn about common protections such as SQL injection filtering. | 22/10/2025 | 22/10/2025 | |
| 4 | **Practice Lab:**<br>- Create and configure a NAT Gateway.<br>- Update Route Tables to enable private subnet internet access.<br>- Verify outbound connectivity from private EC2 instances. | 23/10/2025 | 23/10/2025 | |
| 5 | **Review & Cost Awareness:**<br>- Review NAT Gateway pricing model.<br>- Analyze cost implications for long-running environments. | 24/10/2025 | 24/10/2025 | |

---

### Extra Knowledge: Securing Private Subnet Traffic

This week highlighted the importance of **controlled outbound access**:

- Private instances should not be directly reachable from the Internet.
- NAT Gateway allows outbound connectivity while maintaining inbound isolation.
- Security services like Secrets Manager and WAF reduce risks related to credential leakage and web-based attacks.

These patterns are critical for building secure production-grade systems.

---

### Week 7 Achievements

- Successfully enabled outbound internet access for private subnet resources using NAT Gateway.
- Verified that private EC2 instances could access external resources without being publicly exposed.
- Stored sensitive credentials securely using AWS Secrets Manager instead of hardcoding them.
- Applied basic Web Application Firewall rules to protect web-facing components.
- Improved overall understanding of security and cost trade-offs in AWS architectures.

---

