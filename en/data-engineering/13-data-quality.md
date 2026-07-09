# Data Quality

> *"A beautiful dashboard built on bad data is just a well-presented lie."*

← [Back to index](./0-data-engineering.md)


## What Is Data Quality?

Data quality is the degree to which data is **fit for its intended use**. High-quality data is accurate, complete, consistent, and reliable enough to support business decisions, feed ML models, and produce correct reports.

Quality is not a binary state (good or bad) — it is a multidimensional characteristic that must be continuously monitored and managed throughout the entire data lifecycle.

The cost of poor-quality data is high: wrong decisions, biased models, inconsistent reports, loss of trust in data teams, and in extreme cases, financial and regulatory damage.


## The 6 Dimensions of Quality

### 1. Completeness
Does the data contain all the required information? Are required fields filled in?

**Examples of problems:** `email` field null in 30% of records, orders without associated product, transactions without value.

**How to measure it:** percentage of records with non-null critical fields.


### 2. Uniqueness
Does each real-world entity appear **only once** in the data? Are there no improper duplicates?

**Examples of problems:** customer registered twice with different IDs, same order processed twice due to ingestion failure.

**How to measure it:** count of records with duplicated primary key; ratio between total records and unique records per business key.


### 3. Validity
Does the data follow the **expected format and domain rules**?

**Examples of problems:** date of birth in the future, incorrect ID format, discount value greater than order value, status with a value outside the expected set.

**How to measure it:** percentage of records that violate defined business rules.


### 4. Consistency
Is the data **coherent with itself** and across different systems?

**Examples of problems:** total sales in the CRM different from the ERP total for the same period, product marked as "active" in the catalog but with zero stock in the inventory system.

**How to measure it:** cross-checking metrics between different sources; referential integrity verification between tables.


### 5. Timeliness / Freshness
Is the data **up to date** according to what the use case requires?

**Examples of problems:** dashboard showing yesterday’s data when it should show today’s, ingestion pipeline delayed by 6 hours without detection.

**How to measure it:** time since the last update (`max(updated_at)` vs. current time); comparison with a defined SLA.


### 6. Accuracy
Does the data **correctly reflect the reality** it is intended to represent?

**Examples of problems:** customer address outdated after a move, wrong product price due to typo, file metadata with incorrect GPS coordinates.

**How to measure it:** manual sampling, reconciliation with source systems, cross-checking with trusted external sources.


## Quality Assurance Strategies

### Validation at Ingestion (Shift Left)
The most efficient approach is to detect problems **as early as possible** in the pipeline — ideally at ingestion, before bad data propagates downstream.

**Techniques:**
- Schema validation when data arrives
- Reject invalid records using a dead letter queue
- Immediate alerts when expected volume does not arrive

### Tests in Transformations (dbt Tests)
dbt provides native tests that run after each transformation:

```yaml
# schema.yml
models:
  - name: stg_orders
    columns:
      - name: order_id
        tests:
          - not_null
          - unique
      - name: status
        tests:
          - accepted_values:
              values: ['pending', 'completed', 'cancelled', 'refunded']
      - name: customer_id
        tests:
          - not_null
          - relationships:
              to: ref('stg_customers')
              field: customer_id
      - name: amount
        tests:
          - not_null
          - dbt_utils.expression_is_true:
              expression: ">= 0"
```

**Types of dbt tests:**
- `not_null`: column cannot contain null values
- `unique`: all values must be unique
- `accepted_values`: values must belong to a defined set
- `relationships`: referential integrity between tables


### Great Expectations
Open-source Python framework for defining, documenting, and validating **expectations** about data.

```python
import great_expectations as gx

context = gx.get_context()

# Define expectations
suite = context.add_expectation_suite("sales_suite")

validator = context.get_validator(
    batch_request=batch_request,
    expectation_suite_name="sales_suite"
)

validator.expect_column_values_to_not_be_null("order_id")
validator.expect_column_values_to_be_unique("order_id")
validator.expect_column_values_to_be_between("amount", min_value=0, max_value=1_000_000)
validator.expect_column_values_to_be_in_set("status", ["pending", "completed", "cancelled"])
validator.expect_table_row_count_to_be_between(min_value=1000, max_value=10_000_000)

# Save expectations
validator.save_expectation_suite()

# Validate and generate report
checkpoint = context.add_checkpoint(name="sales_checkpoint", ...)
results = checkpoint.run()
```


### Soda
Data quality platform with its own DSL (SodaCL) for defining checks in YAML, making it more accessible to non-Python teams.

```yaml
# soda_checks.yml
checks for orders:
  - row_count > 0
  - missing_count(order_id) = 0
  - duplicate_count(order_id) = 0
  - invalid_count(status) = 0:
      valid values: [pending, completed, cancelled]
  - avg(amount) between 50 and 500
  - freshness(created_at) < 25h
```


## Continuous Quality Monitoring

Point-in-time tests are not enough. Data that was correct yesterday may be wrong today. Continuous monitoring detects **anomalies and regressions over time**.

### Volume Monitoring
Detects when the number of records in a pipeline is outside the expected pattern.

```sql
-- Alert if today's volume is 20% below the average of the last 7 days
SELECT
    COUNT(*) AS volume_today,
    AVG(volume) OVER (ORDER BY date ROWS BETWEEN 7 PRECEDING AND 1 PRECEDING) AS avg_7d
FROM daily_volumes
WHERE ABS(volume_today - avg_7d) / avg_7d > 0.20
```

### Freshness Monitoring
Detects when the data has not been updated within the expected timeframe.

```sql
-- Alert if data has not been updated in the last 25 hours
SELECT
    MAX(updated_at) AS last_update,
    NOW() - MAX(updated_at) AS time_since_update
FROM fct_sales
HAVING NOW() - MAX(updated_at) > INTERVAL '25 hours'
```

### Statistical Anomaly Detection
Tools such as Monte Carlo and Bigeye automatically monitor distributions, null values, cardinality, and other indicators, alerting when there are statistically significant deviations without the need to define thresholds manually.


## Data Quality Tools

| Tool | Type | Highlight |
|------------|------|----------|
| **dbt tests** | Integrated with dbt | Declarative SQL tests in YAML |
| **Great Expectations** | Open source Python | Complete and flexible framework |
| **Soda** | Open source / Cloud | User-friendly DSL, easy adoption |
| **Monte Carlo** | Commercial | Automatic monitoring and ML-based detection |
| **Bigeye** | Commercial | Observability and automatic alerts |
| **Anomalo** | Commercial | Statistical anomaly detection |
| **Elementary** | Open source | Native observability for dbt |


## Data Quarantine

When data quality issues are detected, the data should not simply be discarded or propagated. **Quarantine** is a separate area where problematic data is isolated for investigation, correction, and possible reprocessing.

``` mermaid
flowchart LR

    ING["Ingestion"]
    VAL{"Validation"}

    BR["Bronze"]
    SI["Silver"]
    GO["Gold"]

    QUA["Quarantine Zone"]

    ALERT["Alert<br/>team"]
    FIX["Investigation<br/>& Correction"]

    ING --> VAL
    VAL -->|OK| BR --> SI --> GO

    VAL -->|FAIL| QUA
    QUA --> ALERT
    QUA --> FIX

    %% Styles
    classDef ingest fill:#17a2b8,stroke:#0f4c5c,color:#ffffff,stroke-width:2px;
    classDef decision fill:#9467bd,stroke:#5a3e85,color:#ffffff,stroke-width:2px;
    classDef bronze fill:#cd7f32,stroke:#8c4a1f,color:#ffffff,stroke-width:2px;
    classDef silver fill:#c0c0c0,stroke:#6e6e6e,color:#000000,stroke-width:2px;
    classDef gold fill:#ffd700,stroke:#b89600,color:#000000,stroke-width:2px;
    classDef quarantine fill:#d62728,stroke:#7f1d1d,color:#ffffff,stroke-width:2px;
    classDef action fill:#ff7f0e,stroke:#b45309,color:#ffffff,stroke-width:2px;

    class ING ingest;
    class VAL decision;
    class BR bronze;
    class SI silver;
    class GO gold;
    class QUA quarantine;
    class ALERT,FIX action;
```


## Data Quality Culture

Data quality is not the exclusive responsibility of the engineering team. It is a collaborative effort:

- **Data engineering:** implements technical controls and monitors pipelines
- **Business teams:** define what "correct data" means for each use case
- **Analysts:** report inconsistencies discovered during analysis
- **Source system owners:** ensure that source systems produce reliable data

**Data contracts** are an emerging pattern: formal agreements between data producers and consumers documenting schema, expected quality, and SLAs — functioning like an API contract, but for data.


## Best Practices

✅ Define tests before putting a pipeline into production, not after.

✅ Treat quality failures as pipeline failures — stop the flow and alert.

✅ Monitor trends, not just snapshots. An indicator that gradually worsens is just as dangerous as a sudden failure.

✅ Document quality rules alongside the data model documentation.

✅ Use percentage thresholds instead of absolute ones for volume tests (data grows over time).

✅ Separate critical tests (blocking) from warning tests (informational).


## References

- [Great Expectations Documentation](https://docs.greatexpectations.io/)
- [Soda Documentation](https://docs.soda.io/)
- [dbt Testing Guide](https://docs.getdbt.com/docs/build/data-tests)
- [Elementary Data (open source dbt observability)](https://www.elementary-data.com/)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)


← [DataOps and CI/CD](./12-dataops-and-cicd.md) · [Back to index](./0-data-engineering.md) · [Observability →](./14-observability.md)


*Documentation in progress · Personal portfolio*
