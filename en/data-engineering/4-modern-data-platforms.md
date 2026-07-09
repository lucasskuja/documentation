# Modern Data Platforms

> *"A data platform integrates engines, storage, governance, and operations so teams do not have to assemble everything from scratch."*

← [Back to index](./0-data-engineering.md)


## What Is a Data Platform?

A data platform is an integrated environment that combines different capabilities required to build, operate, and govern data solutions. Unlike an isolated framework, a platform usually brings together processing, storage, SQL analytics, notebooks, orchestration, security, cataloging, governance, sharing, and integration with BI, ML, and AI tools.

**Examples of the difference:**

| Type | Examples | Role |
|---|---|---|
| Framework / engine | Spark, Flink, Kafka, Trino, dbt | Executes a specific function |
| Format / pattern | Parquet, Delta Lake, Iceberg, Hudi | Defines how data is stored or versioned |
| Cloud service | BigQuery, Redshift, Glue, EMR, Dataflow | Managed service inside a cloud |
| Integrated platform | Databricks, Snowflake, Microsoft Fabric | Combines multiple capabilities into a product/platform |

In practice, many platforms integrate or abstract frameworks. Databricks uses Spark and Delta Lake as central pieces; Snowflake integrates SQL, warehouses, Snowpark, tasks, streams, and sharing; Fabric integrates OneLake, Data Factory, Data Engineering, Data Warehouse, Real-Time Intelligence, and Power BI.


## Overview

| Platform | Main profile | Best for |
|---|---|---|
| Databricks | Lakehouse and data + AI | Spark, Delta Lake, ML, streaming, notebooks, unified governance |
| Snowflake | Cloud Data Platform / Data Cloud | SQL analytics, ELT, data sharing, governance, and simple operations |
| BigQuery | Serverless Data Warehouse | Analytics at scale, low operations, SQL, integrated ML, and data on GCP |
| Amazon Redshift | AWS Data Warehouse | Analytics in the AWS ecosystem, S3 integration, and SQL workloads |
| Microsoft Fabric | SaaS analytics platform | Microsoft organizations, OneLake, Power BI, and integrated experiences |
| Azure Synapse | Integrated Azure analytics | Existing Azure environments with SQL, Spark, and pipelines |
| AWS Glue | Serverless integration | Serverless ETL/ELT, Data Catalog, crawlers, and Spark jobs |
| Amazon EMR | Managed big data | Spark/Hadoop/Flink with more operational control |
| Dataproc | Managed Spark/Hadoop on GCP | Clusters or serverless Spark on Google Cloud |
| Dataflow | Managed Apache Beam | Unified batch and streaming, event-driven pipelines |


## Databricks

Databricks is a Lakehouse/Data Intelligence platform for data engineering, analytics, machine learning, and AI. It combines distributed processing, open storage formats, SQL analytics, notebooks, workflows, and governance.

**Important components:**

| Component | Function |
|---|---|
| Apache Spark | Main engine for distributed processing |
| Delta Lake | Table format with transactions, time travel, upserts, and schema enforcement |
| Databricks SQL | SQL warehouses for BI and analytics |
| Workflows | Orchestration of jobs, notebooks, and pipelines |
| Unity Catalog | Catalog, permissions, lineage, and governance |
| Delta Live Tables | Declarative pipelines for ingestion and transformation |
| MLflow | Machine learning lifecycle |
| Notebooks | Collaborative development in SQL, Python, Scala, and R |

**Use it when:**
- The team needs managed Spark with a strong developer experience.
- The architecture is a Lakehouse based on Delta Lake.
- Workloads combine data engineering, ML, AI, and streaming.
- Centralized governance for data and models is important.
- The organization wants a relatively consistent multi-cloud layer.

**Avoid it when:**
- Most workloads are simple SQL and fit well in a serverless Data Warehouse.
- The team does not need Spark, notebooks, or integrated ML.
- Cluster and warehouse cost will not be governed well.

**Attention point:** Databricks greatly reduces the complexity of operating Spark, but it does not remove the need for good partitioning, tuning, permissions, cost, and modeling practices.


## Snowflake

Snowflake is a cloud data platform with strong focus on SQL analytics, separation between storage and compute, simplified operations, governance, and data sharing.

**Important components:**

| Component | Function |
|---|---|
| Virtual Warehouses | Isolated compute for queries and workloads |
| Databases, schemas, and tables | Logical organization of data |
| Snowpark | Run Python, Java, or Scala code close to the data |
| Streams | Track changes in tables |
| Tasks | Schedule and automate SQL/workflows |
| Snowpipe | Continuous or automated ingestion |
| Data Sharing | Secure data sharing |
| Time Travel | Historical query and recovery |
| Iceberg Tables | Integration with data in open formats |

**Use it when:**
- The core use case is SQL analytics, BI, and ELT.
- The team wants low operations and strong governance.
- There are many data consumers and a need for sharing.
- The organization is working with a multi-cloud strategy.
- Operational simplicity is more valuable than fine-grained engine control.

**Avoid it when:**
- The workload heavily depends on native Spark.
- Processing requires libraries or patterns far outside the Snowflake ecosystem.
- The architecture requires detailed control over cluster, runtime, and filesystem.

**Attention point:** Snowflake is excellent for analytics and ELT, but it still requires discipline around modeling, warehouses, clustering, costs, and pipeline design.


## Google BigQuery

BigQuery is Google Cloud's serverless Data Warehouse. It is widely used for large-scale analytics with little infrastructure administration.

**Important components:**

| Component | Function |
|---|---|
| Datasets and tables | Logical organization of data |
| Slots / reservations | Capacity model for predictable workloads |
| On-demand queries | Billing based on processed data |
| Partitioning and clustering | Cost and performance optimization |
| BigQuery ML | ML models using SQL |
| BigQuery Data Transfer Service | Managed ingestion from common sources |
| BigLake | Governance and querying over object storage data |
| Storage API | High-performance reads and writes |

**Use it when:**
- The team wants serverless analytics with little operational overhead.
- Data and tools already live on GCP.
- Large-scale analytical queries are the priority.
- The team wants to integrate SQL analytics with ML and external data.

**Avoid it when:**
- Query cost predictability is not yet well governed.
- The organization is strongly standardized on another cloud.
- The workload requires fine-grained control over distributed runtime.


## Amazon Redshift

Amazon Redshift is AWS's managed Data Warehouse, available in provisioned clusters and serverless mode. It is strong for organizations that already concentrate data, security, and operations in the AWS ecosystem.

**Important components:**

| Component | Function |
|---|---|
| Redshift Serverless | Execution without managing provisioned clusters |
| Provisioned clusters | More control over capacity and configuration |
| Redshift Spectrum | Query data directly in S3 |
| RA3 nodes | Separation between compute and managed storage |
| COPY | Efficient data loading |
| Materialized views | Optimization of recurring queries |
| AWS integration | S3, Glue Data Catalog, IAM, Lake Formation |

**Use it when:**
- The environment is mostly AWS.
- The team needs a SQL Data Warehouse integrated with S3.
- There are strong IAM, VPC, Lake Formation, and AWS service requirements.

**Avoid it when:**
- The team wants a fully serverless and multi-cloud experience by default.
- The workload is more Lakehouse/Spark/ML than SQL warehouse.


## Microsoft Fabric and Azure Synapse

Microsoft Fabric is a SaaS analytics platform that integrates data experiences around OneLake and the Microsoft ecosystem. Azure Synapse remains common in existing Azure architectures, combining SQL, Spark, and pipelines.

**Important components in Fabric:**

| Component | Function |
|---|---|
| OneLake | Single storage layer for the organization |
| Data Factory | Data ingestion and integration |
| Data Engineering | Spark notebooks, lakehouses, and pipelines |
| Data Warehouse | Analytical SQL workloads |
| Real-Time Intelligence | Events, streaming, and real-time analysis |
| Power BI | Consumption, dashboards, and semantic layer |
| Purview integration | Governance, catalog, and classification |

**Use it when:**
- The organization already uses Microsoft 365, Azure, Power BI, and Entra ID.
- The goal is to reduce friction between engineering, analytics, and BI.
- The team wants a more integrated SaaS experience.

**Avoid it when:**
- The organization needs broad multi-cloud portability.
- The team wants to assemble an open stack with detailed control of each piece.


## AWS Glue and Amazon EMR

Glue and EMR are AWS services that often appear together in data architectures.

| Service | Best for |
|---|---|
| AWS Glue | Serverless ETL/ELT, catalog, crawlers, Spark jobs, and integration |
| Amazon EMR | Managed clusters for Spark, Hadoop, Hive, Flink, Trino, and custom workloads |

**Use Glue when:**
- You want fewer operations.
- The job is common ETL/ELT in Spark or Python.
- Glue Data Catalog is the central Data Lake catalog.

**Use EMR when:**
- You need more control over cluster, versions, and configuration.
- The workload requires varied frameworks or deeper tuning.
- The team can operate clusters with cost discipline.


## Dataproc and Dataflow

On Google Cloud, Dataproc and Dataflow cover different processing profiles.

| Service | Best for |
|---|---|
| Dataproc / Managed Service for Apache Spark | Managed or serverless Spark/Hadoop |
| Dataflow | Apache Beam batch and streaming pipelines |

**Use Dataproc when:**
- The workload is Spark, PySpark, Hadoop, Hive, Flink, or Trino.
- You want to migrate existing Hadoop/Spark workloads to GCP.
- The team needs more control over big data runtime.

**Use Dataflow when:**
- The pipeline is event-driven or streaming.
- You want a unified batch + streaming model with Apache Beam.
- The focus is continuous processing with managed autoscaling.


## Comparison by Use Case

| Use case | Candidate platforms |
|---|---|
| BI and simple SQL analytics | BigQuery, Snowflake, Redshift, Fabric Warehouse |
| Lakehouse with Spark | Databricks, EMR, Dataproc, Synapse/Fabric Data Engineering |
| Multi-cloud | Snowflake, Databricks |
| AWS ecosystem | Redshift, Glue, EMR, Athena, Lake Formation |
| GCP ecosystem | BigQuery, Dataproc, Dataflow, Dataplex |
| Microsoft ecosystem | Fabric, Synapse, Azure Databricks, Power BI |
| Complex streaming | Managed Flink, Dataflow, Databricks Structured Streaming |
| ML/AI close to data | Databricks, BigQuery, Snowflake, Fabric |
| External data sharing | Snowflake, Databricks, BigQuery Analytics Hub |
| Lowest possible operations | BigQuery, Snowflake, Fabric, Glue |


## How to Choose a Platform

| Question | Likely direction |
|---|---|
| Is the workload mostly SQL? | BigQuery, Snowflake, Redshift, Fabric Warehouse |
| Does it need intensive Spark? | Databricks, EMR, Dataproc, Fabric/Synapse Spark |
| Does it need notebooks and ML close to data? | Databricks, Fabric, BigQuery notebooks, Snowpark |
| Does it need to be multi-cloud? | Snowflake or Databricks |
| Is the company already AWS/GCP/Azure? | Start with the native cloud platform |
| Is the team small? | Prefer serverless and SaaS |
| Does cost need to be very predictable? | Use reservations/capacity planning and monitor workloads |
| Is there strong regulation? | Prioritize catalog, lineage, IAM, auditing, and masking |


## Open Source vs Managed Platform

| Approach | Advantages | Disadvantages |
|---|---|---|
| Internally assembled open source | Control, portability, customization | More operations, security, and maintenance |
| Managed platform | Less operations, integration, support, ready governance | Cost, lock-in, and less fine-grained control |
| Hybrid | Balance between control and productivity | Requires well-defined architecture and governance |

A mature decision is rarely "open source or managed" in absolute terms. The most common approach is to combine open tools, cloud services, and integrated platforms according to criticality, cost, team, and maturity.


## Best Practices

- Define workloads before choosing the platform.
- Separate storage, compute, catalog, orchestration, and consumption.
- Monitor cost by domain, pipeline, workspace, warehouse, or cluster.
- Create naming, layer, and permission standards.
- Use separate environments for development, staging, and production.
- Centralize governance, lineage, and sensitive data classification.
- Avoid putting critical logic only in unversioned notebooks.
- Document when to use SQL, Spark, ELT, streaming, or notebooks.
- Reassess the architecture when volume, latency, or usage patterns change.


## References

- [Databricks Documentation](https://docs.databricks.com/)
- [Snowflake Documentation](https://docs.snowflake.com/)
- [Google BigQuery Documentation](https://cloud.google.com/bigquery/docs)
- [Amazon Redshift Documentation](https://docs.aws.amazon.com/redshift/)
- [Microsoft Fabric Documentation](https://learn.microsoft.com/en-us/fabric/)
- [AWS Glue Documentation](https://docs.aws.amazon.com/glue/)
- [Amazon EMR Documentation](https://docs.aws.amazon.com/emr/)
- [Google Cloud Managed Service for Apache Spark](https://cloud.google.com/dataproc/docs)
- [Google Cloud Dataflow Documentation](https://cloud.google.com/dataflow/docs)


← [Cloud and Infrastructure](./3-cloud-and-infrastructure.md) · [Back to index](./0-data-engineering.md) · [Data Ingestion →](./5-data-ingestion.md)


*Documentation in progress · Personal portfolio*
