---
title: "Modernize your MES Workloads using Containers: Körber’s Werum PAS-X MES Suite on AWS Outposts"
date: "2025-08-14T10:00:00+07:00"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

> by Brianna Rosentrater, Doruk Ozturk, Krishna Rajeesh, and Rohit Jagetia on 16 APR 2025 in [Amazon Elastic Kubernetes Service](https://aws.amazon.com/blogs/apn/category/compute/amazon-kubernetes-service/), [Amazon RDS](https://aws.amazon.com/blogs/apn/category/database/amazon-rds/), [AWS Outposts](https://aws.amazon.com/blogs/apn/category/compute/aws-outposts/), [AWS Outposts rack](https://aws.amazon.com/blogs/apn/category/compute/aws-outposts/aws-outposts-rack/), [Best Practices](https://aws.amazon.com/blogs/apn/category/post-types/best-practices/), [Partner solutions](https://aws.amazon.com/blogs/apn/category/post-types/partner-solutions/), [Technical How-to](https://aws.amazon.com/blogs/apn/category/post-types/technical-how-to/) | [Permalink](https://aws.amazon.com/blogs/apn/modernize-your-mes-workloads-using-containers-korbers-werum-pas-x-mes-suite-on-aws-outposts/) | [Comments](https://aws.amazon.com/blogs/apn/modernize-your-mes-workloads-using-containers-korbers-werum-pas-x-mes-suite-on-aws-outposts/#Comments) | [Share](https://aws.amazon.com/blogs/apn/modernize-your-mes-workloads-using-containers-korbers-werum-pas-x-mes-suite-on-aws-outposts/#)
>
> By Brianna Rosentrater, Hybrid Edge Specialist SA – AWS
> By Doruk Ozturk, Sr. Containers Specialist SA – AWS
> By Rohit Jagetia, Sr. GLS Solutions Architect – AWS
> By Krishna Rajeesh, Principal Cloud Consultant – Körber Pharma Software GmbH

Life sciences manufacturers face challenges in maintaining consistent production operations while meeting strict regulatory requirements. Many need to run their Manufacturing Execution Systems (MES) on-premises, but still want to benefit operationally from using managed cloud services and maintaining a consistent software stack across their on-premise and cloud environments. This post will focus on running Werum PAS-X MES from Körber on [AWS Outposts racks](https://aws.amazon.com/outposts/rack/?nc=sn&loc=2). You can run the MES on a single logical Outpost, using multiple Outposts (multiple Outposts [Amazon Resource Names (ARN)](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference-arns.html)), or a multi-rack Outpost (multiple racks under a single Outposts ARN) for high availability. Outposts extends one or more Amazon VPCs into an on-premises network with the ability to connect to a broader range of AWS services in the connected [AWS home Region](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/). Customers can use AWS APIs, tools, and security controls to run, manage, and secure applications. AWS maintains the Outposts infrastructure, AWS services, and software patching as in regional operations. Outposts is designed to be a connected solution, but by using multiple Outposts with redundant [service link](https://docs.aws.amazon.com/outposts/latest/userguide/region-connectivity.html) connections you can plan for network or service failures to avoid downtime.

---

## Overview

[Körber’s Werum PAS-X MES Suite](https://www.koerber-pharma.com/en/solutions/software/werum-pas-x-mes-suite#:~:text=PAS%2DX%20MES%20covers%20the,and%20commercial%20production%20to%20packaging.) is a production management system that allows life science customers to reduce their error rate and manufacturing costs. PAS-X MES covers the entire production cycle of the pharmaceutical, biotech, and cell & gene therapy industries — from process development and commercial production to packaging. The MES Suite includes all important functions for manufacturing in a flexible way. By hosting PAS-X MES on Outposts, customers can reduce time-to-market, enhance efficiency, maintain operational consistency, and mitigate potential workload disruption caused by network disconnects from the AWS Region. This solution provides a streamlined approach to MES deployment while leveraging the benefits of AWS infrastructure.

## Reference Architectures

**Single multi-rack Outpost**
<img src="/images/blogs/PAS-X-on-Outposts-Application-Architecture-1x-multi-rack-Outposts-3.png" alt="" width="50%">

> *Figure 1 – PAS-X running on a multi-rack Outpost under one ARN*

Outposts supports having multiple Outpost racks logically grouped together under a single ARN for management. Amazon [EKS Local Cluster](https://docs.aws.amazon.com/eks/latest/userguide/eks-outposts.html) and [EC2 Placement groups on AWS Outposts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups-outpost.html) are used to spread the EKS worker nodes between multiple physical Outpost racks in this reference architecture. [Amazon Relational Database Service (Amazon RDS)](https://aws.amazon.com/rds/) and [read replicas for Amazon RDS on AWS Outposts](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-on-outposts.rr.html) are also being used here for increased database resiliency. The RDS read replica is maintained using asynchronous replication. In the event of a single rack failure or disconnection from the region, the worker nodes on the secondary Outpost will continue to run, and the RDS read replica can be promoted to the primary instance for continuity. This reference architecture should be used by those who have the space to host a multi-rack Outpost at a single site, and value having the EKS Kubernetes control plane locally on their Outpost. Having the Kubernetes control plane locally hosted helps mitigate the risk of application downtime that might result from temporary network disconnects to the Outposts home AWS Region. These disconnects can be caused by fiber cuts or weather events. Because the entire Kubernetes cluster runs locally on your Outpost, applications remain available. You can perform cluster operations during network disconnects to the cloud. For more information, see [Prepare local Amazon EKS clusters on AWS Outposts for network disconnects](https://docs.aws.amazon.com/eks/latest/userguide/eks-outposts-network-disconnects.html).

**Two Outpost racks**
<img src="/images/blogs/PAS-X-on-Outposts-Application-Architecture-2-logical-Outposts-4.png" alt="" width="50%">

> *Figure 2 – PAS-X running on two Outposts racks*

This reference architecture uses two logical Outpost racks that each have their own Outpost ARN. Here we can use [Amazon EKS Extended Cluster](https://docs.aws.amazon.com/eks/latest/userguide/eks-outposts.html) with [Intra-VPC Communication Across Multiple Outposts with Direct VPC Routing](https://aws.amazon.com/blogs/compute/introducing-intra-vpc-communication-across-multiple-outposts-with-direct-vpc-routing/) to spread the worker nodes between the two Outposts, however in this architecture the Kubernetes control plane will be hosted in the home AWS Region. See [Deploy an Amazon EKS cluster across AWS Outposts with Intra-VPC communication](https://aws.amazon.com/blogs/containers/deploy-an-amazon-eks-cluster-across-aws-outposts-with-intra-vpc-communication/) for more information and a deployment guide. This design also uses [Multi-AZ Amazon RDS on Outposts](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-on-outposts.maz.html) which uses synchronous replication between the primary and standby RDS instances hosted on separate Outpost racks for database high availability. For more information see [Deploy Amazon RDS on AWS Outposts with Multi-AZ high availability](https://aws.amazon.com/blogs/database/deploy-amazon-rds-on-aws-outposts-with-multi-az-high-availability/). In the event of a single rack failure or disconnection from the region, the EKS worker nodes on the secondary Outpost will continue to run, however local management and orchestration actions will be unavailable until the service link connection is reestablished. During a network outage Amazon RDS will continue serving database traffic, and when the service link connection is re-established will automatically promote the standby database instance to the primary role. The DNS records are updated automatically to point to the new primary. This reference architecture should be used by those who value using synchronous replication to maintain their standby database instance, and who want automated failover to the standby instance if the primary instance becomes unavailable. This design also offers multi-AZ resiliency, as each Outpost will be homed to a unique AZ in the chosen region.

During Outposts service link disconnects, service management and orchestration actions for resources hosted on the Outpost will be unavailable as the control plane for these services is in region. Deployed resources will continue to run as long as they aren’t dependent on an AWS regional service to complete a workflow. Contact your AWS account team for more information on how specific services on Outposts handle service link disconnects.

## Considerations

If your PAS-X MES workload requires high availability, review [AWS Outposts High Availability Design and Architecture Considerations](https://docs.aws.amazon.com/whitepapers/latest/aws-outposts-high-availability-design/aws-outposts-high-availability-design.html) in addition to the following:

* The NFS File Share for PAS-X MES can be hosted on an EC2 instance running on your Outposts rack, or it can be an external server connected to your Outpost running in your datacenter. A variety of third party NFS solutions are also supported for Outposts.
* You can host PAS-X MES container images using [Amazon Elastic Container Registry (ECR)](https://aws.amazon.com/ecr/) in the home Region, locally in an EC2 instance on Outposts, or on an external server in your datacenter.
* Outposts uses the [AWS Nitro System](https://aws.amazon.com/ec2/nitro/) including the lightweight Nitro Hypervisor – this is the same hypervisor used in AWS Regions. The Nitro System provides enhanced security that continuously monitors, protects, and verifies the instance hardware and firmware. Virtualization resources are offloaded to dedicated hardware and software minimizing the attack surface. The Nitro System’s security model also prohibits administrative access by default, eliminating the possibility of human error and tampering. Outposts (and the Nitro Hypervisor) does not allow for over-provisioning of capacity. Instance size options will be available according to the instance sizes supported by that instance family on Outposts.
* Compute and storage capacity on your Outpost is finite, and thought should be given as to what kind of host resiliency you’d like your Outpost to support when ordering. If you’d like to architect for failover between two Outposts, each Outpost should be configured to have enough compute and storage capacity to support this. Your AWS account team can help you with capacity and high availability planning.

## Monitoring your Workload on Outposts
AWS Outposts integrates with [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/) and [AWS CloudTrail](https://aws.amazon.com/cloudtrail/) for monitoring and governance of your Outposts environment through the AWS console and APIs.

Amazon EKS supports [Amazon CloudWatch Container Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html) for added visibility of your containerized workloads, as well as open source tooling such as [Prometheus monitoring on Amazon EKS](https://docs.aws.amazon.com/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/prometheus-monitoring-eks.html) and [Grafana in Amazon EKS](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-onboard-query-grafana-7.3.html). For more information, review [Monitoring best practices for AWS Outposts](https://aws.amazon.com/blogs/mt/monitoring-best-practices-for-aws-outposts/). For both architectures, we leveraged CloudWatch Container Insights as our primary monitoring solution, for its seamless integration with Amazon EKS and its managed service nature, which significantly reduces operational overhead.

CloudWatch Container Insights offers comprehensive monitoring capabilities across multiple layers of the Kubernetes infrastructure, providing detailed metrics at the cluster, node, namespace, and workload levels. This multi-dimensional visibility enables both proactive alerting and detailed troubleshooting when issues arise. The solution’s integration with Amazon EKS means that it can be deployed as a managed add-on with minimal configuration, making it an efficient choice for maintaining robust observability in production environments.

<img src="/images/blogs/Figure3-5.png" alt="" width="50%">

> *Figure 3 – CloudWatch Container Insights: cluster level metrics*

<img src="/images/blogs/Figure4-4.png" alt="" width="50%">

> *Figure 4 – CloudWatch Container Insights: workload level metrics*

## Conclusion
This post provides an overview of the reference architectures for running Werum PAS-X MES from Körber on AWS Outposts, guidance on monitoring your Amazon EKS workload on Outposts, and additional considerations for customizing the implementation to your environment and preferences. Outposts allows you to run workloads locally while still using the same AWS APIs, services, and tooling you’re used to, mitigating potential MES disruption caused by network disconnects from the AWS Region. Reach out to your AWS account team to [connect with an Outposts specialist](https://pages.awscloud.com/GLOBAL_PM_LN_outposts-features_2020084_7010z000001Lpcl_01.LandingPage.html) to learn more.

---
## Körber – AWS Partner Spotlight
**[Körber Pharma Software GmbH](https://partners.amazonaws.com/partners/0018a00001hHE6iAAG/K%C3%B6rber%20Pharma%20Software%20GmbH) is an AWS Technology partner** whose Werum PAS-X software solutions help drug manufacturers to digitize their pharma, biotech and cell & gene factories. Using AWS services, we’re able to help our customers modernize their MES through containerization.

[Contact Körber](https://partners.amazonaws.com/contactpartner?partnerId=0018a00001hHE6iAAG&partnerName=K%C3%B6rber%20Pharma%20Software%20GmbH) | [Partner Overview](https://partners.amazonaws.com/partners/0018a00001hHE6iAAG/K%C3%B6rber%20Pharma%20Software%20GmbH)

TAGS: [Amazon EKS](https://aws.amazon.com/blogs/apn/tag/amazon-eks/), [Amazon RDS](https://aws.amazon.com/blogs/apn/tag/amazon-rds/), [AWS Outposts](https://aws.amazon.com/blogs/apn/tag/aws-outposts/), [AWS Partner Solution](https://aws.amazon.com/blogs/apn/tag/aws-partner-solution/), [Körber Pharma Software](https://aws.amazon.com/blogs/apn/tag/korber-pharma-software/)
