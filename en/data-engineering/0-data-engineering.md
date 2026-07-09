# Data Engineering

[Português (Brasil)](../../pt-br/engenharia-de-dados/0-engenharia-de-dados.md) | English

> *"Data is the new oil — but crude oil needs to be refined to have value."*


## What Is Data Engineering?

Data Engineering is the discipline responsible for **designing, building, and maintaining the infrastructure and systems** that enable data to be collected, stored, transformed, and made available in a reliable, scalable, and efficient way.

While data scientists analyze and extract insights from data, it is data engineers who ensure that the data exists, is accessible, and is reliable enough to be used. In other words: **the data engineer builds the pipes; others drink the water**.

The role grew out of software engineering and database administration, but it took on its own identity with the exponential growth of data and the need for robust batch and real-time pipelines.


## Main Concepts

Below is a learning path organized around how data engineering work naturally unfolds: understand, design, establish the foundation, capture, store, transform, automate, make reliable, govern, and serve data to the business.


### 🏗️ [Data Architecture](./1-data-architecture.md)

Defines how the components of a data system are organized and interact. It includes decisions about where data is stored, how it moves between systems, and which architectural patterns are adopted.

Key concepts:
- Data Warehouse
- Data Lake
- Data Lakehouse
- Lambda Architecture
- Kappa Architecture
- Medallion Architecture


### 🧱 [Data Modeling](./2-data-modeling.md)

Foundations and decisions for conceptual, logical, and physical modeling. Includes entities, relationships, keys, cardinality, normalization, dimensional modeling, Data Vault, Lakehouse modeling, and a production checklist.


### ☁️ [Cloud and Infrastructure](./3-cloud-and-infrastructure.md)

Most modern data systems run in the cloud. Understanding services, networking, IAM, IaC, containers, serverless, cost, and scalability is essential for designing reliable platforms.

Main providers: AWS, Google Cloud (GCP), Microsoft Azure.


### 🏢 [Modern Data Platforms](./4-modern-data-platforms.md)

A deeper look at integrated platforms such as Databricks, Snowflake, BigQuery, Redshift, Microsoft Fabric, Synapse, Glue, EMR, Dataproc, and Dataflow. It shows how they combine frameworks, services, governance, compute, storage, and development experiences.


### 📥 [Data Ingestion](./5-data-ingestion.md)

It is the process of **capturing and importing data** from multiple sources, such as databases, APIs, files, events, and streams, into the centralized data environment.

Common tools: Apache Kafka, AWS Kinesis, Airbyte, Fivetran, NiFi.


### 🌊 [Streaming and Events](./6-streaming-and-events.md)

A deeper look at event-driven architectures, Kafka, partitioning, Schema Registry, CDC, Debezium, event time, watermarks, delivery semantics, DLQ, and replay.


### 🗄️ [Data Storage](./7-data-storage.md)

Covers the technologies and strategies used to **persist data** efficiently, taking cost, performance, and access patterns into account.

Main categories:
- Relational databases (OLTP)
- Data Warehouses (OLAP)
- Data Lakes and Lakehouses
- NoSQL databases
- File formats


### ⚙️ [Data Processing](./8-data-processing.md)

Involves **turning raw data into useful data**, applying cleansing, aggregation, enrichment, and business rules. This is where data becomes meaningful.

Common tools: Apache Spark, dbt, Flink, Trino, DuckDB, Pandas.


### 🧮 [Advanced SQL](./9-advanced-sql.md)

Advanced SQL techniques used in data engineering: CTEs, window functions, deduplication, joins, incremental models, MERGE, performance, EXPLAIN, and best practices for analytical queries.


### 🔁 [Data Pipelines](./10-data-pipelines.md)

A data pipeline is the **set of automated steps** that move and transform data from the source to the final destination, such as dashboards, ML models, APIs, or data products.

Good practices include: idempotency, traceability, error handling, and reprocessing.


### 🎼 [Orchestration](./11-orchestration.md)

Orchestration is the **management and scheduling of pipeline execution**, ensuring that tasks run in the correct order, at the right time, and with proper failure handling.

Common tools: Apache Airflow, Prefect, Dagster, Mage.


### 🚀 [DataOps and CI/CD for Data](./12-dataops-and-cicd.md)

Engineering practices to version, test, publish, and operate data pipelines safely. Includes Git, environments, tests, CI/CD, secrets, deployment, rollback, and operational observability.


### ✅ [Data Quality](./13-data-quality.md)

Ensures that data is **accurate, complete, consistent, and reliable**. Without quality, any analysis, metric, or model is compromised.

Common tools: Great Expectations, Soda, dbt tests, Monte Carlo.


### 🔍 [Observability and Monitoring](./14-observability.md)

Involves **monitoring the health of pipelines and data** in production, detecting anomalies, failures, and deviations before they impact consumers.

It includes: logs, metrics, alerts, lineage, freshness, and data cataloging.


### 🔐 [Governance and Security](./15-governance-and-security.md)

Defines **policies, roles, and controls** to ensure that data is used ethically, securely, and in compliance with regulations such as LGPD and GDPR.

It covers: access control, encryption, masking, cataloging, classification, and retention policies.


### 🧰 [Frameworks and Ecosystem](./16-frameworks-and-ecosystem.md)

A deeper look at the main frameworks and tools used in data engineering, such as Spark, PySpark, Hadoop, Kafka, Flink, dbt, Airflow, Delta Lake, Iceberg, Hudi, Trino, and quality, catalog, and infrastructure tools.


### 🌐 [Cloud Comparison for Data Engineering](./17-cloud-comparison.md)

Comparison of the main cloud providers (AWS, GCP, Azure) in relation to the data services they offer, costs, ease of use, and recommended use cases.


### 🧪 [Advanced Techniques](./18-advanced-techniques.md)

A set of practices for making pipelines more robust in production: partitioning, compaction, Spark tuning, CDC, upserts, idempotency, backfill, watermarks, data contracts, observability, and cost optimization.


### 📐 [Semantic Layer and Analytics Engineering](./19-semantic-layer-and-analytics-engineering.md)

How to turn tables into consistent metrics, dimensions, and business concepts. Includes data marts, certified metrics, dbt Semantic Layer, LookML, Cube, contracts, and metric governance.


## How To Use This Documentation

Each section above is an entry point into a dedicated document with greater depth. The idea is that you navigate the topics according to your needs:

- 🟢 **Beginner?** Start with [Architecture](./1-data-architecture.md), [Data Modeling](./2-data-modeling.md), [Ingestion](./5-data-ingestion.md), and [Storage](./7-data-storage.md).
- 🟡 **Intermediate?** Go deeper into [Processing](./8-data-processing.md), [Advanced SQL](./9-advanced-sql.md), [Pipelines](./10-data-pipelines.md), [Orchestration](./11-orchestration.md), and [Quality](./13-data-quality.md).
- 🔴 **Advanced?** Explore [DataOps](./12-dataops-and-cicd.md), [Observability](./14-observability.md), [Governance](./15-governance-and-security.md), [Frameworks](./16-frameworks-and-ecosystem.md), [Advanced Techniques](./18-advanced-techniques.md), and [Semantic Layer](./19-semantic-layer-and-analytics-engineering.md).


## References and Recommended Reading

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- **Designing Data-Intensive Applications** — Martin Kleppmann (O'Reilly)
- **The Data Engineering Cookbook** — Andreas Kretz
- [DataEngineering.wiki](https://dataengineering.wiki)
- [awesome-data-engineering (GitHub)](https://github.com/igorbarinov/awesome-data-engineering)


*Documentation in progress · Personal portfolio*
