# Advanced Data Engineering Techniques

> *"A mature pipeline is not the one that runs once; it is the one that fails, reprocesses, scales, and stays trustworthy."*

← [Back to index](./0-data-engineering.md)


## Goal

This chapter brings together techniques used to make pipelines more efficient, reliable, auditable, and cost-effective in production. They appear across different tools, including Spark, dbt, Kafka, Flink, Airflow, Data Lakes, and cloud Data Warehouses.


## Partitioning

Partitioning organizes data into directories, files, or logical blocks to reduce unnecessary reads.

**Common Data Lake example:**

```text
s3://datalake/sales/silver/year=2026/month=07/day=07/
```

**Best practices:**
- Partition by columns frequently used in filters.
- Avoid partitions with very high cardinality, such as `user_id`.
- Avoid partitions that are too large or too small.
- Monitor pruning: the engine should read only the required partitions.

**Common trap:** partitioning by too many columns creates thousands of small files and degrades performance.


## Columnar Files and Compression

Columnar formats are standard for analytics because they read only the columns required by a query.

| Format | Recommended use |
|---|---|
| Parquet | Most common standard for Data Lakes and Lakehouses |
| ORC | Widely used in the Hive/Hadoop ecosystem |
| Avro | Good for events and evolving schemas |
| JSON/CSV | Useful for simple exchange, poor for analytics at scale |

**Common compression codecs:** Snappy, ZSTD, Gzip.

For analytics, Parquet with Snappy or ZSTD often provides a good balance between cost, speed, and compression.


## Small File Compaction

Too many small files make queries slower because the engine must open, list, and plan thousands of reads.

**Frequent causes:**
- Streaming jobs writing small micro-batches.
- Jobs with excessive partitioning.
- Incremental pipelines without maintenance.
- Parallel writes with too many output partitions.

**Techniques:**
- Periodic compaction jobs.
- `OPTIMIZE` in Delta Lake.
- Data file rewrite in Iceberg.
- Target file size tuning.
- Separation between fast ingestion and read-optimized layers.


## Clustering, Bucketing, and Z-Ordering

These techniques physically organize data to speed up filters and joins.

| Technique | Idea |
|---|---|
| Clustering | Groups data with similar values physically close together |
| Bucketing | Distributes records into buckets using a hash of a key |
| Z-Ordering | Reorganizes data to improve multidimensional filters |

**When to use:** large tables repeatedly filtered by columns such as `customer_id`, `product_id`, `date`, `region`, or `tenant_id`.


## Incremental Processing

Incremental processing avoids recalculating the full history on every run.

**Common strategies:**
- Filter by an update column, such as `updated_at`.
- Persist a watermark in a control table.
- Merge/upsert into the destination.
- Reprocess a moving window, such as the last 3 days.

**Example rule:**

```text
Process records where updated_at > last_successful_run
```

**Careful:** source systems may send late events. For this reason, many pipelines reprocess a recent window to correct delays.


## Idempotency

An idempotent pipeline can run more than once with the same input without creating duplicates or inconsistent state.

**Techniques:**
- Use natural keys or stable surrogate keys.
- Write to a temporary table and swap the partition at the end.
- Use `MERGE` instead of blind `INSERT`.
- Overwrite specific partitions.
- Record run metadata, input, output, and status.

Idempotency is essential for retries, backfills, and failure recovery.


## Backfill and Reprocessing

Backfill is the reprocessing of historical data, usually to fix bugs, recalculate metrics, or fill gaps.

**Best practices:**
- Parameterize pipelines by date or interval.
- Separate business logic from scheduling.
- Run backfills in controlled windows.
- Monitor cost and impact on the cluster/warehouse.
- Validate counts and metrics before publishing.

**Common risk:** reprocessing history with current rules without considering how rules changed over time.


## CDC and Upserts

CDC (Change Data Capture) captures inserts, updates, and deletes from source systems.

**Typical flow:**

```text
Transactional database → Debezium → Kafka → Lakehouse → silver/gold table
```

**Considerations:**
- Event ordering by key.
- Delete handling.
- Deduplication by key and timestamp.
- Schema evolution.
- Reprocessing from offsets.

Table formats such as Delta Lake, Iceberg, and Hudi help apply upserts and deletes in the Data Lake.


## SCD Type 1 and Type 2

Slowly Changing Dimensions are techniques for handling changes in analytical dimensions.

| Type | Behavior | Example |
|---|---|---|
| SCD Type 1 | Overwrites the previous value | Update the customer's current address |
| SCD Type 2 | Preserves history with validity periods | Keep address history by period |

**Common fields in SCD Type 2:**
- `valid_from`
- `valid_to`
- `is_current`
- `version`

Use Type 2 when historical analysis must reflect the context of that period.


## Watermarks and Late Events

In streaming, events can arrive out of order. A watermark defines how long the system waits for late events before closing a window.

**Example:**

```text
Calculate sales in 10-minute windows while accepting up to 5 minutes of delay.
```

**Trade-off:** the greater the lateness tolerance, the more state the system must keep and the higher the operational cost.


## Delivery Semantics

| Semantics | Meaning |
|---|---|
| At-most-once | May lose events, but does not duplicate |
| At-least-once | Does not lose events, but may duplicate |
| Exactly-once | Final result has no observable loss or duplication |

In practice, exactly-once depends on the combination of source, processing, sink, checkpoints, transactions, and idempotency.


## Data Contracts and Schema Evolution

Data contracts define expectations between data producers and consumers.

**A contract may include:**
- Schema and types.
- Required fields.
- Primary keys.
- Validity rules.
- Update frequency.
- Delivery SLA.
- Dataset owner.

Schema evolution allows controlled changes, such as adding columns without breaking consumers. Destructive changes, such as renaming or removing fields, require versioning and a migration plan.


## Spark Tuning

**Points of attention:**
- Avoid unnecessary shuffles.
- Use broadcast joins for small dimensions.
- Repartition data before large writes.
- Tune the number of partitions.
- Resolve data skew with salting or specific strategies.
- Avoid `collect()` on large datasets.
- Use cache only when the DataFrame will be reused.
- Inspect the physical plan with `explain()`.

**Problem signal:** a few executors work heavily while others stay idle. This usually indicates skew or poor partitioning.


## Operational Observability

Mature pipelines emit clear signals about execution and data.

**Useful metrics:**
- Job duration.
- Rows read and written.
- Size of processed data.
- Number of generated files.
- Dataset freshness.
- Error rate.
- Cost per run.

**Useful alerts:**
- Pipeline failed.
- Volume dropped or increased unexpectedly.
- Dataset is late.
- Schema changed.
- Quality dropped below the defined threshold.


## Cost Optimization

In the cloud, performance and cost move together.

**Techniques:**
- Avoid unnecessary full scans.
- Use partitioning and pruning.
- Compact small files.
- Choose columnar formats.
- Shut down idle clusters.
- Prefer autoscaling when appropriate.
- Separate exploratory and production workloads.
- Define TTL for temporary data.
- Monitor cost per pipeline, domain, and data product.


## Production Checklist

- Is the pipeline idempotent?
- Is there a retry strategy?
- Is there a backfill strategy?
- Are there schema and quality tests?
- Is the table partitioned correctly?
- Is the number of files healthy?
- Is cost per run known?
- Are owners and SLAs documented?
- Are schema changes controlled?
- Do failures generate actionable alerts?


## References

- [Apache Spark Tuning Guide](https://spark.apache.org/docs/latest/tuning.html)
- [Spark Structured Streaming Programming Guide](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
- [Delta Lake Optimization](https://docs.delta.io/latest/optimizations-oss.html)
- [Apache Iceberg Maintenance](https://iceberg.apache.org/docs/latest/maintenance/)
- [dbt Incremental Models](https://docs.getdbt.com/docs/build/incremental-models)
- [Debezium Documentation](https://debezium.io/documentation/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)


← [Cloud Comparison](./17-cloud-comparison.md) · [Back to index](./0-data-engineering.md) · [Semantic Layer →](./19-semantic-layer-and-analytics-engineering.md)


*Documentation in progress · Personal portfolio*
