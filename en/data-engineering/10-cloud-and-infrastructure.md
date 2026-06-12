# Cloud and Infrastructure

> *"The cloud does not eliminate infrastructure complexity — it abstracts it so you can focus on what matters."*

← [Back to index](./0-data-engineering.md)


## Why Cloud Is Central to Data Engineering

Most modern data systems run on public cloud providers. Cloud adoption radically transformed data engineering:

- **Elastic scalability:** scales up and down on demand, without provisioning hardware in advance
- **Storage and compute separation:** store petabytes at low cost and process only when necessary
- **Managed services:** databases, orchestrators, processors — without managing the underlying infrastructure
- **Pay-as-you-go model:** pay only for what you use, with no large upfront investments (CapEx → OpEx)
- **Native high availability and resilience**, with SLAs of 99.9%+


## The Big Three Providers

### ☁️ Amazon Web Services (AWS)

The provider with the largest market share and the most mature ecosystem. A cloud pioneer, with an enormous variety of services.

**Main services for data:**

| Service | Category | Description |
|---------|-----------|-----------|
| **S3** | Object Storage | Data Lake, file storage |
| **Redshift** | Data Warehouse | OLAP, analytical queries |
| **Glue** | ETL / Catalog | Managed Spark jobs + Data Catalog |
| **Athena** | Query Engine | SQL over S3, serverless |
| **Kinesis** | Streaming | Real-time stream ingestion |
| **MSK** | Managed Kafka | Apache Kafka as a service |
| **EMR** | Hadoop / Spark | Managed Spark/Hadoop cluster |
| **Lake Formation** | Governance | Data Lake access control |
| **RDS / Aurora** | Relational database | Managed PostgreSQL, MySQL |
| **DynamoDB** | NoSQL | Key-value and document, serverless |
| **Step Functions** | Orchestration | Serverless workflows |
| **MWAA** | Managed Airflow | Apache Airflow as a service |


### ☁️ Google Cloud Platform (GCP)

Recognized for excellence in analytics and ML. BigQuery is widely considered the most powerful and easiest-to-operate Data Warehouse.

**Main services for data:**

| Service | Category | Description |
|---------|-----------|-----------|
| **Cloud Storage (GCS)** | Object Storage | Data Lake, S3 equivalent |
| **BigQuery** | Data Warehouse | Serverless, extreme scale, integrated ML |
| **Dataflow** | Processing | Managed Apache Beam (batch + streaming) |
| **Dataproc** | Hadoop / Spark | Managed Spark/Hadoop cluster |
| **Pub/Sub** | Messaging | Streaming messages |
| **Looker** | BI | BI tool with Semantic Layer |
| **Vertex AI** | ML Platform | Model training and deployment |
| **Cloud Composer** | Managed Airflow | Apache Airflow as a service |
| **Cloud Spanner** | NewSQL | Globally distributed relational database |
| **Bigtable** | Columnar NoSQL | Managed HBase, IoT and time series |
| **Dataplex** | Governance | Data Lake catalog and governance |


### ☁️ Microsoft Azure

Strong presence in enterprises with a Microsoft ecosystem. Native integration with Power BI and Office tools.

**Main services for data:**

| Service | Category | Description |
|---------|-----------|-----------|
| **ADLS Gen2** | Object Storage | Azure Data Lake Storage |
| **Azure Synapse** | Integrated analytics | DW + Spark + Pipeline on one platform |
| **Azure Data Factory** | ETL / Orchestration | Low-code data pipelines |
| **Event Hubs** | Streaming | Event ingestion (Kafka-compatible) |
| **HDInsight** | Hadoop / Spark | Managed cluster |
| **Cosmos DB** | NoSQL | Multi-model, globally distributed |
| **Azure SQL** | Relational database | Managed SQL Server |
| **Power BI** | BI | Microsoft BI tool |
| **Purview** | Governance | Catalog and lineage |
| **Databricks** | Lakehouse | Available on Azure, AWS, and GCP |


## Comparison by Use Case

| Need | AWS | GCP | Azure |
|-------------|-----|-----|-------|
| Data Warehouse | Redshift | **BigQuery** ⭐ | Synapse Analytics |
| Object Storage | **S3** ⭐ | GCS | ADLS Gen2 |
| Managed Kafka | MSK | — | Event Hubs |
| Managed Spark | EMR | Dataproc | HDInsight / Synapse |
| Managed Airflow | MWAA | Cloud Composer | — |
| Integrated BI | QuickSight | **Looker** ⭐ | **Power BI** ⭐ |
| ML Platform | SageMaker | **Vertex AI** ⭐ | Azure ML |
| Overall ecosystem | **Broadest** ⭐ | Analytics | Microsoft Enterprise |


## Cloud Service Models

### IaaS — Infrastructure as a Service
You rent infrastructure (VMs, storage, network) and manage the operating system, middleware, and applications. Maximum control, maximum operational responsibility.

**Examples:** EC2 (AWS), Compute Engine (GCP), Azure VMs.

**When to use in data:** self-managed Spark clusters, databases not available as a managed service.

### PaaS — Platform as a Service
The provider manages the underlying infrastructure; you manage only the application and data. Less control, less operational overhead.

**Examples:** Cloud Composer, MWAA, RDS, BigQuery.

**When to use:** most modern data services.

### SaaS — Software as a Service
Complete solution managed by the vendor. You simply use it.

**Examples:** Snowflake, Fivetran, dbt Cloud, Looker.

**When to use:** when the operational cost of self-hosting exceeds the cost of the managed solution.

### Serverless
You pay only for actual execution time, without managing servers or allocating capacity. Ideal for variable workloads.

**Examples:** BigQuery (queries), Athena, AWS Lambda, Cloud Functions.


## Infrastructure as Code (IaC)

IaC is the practice of **managing and provisioning infrastructure through code**, not manual processes. Just like application code is versioned and reviewed, infrastructure should be too.

**Benefits:**
- Reproducibility: recreate the environment identically in any cloud or region
- Versioning: history of infrastructure changes in Git
- Review: infrastructure goes through code review before being applied
- Automation: provision and destroy environments automatically (CI/CD)

### Terraform
The most popular IaC tool. Multi-cloud, with support for AWS, GCP, Azure, and hundreds of other providers.

```hcl
# Creates an S3 bucket for the Data Lake
resource "aws_s3_bucket" "datalake_bronze" {
  bucket = "my-company-datalake-bronze"

  tags = {
    Environment = "production"
    Layer       = "bronze"
    Owner       = "data-engineering"
  }
}

resource "aws_s3_bucket_versioning" "bronze_versioning" {
  bucket = aws_s3_bucket.datalake_bronze.id
  versioning_configuration {
    status = "Enabled"
  }
}

# Redshift cluster
resource "aws_redshift_cluster" "analytics" {
  cluster_identifier = "analytics-cluster"
  node_type          = "ra3.xlplus"
  number_of_nodes    = 2
  database_name      = "analytics"
  master_username    = var.redshift_user
  master_password    = var.redshift_password
}
```

### Other IaC Tools
- **AWS CDK / CloudFormation:** native AWS IaC
- **Pulumi:** IaC in general-purpose programming languages (Python, TypeScript)
- **Ansible:** server configuration automation


## Containers and Kubernetes

### Docker
Docker containers package applications and their dependencies into portable and reproducible units. Fundamental to ensure that a pipeline that works on a laptop works the same way in production.

```dockerfile
# Dockerfile for Python pipeline
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY src/ .

CMD ["python", "pipeline.py"]
```

**Usage in data:** packaging Spark jobs, ingestion scripts, Airflow workers, ML models.

### Kubernetes (K8s)
Container orchestrator for deploying, scaling, and managing containerized applications in clusters.

**Usage in data:**
- Running Airflow workers (KubernetesExecutor)
- Deploying streaming applications (Flink, Spark Streaming)
- Feature stores and ML model APIs
- Ephemeral data pipelines (one pod per execution)

**Managed services:** EKS (AWS), GKE (GCP), AKS (Azure).


## Network and Infrastructure Security

### VPC — Virtual Private Cloud
Private isolated network inside the cloud. All sensitive data resources should run inside a VPC, not on the public internet.

### Public and Private Subnets
- **Public subnet:** resources with internet access (load balancers, NAT gateways)
- **Private subnet:** resources without direct internet access (databases, processing clusters, sensitive data)

### VPC Peering / PrivateLink
Private connection between VPCs or between your VPC and cloud services, without traversing the public internet. Essential for secure data movement.

### IAM — Identity and Access Management
The cloud access control system. Defines which users, groups, and services can access which resources.

**Best practices:**
- Never use the root account for routine operations
- Create roles with minimum permissions for each service
- Enable MFA for all users with access to sensitive data
- Rotate credentials regularly


## Cloud Cost

Cost is one of the biggest concerns in cloud data architectures. Some optimization strategies:

**Storage/compute separation:** with BigQuery or Snowflake, you always pay for storage, but you only pay for compute when processing. Configure efficient queries to reduce scanned data.

**Spot / Preemptible instances:** interruptible instances cost 60-90% less. Ideal for batch jobs tolerant to interruption (Spark, EMR).

**Auto-scaling:** configure clusters to grow and shrink automatically with load, avoiding payment for idle capacity.

**Storage tiers:** move older data to cold tiers (S3 Glacier, GCS Coldline) automatically with lifecycle policies.

**Partitioning and clustering:** in BigQuery and Snowflake, well-partitioned and clustered tables drastically reduce the volume of data scanned per query — and therefore the cost.

**FinOps:** establish budgets, cost alerts, and periodic spending reviews. Consistent resource tagging allows costs to be assigned by team, product, or pipeline.


## Cloud Architecture Pattern for Data

```text
┌─────────────────────────────────────────────────────────────────┐
│                         Private VPC                             │
│                                                                 │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────┐    │
│  │   Ingestion  │   │  Processing  │   │  Orchestration   │    │
│  │              │   │              │   │                  │    │
│  │ Kafka (MSK)  │   │  Spark (EMR) │   │ Airflow (MWAA)   │    │
│  │ Fivetran     │   │  dbt Cloud   │   │                  │    │
│  └──────┬───────┘   └──────┬───────┘   └──────────────────┘    │
│         │                 │                                     │
│         ▼                 ▼                                     │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Object Storage (S3)                   │   │
│  │   Bronze/Raw    │   Silver/Refined    │   Gold/Curated   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                             │                                   │
│                             ▼                                   │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────┐    │
│  │ Data Warehouse│  │   Catalog    │   │   Monitoring     │    │
│  │ (Redshift /  │  │  (DataHub)   │   │ (CloudWatch +    │    │
│  │  BigQuery)   │  │              │   │   Grafana)       │    │
│  └──────┬────────┘  └──────────────┘   └──────────────────┘    │
│         │                                                       │
└─────────┼───────────────────────────────────────────────────────┘
          │
          ▼
    ┌───────────┐
    │ BI / APIs │
    │ Looker    │
    │ Power BI  │
    └───────────┘
```


## Infrastructure Best Practices

✅ **Everything as code:** all infrastructure provisioned through Terraform or equivalent, versioned in Git.

✅ **Separated environments:** development, staging, and production with complete isolation of data and credentials.

✅ **Principle of least privilege:** each service and user with only the minimum required permissions.

✅ **Cost monitoring:** budget alerts and periodic spending reviews.

✅ **Backup and disaster recovery:** define RPO (Recovery Point Objective) and RTO (Recovery Time Objective) for critical data.

✅ **Encryption in transit and at rest:** standard for all data, especially sensitive data.

✅ **Tags on all resources:** helps with cost management, ownership, and automation.


## References

- [AWS Data Analytics Learning Path](https://aws.amazon.com/training/learn-about/analytics/)
- [Google Cloud Data Engineering](https://cloud.google.com/training/data-engineering-and-analytics)
- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- [FinOps Foundation](https://www.finops.org/)


← [Governance and Security](./9-governance-and-security.md) · [Back to index](./0-data-engineering.md) · [Cloud Comparison for Data Engineering →](./11-cloud-comparison.md)


*Documentation in progress · Personal portfolio*
