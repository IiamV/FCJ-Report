---
title: "Optimizing cost savings: The advantage of Amazon Aurora over self-managed open source databases"
date: "2025-08-14T10:00:00+07:00"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

> by Chris Riggin and Cees Molenaar on 31 MAR 2025 in [Advanced (300)](https://aws.amazon.com/blogs/database/category/learning-levels/advanced-300/), [Amazon Aurora](https://aws.amazon.com/blogs/database/category/database/amazon-aurora/), [Best Practices](https://aws.amazon.com/blogs/database/category/post-types/best-practices/), [Permalink](https://aws.amazon.com/blogs/database/optimizing-cost-savings-the-advantage-of-amazon-aurora-over-self-managed-open-source-databases/)

[Amazon Aurora](https://aws.amazon.com/rds/aurora/), a relational database service from [Amazon Web Services (AWS)](https://aws.amazon.com/), provides unparalleled high performance and availability at global scale, making it a preferred choice for organizations seeking a competitive database solution. As a database built for the cloud, Aurora is engineered to utilize cloud infrastructure capabilities, offering enterprise-level resilience, performance, and scalability for two of the most trusted open source database engines: MySQL and PostgreSQL. A key factor in the success of Aurora is its innovative architecture, which features a decoupled storage cluster distributed across multiple [Availability Zones](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/), resulting in a unique and resilient solution while reducing overall costs. While customers often anticipate cost savings when migrating from commercial databases to Aurora, many are pleasantly surprised to discover similar financial advantages when transitioning from self-managed MySQL and PostgreSQL.

In this post, we highlight often overlooked architectural designs and the inherent features of Aurora that optimize costs when deploying an open source database. The following sections examine various use cases, contrasting typical self-managed database configurations and their associated costs with the equivalent solution on Aurora, highlighting potential cost savings and operational efficiencies. We provide detailed cost comparisons based on the specific criteria defined in each use case using on-demand pricing in the US East (Ohio) region, and they are valid as of the date when this post was published. We encourage readers to consult the [AWS pricing calculator](https://calculator.aws/#/) or their account teams for current rates and personalized cost analyses.

---

## Use case: Fundamental resilient database architecture with scaled reads

One of the most common use cases is based on a fundamental resilient architecture with scaled reads. The below self-managed architecture provides for a primary instance serving read/write traffic, a secondary logical replica that serves dual purposes – offering high availability with a low recovery point objective (RPO) while also acting as a replication source for downstream read replicas, and two read-only instances where read traffic can be diverted and scaled horizontally. This topology optimizes read scalability by distributing read workloads across multiple replicas while avoiding replication overhead on the primary, as the secondary replica handles the replication stream to downstream read replicas. The secondary replica can be promoted to primary during failover scenarios, maintaining business continuity, while the read replicas continue to serve read traffic, now replicating from the newly promoted primary. This architecture specifically uses the secondary instance as the failover target, as industry practices typically avoid using read replicas for failover in MySQL and PostgreSQL due to potential replication lag and data loss during the promotion process. This limitation necessitates the additional secondary instance, effectively increasing the overall cost of the solution.

**Self-managed architecture:**
<img src="/images/blogs/DBBLOG-2824_UC1-SM.png" alt="" width="55%">

The following [Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_AuroraOverview.html) architecture demonstrates how the same workload can be maintained with similar or improved performance while simplifying architecture and reducing resources, ultimately lowering costs. Aurora’s architecture incorporates specific features designed to enhance availability and facilitate disaster recovery. Unlike traditional self-managed open source databases, where achieving zero RPO typically increases both costs and performance overhead, Aurora inherently provides zero RPO failover capabilities. This is achieved through its innovative design, which allows [read replicas](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Replication.html#Aurora.Replication.Replicas) to serve as [high availability failover targets](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraHighAvailability.html#Concepts.AuroraHighAvailability.Instances) without additional configuration or performance impact.


**Aurora architecture:**
<img src="/images/blogs/DBBLOG-2824_UC1-AA.png" alt="" width="55%">
In this use case, this architectural advantage eliminates the need for a dedicated secondary instance solely for failover replication purposes. As a result, by migrating from the self-managed architecture to Aurora, the cluster footprint is reduced from four nodes to three. While this reduction in compute resources does offer some cost savings, it’s not the primary source of economic benefit in this scenario. The majority of cost efficiencies come from Aurora’s [revolutionary storage architecture](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html).

In contrast to traditional database setups where each node typically has its own independent storage containing a separate copy of the data, replicated from the source instance, Aurora employs a fundamentally different approach. While the self-managed architecture not only decreases performance capacity due to replication overhead, it also quadruples the storage costs in this use case. In contrast, all Aurora instances—both primary and read replicas—connect to the same shared storage volume. This [Aurora storage cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html) inherently provides resilience by distributing six copies of data across three Availability Zones. Customers are then charged for only a single copy of the data, effectively reducing the storage cost in this architecture from four to one.

Assuming similar instance types (r6g.xlarge, db.r6g.xlarge) between the self-managed and Aurora architectures, with 2 TB of [io2 provisioned input/output operations per second (IOPS) SSD](https://docs.aws.amazon.com/ebs/latest/userguide/provisioned-iops.html#io2-block-express) storage at 64,000 IOPS in the self-managed architecture and [Aurora I/O-Optimized](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html#aurora-storage-type) (a storage option designed for improved price performance and predictability for I/O-intensive applications) for the Aurora architecture, the cost differences become apparent. Let’s compare the monthly costs of the self-managed architecture versus the Aurora solution:
| Architecture    | # DB Nodes (w/ writer) | Instance Type | Storage Size per Volume | # Storage Volumes | Total Cost |
|-----------------|------------------------|---------------|--------------------------|-------------------|------------|
| Self-Managed    | 4                      | r6g.xlarge   | 2 TB                     | 4                 | $15,756.67 |
| Amazon Aurora   | 3                      | db.r6g.xlarge| 2 TB                     | 1                 | $1,939.05  |
As we can see, the self-managed architecture, requiring four compute nodes and separate storage for each, totals $15,756.67 per month. In contrast, the Aurora solution with three compute nodes and unified storage comes to $1,939.05 per month, representing an 87.7% reduction in costs. This reduction is driven by Aurora’s efficient storage architecture, which eliminates the need for redundant storage copies while maintaining high availability and performance.

While not the focus of this blog, it’s worth mentioning [Amazon Relational Database](https://aws.amazon.com/rds/) Service (Amazon RDS), a managed database service that simplifies database administration tasks. RDS [Multi-AZ with two readable standbys](https://aws.amazon.com/rds/features/multi-az/) eliminates the need for a dedicated secondary/standby instance. While this deployment option requires independent storage volumes, each containing a separate copy of the data, it provides a robust high-availability solution. Aurora’s unique storage architecture offers additional cost optimization benefits through its shared storage model, which is why we’re focusing on Aurora’s capabilities in this post.

| Architecture    | # DB Nodes (w/ writer) | Instance Type   | Storage Size per Volume | # Storage Volumes | Total Cost   |
|-----------------|------------------------|-----------------|--------------------------|-------------------|--------------|
| Self-Managed    | 4                      | r6g.xlarge      | 2 Tb                     | 4                 | $15,756.67   |
| Amazon Aurora   | 3                      | db.r6g.xlarge   | 2 Tb                     | 1                 | $1,939.05    |

As we can see, the self-managed architecture, requiring four compute nodes and separate storage for each, totals $15,756.67 per month. In contrast, the Aurora solution with three compute nodes and unified storage comes to $1,939.05 per month, representing an 87.7% reduction in costs. This reduction is driven by Aurora’s efficient storage architecture, which eliminates the need for redundant storage copies while maintaining high availability and performance.

While not the focus of this blog, it’s worth mentioning [Amazon Relational Database](https://aws.amazon.com/rds/) Service (Amazon RDS), a managed database service that simplifies database administration tasks. RDS [Multi-AZ with two readable standbys](https://aws.amazon.com/rds/features/multi-az/) eliminates the need for a dedicated secondary/standby instance. While this deployment option requires independent storage volumes, each containing a separate copy of the data, it provides a robust high-availability solution. Aurora’s unique storage architecture offers additional cost optimization benefits through its shared storage model, which is why we’re focusing on Aurora’s capabilities in this post.

## Use case: Variable read loads

Now let’s examine a scenario where applications experience fluctuations in read traffic, with peak loads potentially requiring considerably more capacity than the baseline resilient architecture can efficiently provide. The following self-managed architectural design manages variable read traffic through four read-only instances. This configuration is sized for peak demand periods, a standard practice in traditional architectures. The setup uses pre-provisioned resources that handle periodic surges in read traffic but remain underutilized during normal operations. This static approach ensures readiness for high-traffic scenarios at the cost of idle capacity and increased expenses during non-peak periods, creating an opportunity for optimization in database management strategies.

**Self-managed architecture:**
<img src="/images/blogs/DBBLOG-2824_UC2-SM.png" alt="" width="55%">
In this use case, this architectural advantage eliminates the need for a dedicated secondary instance solely for failover replication purposes. As a result, by migrating from the self-managed architecture to Aurora, the cluster footprint is reduced from four nodes to three. While this reduction in compute resources does offer some cost savings, it’s not the primary source of economic benefit in this scenario. The majority of cost efficiencies come from Aurora’s [revolutionary storage architecture](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html).

In contrast to traditional database setups where each node typically has its own independent storage containing a separate copy of the data, replicated from the source instance, Aurora employs a fundamentally different approach. While the self-managed architecture not only decreases performance capacity due to replication overhead, it also quadruples the storage costs in this use case. In contrast, all Aurora instances—both primary and read replicas—connect to the same shared storage volume. This [Aurora storage cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html) inherently provides resilience by distributing six copies of data across three Availability Zones. Customers are then charged for only a single copy of the data, effectively reducing the storage cost in this architecture from four to one.

Assuming similar instance types (r6g.xlarge, db.r6g.xlarge) between the self-managed and Aurora architectures, with 2 TB of [io2 provisioned input/output operations per second (IOPS)](https://docs.aws.amazon.com/ebs/latest/userguide/provisioned-iops.html#io2-block-express) SSD storage at 64,000 IOPS in the self-managed architecture and [Aurora I/O-Optimized](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.Overview.StorageReliability.html#aurora-storage-type) (a storage option designed for improved price performance and predictability for I/O-intensive applications) for the Aurora architecture, the cost differences become apparent. Let’s compare the monthly costs of the self-managed architecture versus the Aurora solution:

To implement these functional requirements, a new platform was necessary. This platform needed three core capabilities: real-time ingestion of various feature types, storage with a consistent schema, and a fast response to diverse query requests. Although these requirements initially seemed vague, designing a general structure allowed for the efficient configuration of data ingestion pipelines, storage methods, and serving schemas, leading to clearer development goals.

In addition to functional requirements, the technical requirements included:

* **Serving traffic:** 1,500 or more requests per second (RPS)
* **Ingestion traffic:** 400 or more writes per second (WPS)
* **Top N values:** 30–50
* **Single feature size:** Up to 8 KB
* **Total number of features:** Over 3 billion or more

At the time, the variety and number of features being used were limited, and the recommendation models were simple, leading to modest technical requirements. However, considering the rapid growth rate, a significant increase in system requirements was anticipated. Based on this prediction, higher goals were set beyond the initial requirements. As of February 2025, serving and ingestion traffic has increased by about 90 times compared to the initial requirements, and the total number of features has increased by hundreds of times. The ability to handle this rapid growth was made possible by the highly scalable architecture of the feature platform, which we discuss in the following sections.

---

## Solution Overview

> *Diagram illustrating the architecture of the feature platform.*
> 
> <img src="/images/bl1-2.png" alt="" width="55%">

The feature platform consists of three main components: feature serving, a stream ingestion pipeline, and a batch ingestion pipeline.

Part 1 of this series will cover **feature serving**. Feature serving is the core function of receiving requests from clients and providing the necessary features. Karrot designed this system with four main components:

* **Server** – A server that receives and processes feature serving requests, and is a pod running on [Amazon Elastic Kubernetes Service](https://aws.amazon.com/eks/) (Amazon EKS).
* **Remote cache** – A remote cache layer shared by the servers, using ElastiCache.
* **Database** – A persistence layer that stores features, using [Amazon DynamoDB](https://aws.amazon.com/dynamodb/).
* **On-demand feature server** – A server that serves features that cannot be stored in the remote cache and database due to compliance issues, or that require real-time computation each time.

From a data store perspective, feature serving should serve high-cardinality features with low latency at a large scale. Karrot introduced a multi-level cache and classified serving strategies according to the characteristics of the features:

* **Local cache (tier 1 cache)** – An in-memory store located inside the server, suitable for cases where the data size is small and frequently accessed or requires a fast response time.
* **Remote cache (tier 2 cache)** – Suitable for cases where the data size is medium and frequently accessed.
* **Database (tier 3 cache)** – Suitable for cases where the data size is large and not frequently accessed or is less sensitive to response time.

---

## Schema Design

The feature platform stores multiple features together using the concept of **feature groups**, similar to column families. All feature groups are defined via a feature group schema, called **feature group specifications**, and each feature group specification defines the name of the feature group, the required features, etc.

Based on this concept, the key design is defined as follows:

Partition key: <feature_group_name>#<feature_group_id>
Sort key: <feature_group_timestamp> or a string representing null

To illustrate how this works in practice, let's explore an example of a feature group representing the *recently clicked flea market articles* by user 1234. Consider the following scenario:

* **Feature group name:** recent_user_clicked_fleaMarketArticles
* **User ID:** 1234
* **Click timestamp:** 987654321
* **Features in the feature group:**
    * Clicked article ID: a
    * User session ID: 1111

In this example, the keys and feature group are created as follows:

Partition key: recent_user_clicked_fleaMarketArticles#1234
Sort Key: 987654321
Value: {"0": "a", "1": "1111"}

The features defined in the feature group specification maintain a fixed order, using this order as an enum when saving the feature group.

---

## Feature Serving Read/Write Flow

The feature platform uses a multi-level cache and database for feature serving, as shown in the following diagram.

> *Diagram illustrating the read/write flow of feature serving.*
> <img src="/images/bl1-3.png" alt="" width="55%">

To illustrate this process, let's consider how the system retrieves feature groups 1, 2, and 3 from flea market articles. The **read flow** (solid line in the previous diagram) demonstrates data access optimization using a multi-level cache strategy:

1.  When a query request arrives, first check the **local cache**.
2.  Data not in the local cache is searched for in **ElastiCache**.
3.  Data not in ElastiCache is searched for in **DynamoDB**.
4.  The feature groups found at each stage are collected and returned as the final response.

The **write flow** (dotted line in the previous diagram) includes the following steps:

1.  Feature groups that resulted in cache misses are stored at each cache level.
2.  Data not found in the local cache but found in the remote cache or database is stored in the upper-level cache.
3.  Data found in ElastiCache is stored in the local cache.
4.  Data found in DynamoDB is stored in both ElastiCache and the local cache.
5.  Cache write operations are performed asynchronously in the background.

This approach presents a strategy for maintaining data consistency and improving future access times in a multi-level cache structure. In an ideal situation, serving would work well without any issues with just the previous flow. However, reality is not so. Problems encountered include cache misses, consistency, and penetration issues:

* **Cache miss problem** – Frequent cache misses slow down response times and place a burden on the next-level cache or database. Karrot uses the [Probabilistic Early Expirations](https://cseweb.ucsd.edu//~varghese/papers/caching-in-cache-ton.pdf) (PEE) technique to proactively refresh data likely to be retrieved again in the future, thereby maintaining low latency and minimizing cache stampedes.
* **Cache consistency problem** – If the Time-To-Live (TTL) of the cache is set incorrectly, it can affect recommendation quality or reduce system efficiency. Karrot sets separate soft and hard TTLs, and sometimes uses a [write-through caching strategy](https://docs.aws.amazon.com/whitepapers/latest/database-caching-strategies-using-redis/write-through.html) to synchronize the cache and database to alleviate consistency issues. Additionally, [jitter](https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) is added to distribute TTL expiration times to mitigate [cache stampede](https://en.wikipedia.org/wiki/Cache_stampede) for feature groups written at similar times.
* **Cache penetration problem** – Continuous queries for non-existent feature groups can lead to DynamoDB queries, increasing costs and response times. The platform addresses this problem through [negative caching](https://aws.amazon.com/blogs/database/caching-empty-results-with-amazon-elasticache-for-redis/), storing information about non-existent feature groups to reduce unnecessary database queries. Additionally, the system monitors the rate of missing feature groups in DynamoDB, negative cache hit rates, and potential consistency issues.

---

## Future Improvements for Feature Serving

Karrot is considering the following future improvements for their feature serving solution:

* **Large data caching** – Recently, the demand for storing large data features is increasing. This is because as Karrot grows, the number of features also increases. Additionally, as the demand for embeddings has grown with the rapid development of large language models (LLMs), the size of the data needing to be stored has increased. Accordingly, we are considering more efficient serving by using an embedded database.
* **Efficient cache usage:** Even if an effective TTL value is set initially, efficiency tends to decrease as user usage patterns change and models are modified. Also, as more feature groups are defined, monitoring becomes more difficult. It needs to be simple to find the optimal TTL value for the cache based on data. We are considering a method to use memory efficiently while maintaining high recommendation quality through cache hit rate and feature group loss prevention. Should we cache a feature group that is retrieved only once? What about a feature group retrieved twice? The current feature platform tries to cache even if a cache miss occurs only once. We believe that all feature groups that miss the cache are worth caching. This naturally increases caching inefficiency. An advanced policy is needed to identify and cache feature groups that are worth caching based on various data. This will increase cache usage efficiency.
* **Multi-level cache optimization:** Currently, the feature platform has a multi-level cache structure, and complexity will increase if an embedded database is added in the future. Therefore, it is necessary to find and set optimal settings by considering different cache levels. In the future, we will try to maximize efficiency by considering different levels of cache settings.

---

## Conclusion

In this article, we reviewed how Karrot built their feature platform, focusing on feature serving capabilities. As of February 2025, the platform reliably handles over 100,000 RPS with a P99 latency of less than 30 milliseconds, providing stable recommendation services through a scalable architecture that efficiently manages traffic increases.

[Part 2]({{< ref "/3-blogstranslated/3.2-blog2" >}}) will explore how features are created using consistent feature schemas and ingestion pipelines through the feature platform.