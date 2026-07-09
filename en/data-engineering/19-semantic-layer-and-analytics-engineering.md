# Semantic Layer and Analytics Engineering

> *"The semantic layer turns tables into business language: shared metrics, dimensions, and rules."*

← [Back to index](./0-data-engineering.md)


## What Is Analytics Engineering?

Analytics Engineering is the practice of applying software engineering to analytical models, metrics, and marts consumed by BI, analytics, product, and data science.

It sits between data engineering and data analysis:

- Data engineering ensures reliable ingestion, storage, and processing.
- Analytics Engineering models data for analytical consumption.
- BI and analytics use these models for business decisions.


## What Is a Semantic Layer?

A semantic layer centrally defines business concepts such as metrics, dimensions, entities, and calculation rules.

**Without a semantic layer:**

- Each dashboard calculates revenue differently.
- Teams use different filters for the same KPI.
- Metrics change without traceability.
- Nobody knows which number is official.

**With a semantic layer:**

- Metrics have one definition.
- Dimensions are standardized.
- Owners are clear.
- Different tools consume the same logic.


## Metric, Dimension, and Entity

| Concept | Definition | Example |
|---|---|---|
| Metric | Aggregated measurable value | Revenue, churn, orders |
| Dimension | Attribute used to slice metrics | Region, product, channel |
| Entity | Business object | Customer, account, order |
| Grain | Level of detail | One row per order |
| Filter | Rule applied to calculation | `status = 'completed'` |


## Certified Metric

A certified metric is an official, documented, and validated metric.

**It should have:**

- Business name.
- Definition.
- Formula.
- Grain.
- Filters.
- Source.
- Owner.
- SLA.
- Usage examples.
- Known limitations.

**Example:**

```yaml
metric: net_revenue
description: Total revenue from completed orders minus refunds.
owner: finance_analytics
grain: order
formula: sum(paid_amount - refunded_amount)
filters:
  - status = 'completed'
dimensions:
  - order_date
  - channel
  - country
```


## Data Marts

Data marts are consumption-oriented models, usually organized by domain or use case.

| Mart | Consumers | Examples |
|---|---|---|
| Finance | Finance, executives | Revenue, margin, delinquency |
| Marketing | Growth, CRM | CAC, campaigns, conversion |
| Product | Product, UX | Adoption, retention, funnel |
| Operations | Logistics, support | SLA, tickets, deliveries |

Marts should be easy to query, well documented, and aligned with business vocabulary.


## dbt and Analytics Engineering

dbt popularized Analytics Engineering by bringing engineering practices to SQL:

- Versioned models.
- Tests.
- Documentation.
- Lineage.
- Macros.
- Materializations.
- Staging, intermediate, and marts layers.

**Common organization:**

```text
models/
|-- staging/
|-- intermediate/
`-- marts/
    |-- finance/
    |-- marketing/
    `-- product/
```


## Semantic Layer Tools

| Tool | Role |
|---|---|
| dbt Semantic Layer / MetricFlow | Metrics defined as code |
| LookML | Looker's semantic model |
| Cube | Semantic layer and APIs for metrics |
| Power BI semantic model | Semantic model for Power BI reports |
| Tableau data model | Relationships, sources, and calculations for Tableau |

The goal is not to choose the most popular tool, but to ensure consistency and governance of definitions.


## Metrics vs Gold Tables

| Item | Role |
|---|---|
| Gold table | Modeled data ready for consumption |
| Metric | Business calculation over modeled data |
| Dashboard | Visualization of metrics and dimensions |
| Semantic layer | Place where the metric is defined and governed |

A Gold table can feed many metrics. A metric can be consumed by many dashboards.


## Metric Contracts

Just like datasets, metrics need contracts.

**A contract should define:**

- Formula.
- Source.
- Grain.
- Refresh frequency.
- Lateness tolerance.
- Allowed dimensions.
- Access rules.
- Technical owner and business owner.
- Change process.


## Metric Governance

**Best practices:**

- Define owners by domain.
- Certify official metrics.
- Version relevant changes.
- Deprecate old metrics with a deadline.
- Document differences between similar metrics.
- Monitor freshness and quality of base tables.
- Avoid multiple definitions for the same KPI.


## Anti-patterns

- KPI calculated directly in the dashboard without documentation.
- Each team maintains its own version of the same metric.
- Gold table treated as official metric without definition.
- Metrics without owner.
- Silent formula changes.
- Dimensions with different names for the same concept.
- Uncertified data used in executive presentations.


## Checklist

- Does the metric have a clear definition?
- Is the grain explicit?
- Is the formula versioned?
- Is there a technical and business owner?
- Are allowed dimensions documented?
- Does the metric have tests?
- Is the data source traceable?
- Is there a refresh SLA?
- Do dashboards use the certified metric?
- Do changes go through review?


## References

- [dbt Semantic Layer](https://docs.getdbt.com/docs/use-dbt-semantic-layer/dbt-sl)
- [MetricFlow Documentation](https://docs.getdbt.com/docs/build/about-metricflow)
- [Looker LookML](https://cloud.google.com/looker/docs/lookml-reference)
- [Cube Documentation](https://cube.dev/docs)
- [Microsoft Power BI Semantic Models](https://learn.microsoft.com/en-us/power-bi/connect-data/service-datasets-understand)


← [Advanced Techniques](./18-advanced-techniques.md) · [Back to index](./0-data-engineering.md)


*Documentation in progress · Personal portfolio*

