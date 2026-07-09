# Data Engineering Frameworks and Ecosystem

> *"Frameworks do not replace fundamentals; they package architectural decisions for recurring problems."*

← [Back to index](./0-data-engineering.md)


## Why Learn the Ecosystem?

Modern data engineering combines ingestion, storage, processing, orchestration, quality, governance, and infrastructure tools. Knowing the main frameworks helps you choose the right technology for volume, latency, cost, team maturity, and reliability requirements.

A good data engineer does not choose Spark, Kafka, or Airflow because they are popular; they choose them because they understand the problem each tool solves and the trade-offs involved.


## Ecosystem Map

| Category | Common tools | Problem they solve |
|---|---|---|
| Distributed processing | Apache Spark, PySpark, Hadoop MapReduce | Transform large data volumes on a cluster |
| Distributed storage | HDFS, S3, GCS, ADLS | Persist scalable and cost-efficient data |
| Messaging and streaming | Apache Kafka, Kafka Connect, Debezium | Capture events and changes in near real time |
| Stream processing | Apache Flink, Spark Structured Streaming, Kafka Streams | Process events continuously with low latency |
| Lakehouse and table formats | Delta Lake, Apache Iceberg, Apache Hudi | Add transactions, versioning, and schema evolution to the Data Lake |
| Distributed SQL | Trino, Presto, Hive | Query distributed data using SQL |
| Analytics transformation | dbt | Version, test, and document SQL transformations |
| Orchestration | Airflow, Dagster, Prefect | Schedule and coordinate workflows |
| Data quality | Great Expectations, Soda, dbt tests | Validate expectations and rules over data |
| Catalog and lineage | DataHub, OpenLineage, Marquez | Document datasets, owners, and dependencies |
| Infrastructure | Docker, Kubernetes, Terraform | Package, scale, and provision environments |

Frameworks and engines solve specific parts of the problem. Platforms such as Databricks, Snowflake, BigQuery, Redshift, and Microsoft Fabric combine many of these capabilities into an integrated environment. For that deeper view, see [Modern Data Platforms](./4-modern-data-platforms.md).


## Apache Spark and PySpark

Apache Spark is a unified engine for distributed processing. It supports batch, SQL, streaming, machine learning, and graph workloads. PySpark is Spark's Python API, widely used by teams that already work with Python.

**Use it when:**
- The volume exceeds the comfortable capacity of a single machine.
- The pipeline needs to combine large files, heavy joins, and distributed aggregations.
- The environment already uses Databricks, EMR, Dataproc, Synapse, or Kubernetes.

**Avoid it when:**
- The dataset fits comfortably in DuckDB, Polars, Pandas, or the Data Warehouse itself.
- Cluster operational complexity is not justified.
- The transformation is purely SQL and runs well with dbt in the warehouse.

**Essential concepts:**
- DataFrame API
- Lazy evaluation
- Partitions
- Shuffle
- Broadcast join
- Cache and persist
- Adaptive Query Execution (AQE)
- Spark Structured Streaming

**Mental example:**

```text
Read raw Parquet → filter valid records → aggregate by key → write gold layer
```

Spark shines when these steps need to be distributed across many machines.


## Apache Hadoop

Hadoop was the ecosystem that popularized large-scale distributed storage and processing. Its classic building blocks are:

| Component | Function |
|---|---|
| HDFS | Distributed file system |
| MapReduce | Batch distributed processing model |
| YARN | Cluster resource manager |
| Hive | SQL layer over distributed data |

Pure Hadoop is less common in new projects today, but its concepts remain important. HDFS, YARN, Hive Metastore, partitioning, and columnar files directly influenced Data Lakes, Spark, and cloud platforms.

**Use it when:** you work with legacy environments, on-premise clusters, or platforms that still depend on HDFS/YARN.

**Avoid it when:** the company already has cloud object storage and managed services that are simpler to operate.


## Apache Kafka and Kafka Connect

Kafka is an event streaming platform used to publish, store, and consume events with high throughput.

**Main uses:**
- Application event ingestion.
- Asynchronous integration between systems.
- Streaming logs, clicks, transactions, and sensor data.
- CDC with Debezium.

**Essential concepts:**
- Topic
- Partition
- Producer
- Consumer group
- Offset
- Retention
- Schema Registry

Kafka Connect complements Kafka with ready-made connectors for databases, files, object storage, Data Warehouses, and SaaS systems.


## Apache Flink

Flink is a stateful stream processing engine. Unlike engines centered on batch, Flink was designed to handle continuous event streams with state, windows, event time, and low latency.

**Use it when:**
- The use case requires real-time decisions.
- There is complex state by user, account, session, or device.
- Events may arrive late and need event-time handling.

**Examples:** fraud detection, operational monitoring, real-time alerts, continuous event enrichment.


## dbt

dbt is a SQL-first tool for analytics transformation. It creates versioned, testable, documented SQL models, usually on top of BigQuery, Snowflake, Redshift, Databricks, PostgreSQL, or DuckDB.

**Use it when:**
- Transformation happens inside the Data Warehouse or Lakehouse.
- The team is strong in SQL.
- The project needs lineage, tests, and metric documentation.

**Avoid it when:**
- Processing requires complex Python logic.
- The volume needs a distributed job outside the warehouse.
- Latency is continuous streaming.


## Airflow, Dagster, and Prefect

Orchestration tools coordinate pipeline execution. They should not concentrate all transformation logic; their main role is to define dependencies, schedules, retries, parameters, and operational observability.

| Tool | Profile |
|---|---|
| Airflow | Mature industry standard, excellent for batch workflows |
| Dagster | Strong in assets, typing, testing, and data platform thinking |
| Prefect | Good developer experience and flexible execution |


## Delta Lake, Apache Iceberg, and Apache Hudi

Table formats add database-like capabilities to the Data Lake:

- ACID transactions
- Time travel
- Schema evolution
- Upserts and deletes
- Compaction
- Snapshot control
- Integration with multiple engines

| Format | Strengths |
|---|---|
| Delta Lake | Strong integration with Spark and Databricks |
| Apache Iceberg | Broad multi-engine adoption and modern metadata design |
| Apache Hudi | Strong in incremental ingestion, upserts, and CDC |

These formats are central to Lakehouse architectures.


## Trino, Presto, and Hive

Trino and Presto are distributed SQL engines used to query data across different sources, such as object storage, relational databases, and Hive/Iceberg/Delta catalogs.

**Use them when:**
- You need to query data without moving everything into a single warehouse.
- The use case is SQL exploration, federation, or a semantic access layer.
- Data is stored as Parquet/ORC in the Data Lake.

Hive remains important because of legacy deployments and Hive Metastore, still used as a catalog in many environments.


## Ingestion Tools

| Tool | Common use |
|---|---|
| Airbyte | Open source EL connectors for APIs, databases, and SaaS |
| Fivetran | Managed ingestion with low operational effort |
| Apache NiFi | Visual flows, routing, and lightweight data transformation |
| Debezium | CDC from relational database logs |
| Kafka Connect | Source and sink connectors integrated with Kafka |


## How to Choose

| Question | Likely choice |
|---|---|
| Does it fit on one machine? | DuckDB, Polars, or Pandas |
| Is it SQL transformation in the warehouse? | dbt |
| Is it large-scale distributed batch? | Spark/PySpark |
| Is it streaming with complex state? | Flink |
| Is it event or CDC at scale? | Kafka + Debezium |
| Is it a Data Lake with transactions? | Delta Lake, Iceberg, or Hudi |
| Is it job coordination? | Airflow, Dagster, or Prefect |
| Is it federated query? | Trino/Presto |


## Best Practices

- Choose tools based on the problem, not popularity.
- Prefer managed services when cluster operation is not a competitive advantage.
- Keep heavy transformation logic out of the orchestrator.
- Use columnar formats such as Parquet or ORC for analytics.
- Standardize names, partitions, layers, and data contracts.
- Monitor cost, execution time, processed volume, and failures.
- Document owners, SLAs, lineage, and consumers for each relevant dataset.


## References

- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [Apache Hadoop Documentation](https://hadoop.apache.org/docs/current/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Apache Flink Documentation](https://nightlies.apache.org/flink/flink-docs-stable/)
- [dbt Documentation](https://docs.getdbt.com/)
- [Apache Airflow Documentation](https://airflow.apache.org/docs/apache-airflow/stable/)
- [Delta Lake Documentation](https://docs.delta.io/)
- [Apache Iceberg Documentation](https://iceberg.apache.org/docs/latest/)
- [Apache Hudi Documentation](https://hudi.apache.org/docs/overview/)
- [Trino Documentation](https://trino.io/docs/current/)


← [Governance and Security](./15-governance-and-security.md) · [Back to index](./0-data-engineering.md) · [Cloud Comparison →](./17-cloud-comparison.md)


*Documentation in progress · Personal portfolio*
