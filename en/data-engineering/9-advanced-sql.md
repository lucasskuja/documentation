# Advanced SQL for Data Engineering

> *"SQL is the lingua franca of data; mastering advanced SQL means mastering a large part of analytical and operational data work."*

← [Back to index](./0-data-engineering.md)


## Why Advanced SQL Matters

SQL appears in Data Warehouses, Lakehouses, distributed engines, dbt, BI tools, ELT pipelines, and quality checks. Even when processing uses Spark or Python, SQL remains the most common way to express analytical transformations.

Advanced SQL helps with:

- Deduplicating data.
- Creating incremental models.
- Calculating metrics with windows.
- Investigating anomalies.
- Optimizing costs.
- Validating quality.
- Building marts and Gold layers.


## CTEs

CTEs (`WITH`) help break complex queries into readable steps.

```sql
WITH valid_sales AS (
    SELECT *
    FROM raw.sales
    WHERE status = 'completed'
),
sales_by_customer AS (
    SELECT
        customer_id,
        SUM(amount) AS total_revenue
    FROM valid_sales
    GROUP BY customer_id
)
SELECT *
FROM sales_by_customer
WHERE total_revenue > 1000;
```

**Best practices:**

- Name CTEs by the role they play.
- Avoid huge CTEs with too many responsibilities.
- Use CTEs to document transformation reasoning.
- Check whether the engine materializes or optimizes CTEs automatically.


## Window Functions

Window functions calculate values over a set of rows without collapsing the result like `GROUP BY`.

```sql
SELECT
    customer_id,
    order_id,
    order_date,
    amount,
    SUM(amount) OVER (
        PARTITION BY customer_id
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_revenue
FROM analytics.orders;
```

**Useful functions:**

| Function | Use |
|---|---|
| `ROW_NUMBER()` | Deduplication and deterministic ranking |
| `RANK()` / `DENSE_RANK()` | Ranking with ties |
| `LAG()` / `LEAD()` | Compare current row with previous/next row |
| `SUM() OVER` | Running totals |
| `AVG() OVER` | Moving averages |
| `FIRST_VALUE()` | First value in the window |
| `LAST_VALUE()` | Last value in the window |


## Deduplication

Deduplication requires a clear survival rule.

```sql
WITH ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY order_id
            ORDER BY updated_at DESC, ingestion_at DESC
        ) AS rn
    FROM raw.orders
)
SELECT *
FROM ranked
WHERE rn = 1;
```

**Important questions:**

- Which key defines duplication?
- Which record should win?
- Is the timestamp reliable?
- Are ties possible?
- Do deletes need to be considered?


## Advanced Joins

| Type | Use |
|---|---|
| `INNER JOIN` | Only records with matches |
| `LEFT JOIN` | Preserves all records from the left |
| `FULL OUTER JOIN` | Compares complete sets |
| `SEMI JOIN` | Returns records that have a match |
| `ANTI JOIN` | Returns records without a match |
| `CROSS JOIN` | Combines all with all; use carefully |

**Common anti-join:**

```sql
SELECT c.*
FROM dim_customer c
LEFT JOIN fact_sales s
    ON c.customer_id = s.customer_id
WHERE s.customer_id IS NULL;
```

This pattern finds customers without sales.


## Incremental Models

Incremental queries process only new or changed data.

```sql
SELECT *
FROM raw.events
WHERE updated_at >= (
    SELECT COALESCE(MAX(updated_at), TIMESTAMP '1900-01-01')
    FROM analytics.processed_events
);
```

**Considerations:**

- Use a safety window for late events.
- Combine incremental logic with `MERGE` when updates exist.
- Handle deletes when the source supports CDC.
- Monitor gaps between runs.


## MERGE and Upsert

`MERGE` combines insert, update, and delete in one logical operation.

```sql
MERGE INTO silver.customers AS target
USING staging.customers AS source
ON target.customer_id = source.customer_id
WHEN MATCHED THEN UPDATE SET
    name = source.name,
    email = source.email,
    updated_at = source.updated_at
WHEN NOT MATCHED THEN INSERT (
    customer_id, name, email, updated_at
) VALUES (
    source.customer_id, source.name, source.email, source.updated_at
);
```

**Use when:** existing records may change and the destination must reflect the latest state.


## Aggregations and Granularity

Every aggregation must respect data grain.

```sql
SELECT
    DATE_TRUNC('month', sale_date) AS month,
    product_id,
    SUM(total_amount) AS revenue
FROM fact_sales
GROUP BY 1, 2;
```

**Common mistake:** mixing metrics at different granularities, such as revenue per order and distinct customers per month, without making the aggregation level explicit.


## Performance

Good queries reduce data read, shuffles, and unnecessary joins.

**Techniques:**

- Filter early.
- Select only necessary columns.
- Use partitioning and clustering.
- Avoid functions on filter columns when they prevent pruning.
- Pre-aggregate before large joins.
- Avoid `SELECT *` on wide tables.
- Use materialized views when the engine supports them.
- Analyze the execution plan.


## EXPLAIN and Execution Plan

`EXPLAIN` shows how the engine plans to run the query.

**Look for:**

- Unexpected full scans.
- Very expensive joins.
- Filters applied late.
- Excessive shuffle/distribution.
- Lack of partition pruning.
- Row estimates very different from reality.


## Anti-patterns

- `SELECT *` in production pipelines.
- Date filters that do not use the partition column.
- Joins without a clear key.
- Deduplication without deterministic ordering.
- Giant CTEs that are hard to review.
- Recalculating full history unnecessarily.
- Duplicate metric definitions across many queries.
- Silent casting of incompatible types.
- Using `DISTINCT` to hide a modeling problem.


## Checklist

- Does the query make the grain clear?
- Do filters use partitions when possible?
- Do joins have expected cardinality?
- Is there a deterministic deduplication rule?
- Is scan cost acceptable?
- Can the query be incremental?
- Do metrics have centralized definitions?
- Was the execution plan analyzed?
- Does the query have quality tests?


## References

- [PostgreSQL Documentation - Queries](https://www.postgresql.org/docs/current/queries.html)
- [BigQuery Query Syntax](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax)
- [Snowflake SQL Reference](https://docs.snowflake.com/en/sql-reference)
- [Databricks SQL Language Reference](https://docs.databricks.com/sql/language-manual/)
- [dbt SQL Models](https://docs.getdbt.com/docs/build/sql-models)


← [Data Processing](./8-data-processing.md) · [Back to index](./0-data-engineering.md) · [Data Pipelines →](./10-data-pipelines.md)


*Documentation in progress · Personal portfolio*

