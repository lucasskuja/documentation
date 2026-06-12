# Data Processing

> *"Raw data is just raw material. Processing is what turns ore into steel."*

← [Back to index](./0-data-engineering.md)


## What Is Data Processing?

Data processing is the set of operations that **transform raw data into useful and trustworthy information**. This is the stage where data is cleaned, enriched, aggregated, modeled, and prepared for consumption — whether by analysts, dashboards, ML models, or applications.

Processing answers questions such as:
- How do we remove duplicate or invalid records?
- How do we combine data from multiple sources?
- How do we calculate metrics and aggregations?
- How do we ensure that data is in the correct format for the destination?


## ETL vs ELT

Two paradigms dominate how processing fits into the data flow:

### ETL — Extract, Transform, Load
The traditional model: data is **transformed before being loaded** into the destination. Transformation happens on an intermediate system or dedicated server.

``` mermaid
flowchart LR

    SRC["Source"]
    EXT["Extraction"]
    TRF["Transformation"]
    LOAD["Load"]
    DW["Data Warehouse"]

    SRC --> EXT --> TRF --> LOAD --> DW

    %% Styles
    classDef source fill:#6c757d,stroke:#343a40,color:#ffffff,stroke-width:2px;
    classDef extract fill:#17a2b8,stroke:#0f4c5c,color:#ffffff,stroke-width:2px;
    classDef transform fill:#9467bd,stroke:#5a3e85,color:#ffffff,stroke-width:2px;
    classDef load fill:#ff7f0e,stroke:#b45309,color:#ffffff,stroke-width:2px;
    classDef warehouse fill:#1f77b4,stroke:#0d3b66,color:#ffffff,stroke-width:2px;

    class SRC source;
    class EXT extract;
    class TRF transform;
    class LOAD load;
    class DW warehouse;
```

**Advantages:** full control over transformation, data arrives clean at the destination.

**Disadvantages:** more rigid pipeline, harder to re-run, less flexible for exploration.

**When it was common:** on-premise environments with expensive Data Warehouses where loading raw data was not feasible.


### ELT — Extract, Load, Transform
The modern model: data is loaded **first into the destination in its raw form**, and transformation happens inside the Data Warehouse or Data Lake itself, taking advantage of its processing power.

``` mermaid
flowchart LR

    SRC["Source"]
    EXT["Extraction"]
    LOAD["Load"]
    DW["Data Warehouse"]
    TRF["Transformation"]

    SRC --> EXT --> LOAD --> DW --> TRF

    %% Styles
    classDef source fill:#6c757d,stroke:#343a40,color:#ffffff,stroke-width:2px;
    classDef extract fill:#17a2b8,stroke:#0f4c5c,color:#ffffff,stroke-width:2px;
    classDef load fill:#ff7f0e,stroke:#b45309,color:#ffffff,stroke-width:2px;
    classDef warehouse fill:#1f77b4,stroke:#0d3b66,color:#ffffff,stroke-width:2px;
    classDef transform fill:#9467bd,stroke:#5a3e85,color:#ffffff,stroke-width:2px;

    class SRC source;
    class EXT extract;
    class LOAD load;
    class DW warehouse;
    class TRF transform;
```

**Advantages:** greater flexibility, raw data is preserved, transformations can be versioned and re-run easily, and it takes advantage of cloud scalability.

**Disadvantages:** requires a destination with enough computational power, and raw data remains exposed in the environment.

**Why ELT dominates today:** BigQuery, Snowflake, and Redshift have elastic and affordable compute. It no longer makes sense to transform before loading when the destination is more powerful than any intermediate server.

## Types of Transformations

### Data Cleansing
- Removing duplicates
- Handling null values (fill, discard, flag)
- Standardizing formats (dates, strings, phone numbers, IDs)
- Removing special characters and incorrect encoding
- Validating data types

### Enrichment
- Join with reference tables (for example: add product name from product ID)
- External data lookup (for example: geolocation coordinates from postal code)
- Calculation of derived fields (for example: age from birth date)

### Aggregation
- Summarizations (SUM, COUNT, AVG, MIN, MAX)
- Time windows (sales by month, weekly active users)
- Business metrics (LTV, churn rate, conversion rate)

### Filtering and Projection
- Removing records irrelevant to the use case
- Selecting only the required columns

### Joins and Combinations
- Combining data from multiple sources into a unified view
- Detecting matches between records from different systems

### Pivoting and Unpivoting
- Transforming rows into columns (pivot) or columns into rows (unpivot)
- Normalizing nested structures (exploding arrays in JSON)


## Batch vs Streaming Processing

### Batch Processing
Processes a **fixed set of data** all at once. The query or job has a clear beginning and end.

**Characteristics:**
- Efficiently processes large volumes
- Higher latency (results become available only when the job completes)
- Simpler to implement and debug
- Easier re-execution and backfill

**Use cases:** nightly transformations, daily metric calculation, report generation, ML model training.

### Streaming Processing
Processes data **continuously**, as it arrives, event by event or in micro-batches.

**Characteristics:**
- Low latency (results in seconds or less)
- Greater complexity: state management, watermarks, exactly-once
- Requires dedicated always-on infrastructure
- Useful where delay has real business cost

**Use cases:** fraud detection, real-time alerts, live dashboard updates, immediate personalization.


## Distributed Processing Concepts

For large data volumes, processing on a single machine is not viable. Distributed processing splits the work across multiple machines in parallel.

### Data Partitioning
Data is divided into partitions, each processed by a different cluster node. The choice of partitioning key directly impacts performance.

### Shuffle
An operation that redistributes data across cluster nodes, necessary for joins and aggregations that need to bring related data together. It is the most expensive operation in terms of I/O and network — it should be minimized.

### Map and Reduce
Classic distributed processing paradigm:
- **Map:** applies a transformation to each record in parallel
- **Reduce:** aggregates the results from all nodes into a final result

### Data Skew
Imbalance in data distribution across partitions. If one partition has much more data than the others, one node becomes overloaded while the rest remain idle, degrading performance.


## Processing Tools

### 🔥 Apache Spark
The most popular distributed processing engine. Supports batch, streaming (Structured Streaming), SQL, ML (MLlib), and graphs (GraphX) in a single unified API.

**Characteristics:**
- In-memory processing (much faster than MapReduce)
- APIs in Python (PySpark), Scala, Java, and R
- Native integration with Delta Lake, Iceberg, S3, HDFS, Kafka
- Available on managed platforms: Databricks, EMR, Dataproc

**When to use:** processing large volumes, complex transformations, ML pipelines, and environments that already use the Spark ecosystem.

```python
# PySpark example
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, sum, to_date

spark = SparkSession.builder.appName("sales").getOrCreate()

df = spark.read.parquet("s3://datalake/sales/raw/")

result = (
    df
    .filter(col("status") == "completed")
    .withColumn("date", to_date(col("created_at")))
    .groupBy("date", "product_id")
    .agg(sum("amount").alias("total_revenue"))
)

result.write.parquet("s3://datalake/sales/gold/revenue_per_day/")
```


### 🔧 dbt (data build tool)
SQL-first transformation tool that brings software engineering practices into the world of data transformations: versioning, testing, documentation, and modularity.

**Characteristics:**
- Transformations in pure SQL (with Jinja for templating)
- Models as `.sql` files versioned in Git
- Native tests (not null, unique, accepted values, relationships)
- Automatic generation of documentation and lineage
- Runs inside the Data Warehouse (BigQuery, Snowflake, Redshift, DuckDB)

**When to use:** analytical transformations inside the Data Warehouse (ELT), teams that prefer SQL over Python, projects that require documentation and transformation governance.

```sql
-- models/silver/clean_sales.sql
{{ config(materialized='incremental', unique_key='sale_id') }}

SELECT
    sale_id,
    customer_id,
    product_id,
    CAST(amount AS NUMERIC) AS amount,
    DATE(created_at) AS sale_date
FROM {{ source('raw', 'sales') }}
WHERE status = 'completed'
  AND amount > 0
```


### 🐟 Apache Flink
High-performance stateful stream processing engine. The reference choice for complex streaming use cases.

**When to use:** streaming with complex state, real-time event processing, use cases where latency matters a lot.


### 🦆 DuckDB
Embedded OLAP database (serverless). Processes Parquet, CSV, and JSON files directly with high-performance SQL, running locally or in notebooks.

**When to use:** local analytics, prototyping, lightweight pipelines without infrastructure, or as an alternative to Pandas for medium-sized datasets.

```python
import duckdb

result = duckdb.sql("""
    SELECT
        DATE_TRUNC('month', sale_date) AS month,
        SUM(amount) AS revenue
    FROM read_parquet('s3://datalake/sales/*.parquet')
    GROUP BY 1
    ORDER BY 1
""").df()
```


### 🐼 Pandas
Python library for in-memory data manipulation. Widely used for small to medium datasets, prototyping, and local pipelines.

**Limitation:** it does not scale for large volumes (everything stays in one machine’s memory). For bigger volumes, consider **Polars** (faster and more modern alternative) or **Dask** (distributed Pandas).


### Trino / Presto
Distributed SQL query engine that allows querying data across multiple heterogeneous sources (S3, relational databases, NoSQL) with a single SQL query. Ideal for data federation.


## Processing Best Practices

**Idempotency:** jobs must be re-runnable without creating duplicates or inconsistent states.

**Incrementality:** process only new data whenever possible, avoiding full reprocessing.

**Testing:** validate transformation outputs with automated tests (counts, expected ranges, referential integrity).

**Logging and observability:** record execution time, processed volume, and errors to make debugging easier.

**Separation of responsibilities:** keep transformations simple and composed of smaller, reusable modules.

**Avoid duplicated business logic:** centralize metric definitions to avoid inconsistencies across pipelines.


## References

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [dbt Documentation](https://docs.getdbt.com/)
- [DuckDB Documentation](https://duckdb.org/docs/)
- [Apache Flink Documentation](https://nightlies.apache.org/flink/flink-docs-stable/)


← [Data Storage](./3-data-storage.md) · [Back to index](./0-data-engineering.md) · [Data Pipelines →](./5-data-pipelines.md)


*Documentation in progress · Personal portfolio*
