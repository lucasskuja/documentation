# Data Storage

> *"Where you store your data defines how — and whether — you can use it."*

← [Back to index](./0-data-engineering.md)


## What Is Data Storage?

Data storage is the layer responsible for **persisting data in a durable, organized, and accessible way**. It is one of the most consequential architectural decisions: the type of storage chosen determines performance, cost, scalability, and the types of queries that will be possible.

There is no ideal storage type for every case. The key is understanding the **access pattern** (how and how often data will be read and written) and choosing the technology that best fits that pattern.


## Storage Categories

### 🗃️ Relational Databases (OLTP)

Designed for **operational transactions** — systems that require consistency, referential integrity, and support for many concurrent read and write operations.

**Characteristics:**
- Tabular model with rigid schema
- ACID support (Atomicity, Consistency, Isolation, Durability)
- Standard SQL language
- Excellent for point reads and frequent writes
- Do not scale well for analytical queries over large volumes

**When to use in Data Engineering:** as a data source (the company’s transactional systems), or as a destination for processed data that needs to be queried by applications.

**Main tools:** PostgreSQL, MySQL, SQL Server, Oracle.


### 📊 Data Warehouses (OLAP)

Optimized for **analytical queries** over large volumes of historical data. Unlike OLTP databases, they prioritize reading many rows with aggregations instead of frequent point writes.

**Characteristics:**
- Columnar storage (reads only the required columns)
- Optimized for scans and aggregations
- Schema defined in advance (*schema-on-write*)
- Separation of storage and compute (in modern versions)
- Elastic scalability

**When to use:** analytics, BI reporting, business KPIs, modeled and curated data.

**Main tools:**

| Tool | Provider | Highlight |
|------------|----------|----------|
| BigQuery | Google Cloud | Serverless, query-based billing, unlimited scale |
| Snowflake | Multi-cloud | Full storage/compute separation, easy to operate |
| Redshift | AWS | Integrated with the AWS ecosystem, Redshift Spectrum |
| Azure Synapse | Microsoft Azure | Integrated with Power BI and Azure Data Factory |
| ClickHouse | Open source / Cloud | Extreme performance for real-time analytics |
| DuckDB | Open source (local) | Local, in-process analytics on files |


### 🏞️ Object Storage (Data Lake)

Low-cost, highly scalable **object storage** designed to store any kind of data in its raw format. It is the foundation of modern Data Lakes.

**Characteristics:**
- Very low cost (cents per GB/month)
- Nearly unlimited scalability
- Supports any format (CSV, JSON, Parquet, Avro, images, videos)
- No mandatory schema
- Access via HTTP API (not native SQL)
- Extremely high durability (99.999999999% — "11 nines")

**When to use:** storing raw data, logs, unstructured data, ML datasets, backups, and infrequently accessed data.

**Main tools:**

| Tool | Provider |
|------------|----------|
| Amazon S3 | AWS |
| Google Cloud Storage (GCS) | Google Cloud |
| Azure Data Lake Storage (ADLS) | Microsoft Azure |
| MinIO | Open source (self-hosted) |


### 📁 File Formats

Choosing the file format is just as important as choosing the storage. Poor choices directly impact performance and cost.

#### CSV / TSV
- Simple, human-readable, universally supported
- No typing, no native compression, slow for analytics
- Use only for integration with legacy systems or one-off exports

#### JSON / JSONL
- Flexible for semi-structured and nested data
- No native compression, verbose
- JSONL (JSON Lines) is better for streaming and line-by-line ingestion

#### Parquet ⭐
- Binary columnar format, the **industry standard for analytics**
- Excellent compression (typically 5-10x smaller than CSV)
- Selective column reading (projection pushdown)
- Native support in Spark, BigQuery, Redshift, Athena, DuckDB
- Ideal for Data Lakes and Lakehouses

#### Avro
- Row-oriented format with embedded schema
- Excellent for **streaming and ingestion** (Kafka uses Avro natively with Schema Registry)
- Supports schema evolution

#### ORC
- Similar to Parquet (columnar), more common in the legacy Hive/Hadoop ecosystem
- Preferred in some scenarios with Hive and Presto

#### Delta Lake / Iceberg / Hudi
- They are not just formats — they are **transactional table layers** on top of Parquet
- Add support for ACID, time travel, schema evolution, upserts, and deletes
- Fundamental for Lakehouse architectures
- Apache Iceberg is emerging as the dominant open standard


### 🗂️ NoSQL Databases

Designed for use cases where the relational model is not a good fit — high horizontal scalability, schema-less data, or very specific access patterns.

**Types and use cases:**

#### Document (Document Store)
Stores data as JSON/BSON documents. Flexible for data with variable structure.
- **Tools:** MongoDB, Firestore, CouchDB
- **Usage in DE:** data source for web/mobile applications, product catalogs

#### Key-Value
Ultra-fast access by key. Simple and extremely high-performance.
- **Tools:** Redis, DynamoDB, Memcached
- **Usage in DE:** result caching, user sessions, simple queues

#### Columnar (Wide-Column)
Optimized for writing and reading at scale with sparse data.
- **Tools:** Apache Cassandra, HBase, Google Bigtable
- **Usage in DE:** time series, IoT data, ultra-high-volume logs

#### Graph
Models entities and relationships as nodes and edges.
- **Tools:** Neo4j, Amazon Neptune
- **Usage in DE:** social networks, fraud detection, recommendation


### 📈 Time-Series Databases

Specialized in time-indexed data — metrics, telemetry, monitoring.

**Characteristics:** optimized compression for temporal data, native time-window functions, automatic downsampling.

**Tools:** InfluxDB, TimescaleDB (PostgreSQL extension), QuestDB, Amazon Timestream.


## Organization Strategies in Storage

### Partitioning
Organizing data into directories by common attributes (date, region, category) so queries read only the relevant partitions.

```text
s3://my-datalake/sales/
├── year=2024/
│   ├── month=01/
│   │   ├── day=01/
│   │   │   ├── part-00000.parquet
│   │   │   └── part-00001.parquet
│   │   └── day=02/
│   └── month=02/
└── year=2025/
```

**Partitioning best practices:**
- Partition by columns frequently used in filters (`WHERE`)
- Avoid **over-partitioning** (very small partitions create metadata overhead)
- Dates are the most common partitioning strategy in analytical data

### Small File Compaction
A classic Data Lake problem: too many small files (the "small files problem") degrade read performance. Small files should be periodically compacted into larger files. Formats like Delta Lake and Iceberg provide native `OPTIMIZE` / `COMPACTION` operations.

### Clustering / Z-Ordering
Technique that physically organizes data inside files by specific columns, improving the efficiency of data skipping (skipping irrelevant blocks during reads).


## Access Layers: Hot, Warm, and Cold

Access frequency should guide where and how data is stored:

| Layer | Access | Storage Cost | Retrieval Cost | Example |
|--------|--------|---------------|-------------------|---------|
| **Hot** | Frequent | High | Low | Last 30 days of data on SSD |
| **Warm** | Occasional | Medium | Medium | Last year of data on HDD/S3 Standard |
| **Cold** | Rare | Very low | High / Slow | Historical data in S3 Glacier, Coldline |

Lifecycle policies automate the movement between layers.


## Quick Comparison

| Type | Best for | Examples |
|------|-------------|----------|
| Relational (OLTP) | Operational transactions | PostgreSQL, MySQL |
| Data Warehouse (OLAP) | Analytics and BI | BigQuery, Snowflake |
| Object Storage | Raw data, Data Lake | S3, GCS, ADLS |
| Document | Flexible application data | MongoDB, Firestore |
| Key-Value | Cache and ultra-fast access | Redis, DynamoDB |
| Columnar NoSQL | IoT, logs, high-write workloads | Cassandra, Bigtable |
| Time Series | Metrics and telemetry | InfluxDB, TimescaleDB |
| Graph | Networks and relationships | Neo4j, Neptune |


## References

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- **Designing Data-Intensive Applications** — Martin Kleppmann (O'Reilly)
- [Apache Parquet Documentation](https://parquet.apache.org/docs/)
- [Apache Iceberg Documentation](https://iceberg.apache.org/docs/latest/)
- [Delta Lake Documentation](https://docs.delta.io/latest/index.html)


← [Streaming and Events](./6-streaming-and-events.md) · [Back to index](./0-data-engineering.md) · [Data Processing →](./8-data-processing.md)


*Documentation in progress · Personal portfolio*
