# Data Engineering

[Português (Brasil)](../../pt-br/engenharia-de-dados/0-engenharia-de-dados.md) | English

> *"Data is the new oil — but crude oil needs to be refined to have value."*


## What Is Data Engineering?

Data Engineering is the discipline responsible for **designing, building, and maintaining the infrastructure and systems** that enable data to be collected, stored, transformed, and made available in a reliable, scalable, and efficient way.

While data scientists analyze and extract insights from data, it is data engineers who ensure that the data exists, is accessible, and is reliable enough to be used. In other words: **the data engineer builds the pipes; others drink the water**.

The role grew out of software engineering and database administration, but it took on its own identity with the exponential growth of data and the need for robust batch and real-time pipelines.


## Main Concepts

Below is a broad view of the pillars that structure Data Engineering. Each topic has its own in-depth documentation.


### 🏗️ [Data Architecture](./1-data-architecture.md)

Defines how the components of a data system are organized and interact. It includes decisions about where data is stored, how it moves between systems, and which architectural patterns are adopted.

Key concepts:
- Data Warehouse
- Data Lake
- Data Lakehouse
- Lambda Architecture
- Kappa Architecture
- Medallion Architecture


### 📥 [Data Ingestion](./2-data-ingestion.md)

It is the process of **capturing and importing data** from multiple sources (databases, APIs, files, streams, and so on) into the centralized data environment.

It can happen in two main ways:
- **Batch:** data collected at regular intervals.
- **Streaming (real time):** data captured and processed continuously.

Common tools: Apache Kafka, AWS Kinesis, Airbyte, Fivetran, NiFi.


### 🗄️ [Data Storage](./3-data-storage.md)

Covers the technologies and strategies used to **persist data** efficiently, taking cost, performance, and access patterns into account.

Main categories:
- Relational databases (OLTP)
- Data Warehouses (OLAP)
- Data Lakes (raw storage in object storage)
- NoSQL databases (document, columnar, graph, key-value)

Common tools: BigQuery, Snowflake, Redshift, Delta Lake, S3, PostgreSQL.


### ⚙️ [Data Processing](./4-data-processing.md)

Involves **turning raw data into useful data**, applying cleansing, aggregation, enrichment, and business rules. This is where data becomes meaningful.

Paradigms: distributed processing, in-memory, batch vs. streaming.

Common tools: Apache Spark, dbt, Flink, Trino, Pandas.


### 🔁 [Data Pipelines](./5-data-pipelines.md)

A data pipeline is the **set of automated steps** that move and transform data from the source to the final destination (dashboards, ML models, APIs, and so on).

A typical pipeline follows the pattern:

```
Source → Ingestion → Storage → Transformation → Destination
```

Good practices include: idempotency, traceability, error handling, and reprocessing.


### 🎼 [Orchestration](./6-orchestration.md)

Orchestration is the **management and scheduling of pipeline execution**, ensuring that tasks run in the correct order, at the right time, and with proper failure handling.

Key concepts: DAGs (Directed Acyclic Graphs), task dependencies, retries, alerts.

Common tools: Apache Airflow, Prefect, Dagster, Mage.


### ✅ [Data Quality](./7-data-quality.md)

Ensures that data is **accurate, complete, consistent, and reliable**. Without quality, any analysis or model is compromised.

Quality dimensions: completeness, uniqueness, validity, consistency, timeliness, accuracy.

Common tools: Great Expectations, Soda, dbt tests, Monte Carlo.


### 🔍 [Observability and Monitoring](./8-observability.md)

Involves **monitoring the health of pipelines and data** in production, detecting anomalies, failures, and deviations before they impact consumers.

It includes: logs, metrics, alerts, lineage (traceability of data origin), and data cataloging.

Common tools: DataHub, OpenLineage, Monte Carlo, Grafana.


### 🔐 [Governance and Security](./9-governance-and-security.md)

Defines **policies, roles, and controls** to ensure that data is used ethically, securely, and in compliance with regulations (LGPD, GDPR).

It covers: access control, encryption, data masking, cataloging, and retention policies.


### ☁️ [Cloud and Infrastructure](./10-cloud-and-infrastructure.md)

Most modern data systems run in the cloud. Understanding infrastructure services and models is essential for the data engineer.

Main providers: AWS, Google Cloud (GCP), Microsoft Azure.

Key concepts: IaC (Infrastructure as Code), containers, serverless, horizontal scalability.

### 🌐 [Cloud Comparison for Data Engineering](./11-cloud-comparison.md)

Comparison of the main cloud providers (AWS, GCP, Azure) in relation to the data services they offer, costs, ease of use, and recommended use cases.


## How To Use This Documentation

Each section above is an entry point into a dedicated document with greater depth. The idea is that you navigate the topics according to your needs:

- 🟢 **Beginner?** Start with [Architecture](./1-data-architecture.md) and [Pipelines](./5-data-pipelines.md).
- 🟡 **Intermediate?** Go deeper into [Orchestration](./6-orchestration.md) and [Quality](./7-data-quality.md).
- 🔴 **Advanced?** Explore [Observability](./8-observability.md) and [Governance](./9-governance-and-security.md).


## References and Recommended Reading

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- **Designing Data-Intensive Applications** — Martin Kleppmann (O'Reilly)
- **The Data Engineering Cookbook** — Andreas Kretz
- [DataEngineering.wiki](https://dataengineering.wiki)
- [awesome-data-engineering (GitHub)](https://github.com/igorbarinov/awesome-data-engineering)


*Documentation in progress · Personal portfolio*
