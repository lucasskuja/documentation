# Cloud Comparison for Data Engineering

> *"The best cloud is the one that solves your problem — not the one that is currently fashionable."*

← [Back to index](./0-data-engineering.md)


## Provider Overview

```mermaid
mindmap
  root((Cloud for<br/>Data))
    AWS
      S3
      Redshift
      Glue
      Athena
      Kinesis
      MSK
      EMR
      MWAA
    GCP
      BigQuery
      GCS
      Dataflow
      Dataproc
      Pub/Sub
      Composer
      Vertex AI
    Azure
      ADLS Gen2
      Synapse
      Data Factory
      Event Hubs
      HDInsight
      Databricks
      Power BI
```


## Market Share and Positioning

```mermaid
quadrantChart
    title Cloud Positioning in Data
    x-axis Ease of Use --> Higher Complexity
    y-axis Smaller Ecosystem --> Larger Ecosystem
    quadrant-1 "Powerful and Complex"
    quadrant-2 "Powerful and Accessible"
    quadrant-3 "Simple but Limited"
    quadrant-4 "Complex and Niche"
    AWS: [0.75, 0.95]
    GCP: [0.35, 0.75]
    Azure: [0.60, 0.80]
    Snowflake: [0.20, 0.55]
    Databricks: [0.55, 0.60]
```

| Criterion | AWS | GCP | Azure |
|----------|-----|-----|-------|
| **Overall cloud market share** | ~33% | ~11% | ~22% |
| **Strength in data** | Full ecosystem | Analytics and ML | Enterprise and Microsoft |
| **Best data service** | S3 + Glue | BigQuery | Synapse + Power BI |
| **Learning curve** | High | Medium | Medium-High |
| **Microsoft integration** | Low | Low | Native |
| **Maturity** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |


## Service Comparison by Category

### 🗄️ Object Storage (Data Lake)

```mermaid
graph LR
    subgraph AWS
        S3["🪣 Amazon S3<br/>99.999999999%<br/>durability<br/>lifecycle policies<br/>S3 Intelligent-Tiering"]
    end
    subgraph GCP
        GCS["🪣 Cloud Storage<br/>99.999999999%<br/>durability<br/>Multi-regional<br/>Autoclass"]
    end
    subgraph Azure
        ADLS["🪣 ADLS Gen2<br/>Hierarchical<br/>Namespace<br/>Granular ACLs<br/>Integrated with AD"]
    end

    S3 -->|"Winner in<br/>ecosystem"| Winner["⭐ Industry Reference"]
    GCS -->|"Best<br/>integration"| Winner
    ADLS -->|"Best for<br/>enterprise AD"| Winner
```

| Criterion | Amazon S3 | Google Cloud Storage | Azure Data Lake Gen2 |
|----------|-----------|----------------------|----------------------|
| **Durability** | 99.999999999% | 99.999999999% | 99.999999999% |
| **Availability** | 99.99% | 99.99% | 99.9% |
| **Storage cost (GB/month)** | ~$0.023 | ~$0.020 | ~$0.018 |
| **Storage tiers** | Standard, IA, Glacier, Deep Archive | Standard, Nearline, Coldline, Archive | Hot, Cool, Archive |
| **Access control** | IAM + Bucket Policies | IAM + ACLs | RBAC + hierarchical ACLs |
| **Versioning** | ✅ | ✅ | ✅ |
| **Event notifications** | SNS, SQS, Lambda | Pub/Sub, Cloud Functions | Event Grid, Functions |
| **Hierarchical namespace** | Emulated (prefixes) | Emulated (prefixes) | ✅ Native |
| **AD integration** | ❌ | ❌ | ✅ Azure AD |
| **Best for** | AWS ecosystem, reference | Cost-benefit, native GCP | Enterprise with AD |


### 📊 Data Warehouse (OLAP)

```mermaid
graph TD
    subgraph Redshift["Amazon Redshift"]
        R1["Columnar Storage (RA3)"]
        R2["Redshift Spectrum (S3 queries)"]
        R3["Aqua (accelerated cache)"]
        R4["Concurrency Scaling"]
    end

    subgraph BigQuery["Google BigQuery"]
        B1["Serverless — no cluster"]
        B2["Full storage/compute separation"]
        B3["BQML — ML inside SQL"]
        B4["BI Engine (in-memory cache)"]
        B5["Omni — multi-cloud queries"]
    end

    subgraph Synapse["Azure Synapse"]
        S1["Dedicated SQL Pool"]
        S2["Serverless SQL Pool"]
        S3["Apache Spark Pool"]
        S4["Data Explorer Pool"]
    end
```

| Criterion | Amazon Redshift | Google BigQuery | Azure Synapse |
|----------|-----------------|-----------------|---------------|
| **Billing model** | Per cluster hour | Per TB scanned / slot | Per DWU or serverless |
| **Serverless** | ✅ Redshift Serverless | ✅ Native | ✅ Serverless SQL Pool |
| **Storage/compute separation** | ✅ RA3 nodes | ✅ Full | Partial |
| **Integrated ML support** | ❌ | ✅ BQML | ✅ via Synapse ML |
| **Out-of-the-box performance** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Ease of operation** | Medium | High | Medium |
| **Semi-structured data support** | Partial (SUPER type) | ✅ Native JSON | ✅ |
| **Concurrency** | Concurrency Scaling | Automatic | Workload Management |
| **Native BI integration** | QuickSight | Looker | Power BI |
| **Best for** | Heavy AWS workloads | Maximum-scale analytics | Microsoft enterprise |


### 🔄 Managed ETL / Ingestion

```mermaid
flowchart LR
    Sources["🗃️ Sources<br/>CRM · ERP<br/>DBs · APIs"]

    subgraph AWS_ETL["AWS"]
        Glue["AWS Glue<br/>(serverless Spark)"]
        DMS["AWS DMS<br/>(migration and CDC)"]
        AthenaQ["Athena<br/>(query over S3)"]
    end

    subgraph GCP_ETL["GCP"]
        Dataflow["Dataflow<br/>(Apache Beam)"]
        DataFusion["Cloud Data Fusion<br/>(low-code ETL)"]
        Composer["Cloud Composer<br/>(managed Airflow)"]
    end

    subgraph Azure_ETL["Azure"]
        ADF["Azure Data Factory<br/>(low-code pipelines)"]
        ADLA["Data Lake Analytics"]
        Databricks_A["Databricks<br/>(Spark)"]
    end

    Sources --> Glue
    Sources --> Dataflow
    Sources --> ADF
```

| Criterion | AWS Glue | GCP Dataflow | Azure Data Factory |
|----------|----------|--------------|--------------------|
| **Paradigm** | Serverless Spark | Apache Beam | Low-code + code |
| **Language** | Python, Scala | Python, Java | JSON + Python |
| **Learning curve** | Medium | High | Low |
| **Batch** | ✅ | ✅ | ✅ |
| **Streaming** | Limited | ✅ Native | ✅ via Event Hubs |
| **Integrated catalog** | ✅ Glue Data Catalog | ✅ Dataplex | ✅ Purview |
| **Native connector for other clouds** | Limited | Limited | ✅ via ADF |
| **Cost** | Per DPU-hour | Per vCPU-hour | Per activity |
| **Best for** | Spark workloads on AWS | Complex streaming | Enterprise integration |


### ⚡ Streaming and Messaging

```mermaid
graph TB
    subgraph Producers["📤 Producers"]
        App["Applications"]
        IoT["IoT Devices"]
        CDC["CDC / Logs"]
    end

    subgraph AWS_S["AWS"]
        Kinesis["Kinesis Data Streams<br/>(managed streams)"]
        KF["Kinesis Firehose<br/>(delivery to S3/Redshift)"]
        MSK["Amazon MSK<br/>(managed Kafka)"]
        SQS["Amazon SQS<br/>(message queue)"]
    end

    subgraph GCP_S["GCP"]
        PubSub["Cloud Pub/Sub<br/>(global messaging)"]
        DF2["Dataflow<br/>(stream processing)"]
    end

    subgraph Azure_S["Azure"]
        EH["Event Hubs<br/>(Kafka-compatible)"]
        SB["Service Bus<br/>(enterprise queue)"]
        ASA["Stream Analytics<br/>(SQL processing)"]
    end

    App --> Kinesis
    App --> PubSub
    App --> EH
    IoT --> MSK
    IoT --> EH
    CDC --> MSK
```

| Criterion | AWS Kinesis | AWS MSK (Kafka) | GCP Pub/Sub | Azure Event Hubs |
|----------|-------------|-----------------|-------------|-----------------|
| **Kafka compatibility** | ❌ | ✅ Full | ❌ | ✅ Partial |
| **Message retention** | 7-365 days | Configurable | 7 days (max 31) | 1-7 days |
| **Message replay** | ✅ | ✅ | Limited | ✅ |
| **Auto-scaling** | ✅ | Manual (brokers) | ✅ Automatic | ✅ Auto-inflate |
| **Operational overhead** | Low | Medium | Low | Low |
| **Cost** | Per shard/hour | Per broker/hour | Per GB | Per TU/hour |
| **Latency** | ~200ms | < 10ms | ~100ms | ~100ms |
| **Best for** | Simple AWS integration | Kafka-native apps | GCP serverless apps | Azure apps / Kafka compatibility |


### 🔥 Distributed Processing (Spark)

```mermaid
graph LR
    subgraph Managed_Spark["Cloud-Managed Spark"]
        EMR["Amazon EMR<br/>• More configurable<br/>• Hadoop support<br/>• Spot instances<br/>• EC2 or EKS"]
        Dataproc["GCP Dataproc<br/>• Setup in 90s<br/>• BigQuery integration<br/>• Preemptible VMs<br/>• Optional GKE"]
        HDInsight["Azure HDInsight<br/>• Legacy Hadoop<br/>• Integrated with ADLS<br/>• Declining adoption"]
        ADB["Azure Databricks<br/>• Better UX<br/>• Native Delta Lake<br/>• Unity Catalog<br/>• Multi-cloud"]
    end

    Databricks["🏆 Databricks<br/>(available on all 3 clouds)"] --> EMR
    Databricks --> Dataproc
    Databricks --> ADB
```

| Criterion | Amazon EMR | GCP Dataproc | Azure HDInsight | Databricks (all clouds) |
|----------|------------|--------------|-----------------|--------------------------|
| **Cluster startup time** | 5-10 min | ~90 seconds | 15-20 min | 2-5 min |
| **Ease of use** | Medium | High | Low | High |
| **Native Delta Lake** | Configurable | Configurable | Configurable | ✅ Full |
| **Integrated notebook** | EMR Studio | Dataproc Hub | Basic | ✅ Databricks Notebooks |
| **Auto-scaling** | ✅ | ✅ | Limited | ✅ |
| **Spot/preemptible instances** | ✅ | ✅ | ✅ | ✅ |
| **Serverless Spark** | ✅ EMR Serverless | ✅ Dataproc Serverless | ❌ | ✅ |
| **Multi-cloud** | ❌ | ❌ | ❌ | ✅ |
| **Best for** | AWS flexibility | Speed + GCP | Legacy | Modern Lakehouse |


### 🎼 Orchestration

```mermaid
graph TD
    subgraph Cloud_Orchestration["Managed Orchestration"]
        MWAA["Amazon MWAA<br/>Airflow on AWS<br/>Integrated with IAM/VPC<br/>Complex setup"]
        Composer["Cloud Composer<br/>Airflow on GCP<br/>Easier to configure<br/>Based on GKE"]
        ADF2["Azure Data Factory<br/>Low-code visual<br/>Not Airflow<br/>Native Azure connectors"]
    end

    subgraph SaaS_Orchestration["SaaS Orchestration (multi-cloud)"]
        Astronomer["Astronomer<br/>(Airflow cloud)"]
        Prefect["Prefect Cloud"]
        Dagster["Dagster Cloud"]
    end

    MWAA --> Airflow["Apache Airflow"]
    Composer --> Airflow
    ADF2 --> LowCode["Low-Code<br/>Paradigm"]
```

| Criterion | Amazon MWAA | GCP Cloud Composer | Azure Data Factory | Astronomer |
|----------|-----------|--------------------|-------------------|------------|
| **Based on Airflow** | ✅ | ✅ | ❌ (proprietary) | ✅ |
| **Ease of setup** | Low | Medium | High | High |
| **Base cost** | ~$0.49/hour | ~$0.37/hour | Per execution | Per task |
| **Scalability** | Auto | Auto (GKE) | Automatic | Auto |
| **Support for DAGs as code** | ✅ | ✅ | Partial (JSON) | ✅ |
| **Native cloud integration** | ✅ AWS | ✅ GCP | ✅ Azure | Multi-cloud |
| **Version upgrades** | Manual | Manual | Automatic | Automatic |
| **Best for** | Airflow on AWS | Airflow on GCP | Visual Azure integration | Multi-cloud Airflow |


## Architecture Comparison by Cloud

### Data Lake Architecture

```mermaid
graph TD
    subgraph AWS_Lake["🟠 AWS — Data Lake"]
        A_src["Sources"] --> A_ing["Kinesis / DMS / Glue"]
        A_ing --> A_s3["S3 — Bronze/Silver/Gold"]
        A_s3 --> A_glue["Glue Catalog"]
        A_s3 --> A_athena["Athena — ad hoc queries"]
        A_s3 --> A_emr["EMR — processing"]
        A_emr --> A_red["Redshift — DW"]
        A_red --> A_qs["QuickSight — BI"]
    end
```

```mermaid
graph TD
    subgraph GCP_Lake["🔵 GCP — Data Lake"]
        G_src["Sources"] --> G_ing["Pub/Sub / Dataflow / Transfer"]
        G_ing --> G_gcs["GCS — Bronze/Silver/Gold"]
        G_gcs --> G_dp["Dataproc — processing"]
        G_gcs --> G_bq["BigQuery — DW + ad hoc"]
        G_bq --> G_looker["Looker — BI"]
        G_bq --> G_vertex["Vertex AI — ML"]
    end
```

```mermaid
graph TD
    subgraph Azure_Lake["🔷 Azure — Data Lake"]
        Az_src["Sources"] --> Az_ing["Event Hubs / ADF / DMS"]
        Az_ing --> Az_adls["ADLS Gen2 — Bronze/Silver/Gold"]
        Az_adls --> Az_syn["Synapse — DW + Spark"]
        Az_syn --> Az_pbi["Power BI — BI"]
        Az_adls --> Az_db["Databricks — processing"]
        Az_db --> Az_syn
    end
```


### Lakehouse Architecture

```mermaid
flowchart LR
    subgraph AWS_LH["🟠 AWS Lakehouse"]
        direction TB
        s3d["S3 + Delta Lake / Iceberg"]
        emrd["EMR / Glue Spark"]
        red2["Redshift Spectrum"]
        s3d <--> emrd
        s3d <--> red2
    end

    subgraph GCP_LH["🔵 GCP Lakehouse"]
        direction TB
        gcsd["GCS + BigLake / Iceberg"]
        dpd["Dataproc Spark"]
        bqd["BigQuery Omni"]
        gcsd <--> dpd
        gcsd <--> bqd
    end

    subgraph Azure_LH["🔷 Azure Lakehouse"]
        direction TB
        adlsd["ADLS Gen2 + Delta Lake"]
        dbd["Databricks"]
        synd["Synapse Link"]
        adlsd <--> dbd
        adlsd <--> synd
    end

    subgraph Multi["🌐 Multi-cloud Lakehouse"]
        direction TB
        Databricks2["Databricks\n(AWS + GCP + Azure)"]
        Snowflake2["Snowflake\n(AWS + GCP + Azure)"]
    end
```


### Streaming Pipeline by Cloud

```mermaid
sequenceDiagram
    participant Source
    participant Stream
    participant Processor
    participant Storage
    participant DW

    Note over Source,DW: 🟠 AWS
    Source->>Stream: Kinesis Data Streams
    Stream->>Processor: Lambda / Flink on EMR
    Processor->>Storage: S3 (Delta/Iceberg)
    Storage->>DW: Redshift Spectrum

    Note over Source,DW: 🔵 GCP
    Source->>Stream: Cloud Pub/Sub
    Stream->>Processor: Dataflow (Apache Beam)
    Processor->>Storage: GCS (BigLake)
    Storage->>DW: BigQuery Streaming Insert

    Note over Source,DW: 🔷 Azure
    Source->>Stream: Event Hubs
    Stream->>Processor: Stream Analytics / Databricks
    Processor->>Storage: ADLS Gen2 (Delta)
    Storage->>DW: Synapse Analytics
```


### Batch Pipeline by Cloud

```mermaid
gantt
    title Daily Batch Pipeline — Component Comparison
    dateFormat HH:mm
    axisFormat %H:%M

    section 🟠 AWS
    Extraction (DMS/Glue)         :a1, 02:00, 30m
    Processing (EMR/Glue)         :a2, after a1, 60m
    Load (Redshift COPY)          :a3, after a2, 20m
    BI available (QuickSight)     :milestone, after a3, 0m

    section 🔵 GCP
    Extraction (Dataflow/Transfer):b1, 02:00, 20m
    Processing (Dataproc)         :b2, after b1, 45m
    Load (BQ Load Job)            :b3, after b2, 10m
    BI available (Looker)         :milestone, after b3, 0m

    section 🔷 Azure
    Extraction (ADF Pipeline)     :c1, 02:00, 25m
    Processing (Databricks)       :c2, after c1, 50m
    Load (Synapse COPY)           :c3, after c2, 15m
    BI available (Power BI)       :milestone, after c3, 0m
```


## Open Source vs Cloud-Native Tools Comparison

```mermaid
graph LR
    subgraph OpenSource["🔓 Open Source / Multi-cloud"]
        Kafka["Apache Kafka\n(messaging)"]
        Spark2["Apache Spark\n(processing)"]
        Airflow2["Apache Airflow\n(orchestration)"]
        dbt2["dbt\n(transformation)"]
        Iceberg2["Apache Iceberg\n(table format)"]
        Flink2["Apache Flink\n(streaming)"]
    end

    subgraph CloudNative["☁️ Cloud-native (lock-in)"]
        AWS_N["Kinesis · Glue · MWAA\nRedshift · Athena"]
        GCP_N["Pub/Sub · Dataflow\nBigQuery · Composer"]
        Az_N["Event Hubs · ADF\nSynapse · Power BI"]
    end

    Kafka -->|"AWS MSK\nGCP —\nAzure Event Hubs"| CloudNative
    Spark2 -->|"AWS EMR\nGCP Dataproc\nAzure Databricks"| CloudNative
    Airflow2 -->|"AWS MWAA\nGCP Composer\nAzure — "| CloudNative
```

| Tool | AWS Equivalent | GCP Equivalent | Azure Equivalent | Lock-in? |
|------------|----------------|----------------|-------------------|----------|
| Apache Kafka | MSK | — (Pub/Sub is different) | Event Hubs | Low (MSK is pure Kafka) |
| Apache Spark | EMR / Glue | Dataproc | HDInsight / Databricks | Low |
| Apache Airflow | MWAA | Cloud Composer | — | Low |
| dbt | dbt Cloud / Redshift | dbt Cloud / BigQuery | dbt Cloud / Synapse | None |
| Apache Flink | Kinesis Analytics | Dataflow | Stream Analytics | Medium |
| Apache Iceberg | Glue + Athena | BigLake | Synapse / Databricks | None |


## Cost Comparison

### Billing Model by Service

```mermaid
graph TD
    subgraph StorageCost["Storage (per GB/month)"]
        S_AWS["AWS S3<br/>~$0.023"]
        S_GCP["GCS<br/>~$0.020"]
        S_Az["ADLS Gen2<br/>~$0.018"]
    end

    subgraph DW_Cost["Data Warehouse (processing)"]
        DW_AWS["Redshift<br/>~$0.25/hour per node<br/>or serverless by RPU"]
        DW_GCP["BigQuery<br/>~$5 per TB scanned<br/>or reserved slots"]
        DW_Az["Synapse<br/>~$1.20 per DWU/hour"]
    end

    subgraph Stream_Cost["Streaming (per hour)"]
        St_AWS["Kinesis<br/>~$0.015/shard/hour"]
        St_GCP["Pub/Sub<br/>~$0.04/GB"]
        St_Az["Event Hubs<br/>~$0.10/TU/hour"]
    end
```

| Scenario | AWS | GCP | Azure | Notes |
|---------|-----|-----|-------|-------------|
| **Storage 100 TB/month** | ~$230 | ~$200 | ~$180 | Prices vary by region |
| **DW: 10 TB scanned/day** | ~$500/month (Redshift) | ~$1,500/month (on-demand) | ~$800/month (Synapse) | BigQuery has cheaper reserved slots at high volume |
| **Spark: 100 hours/month** | ~$200 (EMR Spot) | ~$150 (Dataproc preemptible) | ~$180 (HDInsight Spot) | Spot/preemptible reduces cost by 60-80% |
| **Kafka: 100 GB/day** | ~$400/month (MSK) | ~$120/month (Pub/Sub) | ~$200/month (Event Hubs) | MSK costs more because it is full managed Kafka |
| **Managed Airflow** | ~$350/month (minimum MWAA) | ~$270/month (minimum Composer) | Via ADF (per execution) | All have high base cost |

### Cost Reduction Strategies

```mermaid
flowchart TD
    Cost["💰 Cloud Cost Reduction"] --> S1["Storage Tiers<br/>Move cold data to<br/>Glacier / Coldline / Archive"]
    Cost --> S2["Spot / Preemptible<br/>60-80% cheaper<br/>for fault-tolerant jobs"]
    Cost --> S3["Storage/Compute Separation<br/>Pay for compute only when processing"]
    Cost --> S4["Efficient Partitioning<br/>Reduces data scanned in the DW"]
    Cost --> S5["Reserved / Committed Use<br/>30-60% discount with commitment"]
    Cost --> S6["Auto-scaling<br/>Avoids idle capacity"]
    Cost --> S7["Compression and format<br/>Parquet reduces 5-10x vs CSV"]
```


## Governance and Security by Cloud

```mermaid
graph LR
    subgraph AWS_Gov["🟠 AWS Governance"]
        IAM_A["IAM<br/>(identity and access)"]
        Lake_F["Lake Formation<br/>(Data Lake control)"]
        Macie["Macie<br/>(sensitive data detection)"]
        CloudT["CloudTrail<br/>(auditing)"]
        KMS_A["AWS KMS<br/>(encryption keys)"]
    end

    subgraph GCP_Gov["🔵 GCP Governance"]
        IAM_G["Cloud IAM<br/>(identity and access)"]
        Dataplex2["Dataplex<br/>(Lake governance)"]
        DLP["Cloud DLP<br/>(sensitive data)"]
        Audit["Audit Logs<br/>(auditing)"]
        KMS_G["Cloud KMS<br/>(keys)"]
    end

    subgraph Az_Gov["🔷 Azure Governance"]
        RBAC_Az["Azure RBAC<br/>(identity and access)"]
        Purview2["Microsoft Purview<br/>(catalog + governance)"]
        Defender["Defender for Data<br/>(security)"]
        Monitor["Azure Monitor<br/>(auditing)"]
        KV["Key Vault<br/>(keys and secrets)"]
    end
```

| Criterion | AWS | GCP | Azure |
|----------|-----|-----|-------|
| **Identity management** | AWS IAM | Cloud IAM | Azure AD + RBAC |
| **Data Lake control** | Lake Formation | Dataplex | ADLS ACLs + Purview |
| **Data catalog** | Glue Data Catalog | Dataplex / Data Catalog | Microsoft Purview |
| **Sensitive data detection** | Amazon Macie | Cloud DLP | Microsoft Purview |
| **Managed encryption** | AWS KMS | Cloud KMS | Azure Key Vault |
| **Auditing** | CloudTrail | Cloud Audit Logs | Azure Monitor |
| **Compliance frameworks** | SOC, PCI, LGPD, HIPAA | SOC, PCI, LGPD, HIPAA | SOC, PCI, LGPD, HIPAA |
| **Corporate AD integration** | ❌ (via SSO) | ❌ (via SSO) | ✅ Native |
| **Differentiator** | Granular Lake Formation | Automatic DLP | Unified Purview |


## Machine Learning and AI by Cloud

```mermaid
graph TD
    subgraph ML_Platforms["ML Platforms"]
        SageMaker["🟠 Amazon SageMaker<br/>• Studio (notebooks)<br/>• Feature Store<br/>• Model Registry<br/>• Autopilot (AutoML)<br/>• Pipelines"]
        VertexAI["🔵 Google Vertex AI<br/>• Workbench (notebooks)<br/>• Feature Store<br/>• Model Registry<br/>• AutoML<br/>• Pipelines<br/>• Integrated Gemini"]
        AzureML["🔷 Azure Machine Learning<br/>• Studio (low-code)<br/>• Feature Store<br/>• Model Registry<br/>• AutoML<br/>• Pipelines<br/>• Integrated OpenAI"]
    end
```

| Criterion | Amazon SageMaker | Google Vertex AI | Azure ML |
|----------|-----------------|-----------------|----------|
| **Managed Feature Store** | ✅ | ✅ | ✅ |
| **AutoML** | ✅ Autopilot | ✅ AutoML | ✅ |
| **ML Pipelines** | ✅ | ✅ | ✅ |
| **Foundation Models / LLMs** | Bedrock (third-party) | Gemini / PaLM | OpenAI (Azure OpenAI) |
| **Integrated MLOps** | ✅ | ✅ | ✅ |
| **SQL for ML** | ❌ | ✅ BigQuery ML | ✅ Synapse ML |
| **Data integration** | S3 + Redshift | Native BigQuery | ADLS + Synapse |
| **Differentiator** | Most complete | BigQuery ML + Gemini | OpenAI + Microsoft |


## When to Choose Each Cloud?

```mermaid
flowchart TD
    Start(["Which cloud should you choose?"]) --> Q1{"Already using<br/>Microsoft products?"}

    Q1 -->|Yes| Q1A{"Are Power BI and<br/>Azure AD<br/>critical?"}
    Q1A -->|Yes| Azure["🔷 Azure<br/>Synapse + ADLS + Power BI"]
    Q1A -->|No| Q2

    Q1 -->|No| Q2{"Is analytics<br/>the main focus?"}
    Q2 -->|Yes| Q2A{"Do you want serverless<br/>without managing<br/>clusters?"}
    Q2A -->|Yes| GCP["🔵 GCP<br/>BigQuery + GCS + Dataflow"]
    Q2A -->|No| Q3

    Q2 -->|No| Q3{"Need the<br/>broadest ecosystem<br/>possible?"}
    Q3 -->|Yes| AWS["🟠 AWS<br/>S3 + Redshift + Glue + EMR"]
    Q3 -->|No| Q4{"Multi-cloud<br/>or migration?"}
    Q4 -->|Yes| Multi["🌐 Multi-cloud<br/>Snowflake + Databricks<br/>+ dbt + Kafka"]
    Q4 -->|No| AWS
```

| Scenario | Recommended Cloud | Rationale |
|---------|------------------|---------------|
| Analytics startup with no legacy | GCP | Serverless BigQuery, lower overhead, excellent cost-benefit |
| Enterprise with Active Directory | Azure | Native AD integration, Power BI, Microsoft ecosystem |
| High-volume e-commerce | AWS | Largest ecosystem, Kinesis for streaming, market maturity |
| ML/AI as business core | GCP | Vertex AI + BigQuery ML + Gemini, best data-ML integration |
| Company with everything on AWS | AWS | Native integration, less overhead, egress savings |
| Multi-cloud / portability | Snowflake + Databricks | Avoid lock-in, same API across all 3 clouds |
| Regulated data (health/finance) | Any of the three | All 3 support compliance. Depends on the business partner |


## Executive Summary

```mermaid
graph LR
    subgraph Strengths["✅ Strengths"]
        AWS_S["🟠 AWS<br/>Largest ecosystem<br/>Largest community<br/>Most services available<br/>Best for generalists"]
        GCP_S["🔵 GCP<br/>Unmatched BigQuery<br/>Best pure analytics<br/>Cost-benefit<br/>Integrated ML"]
        Az_S["🔷 Azure<br/>Best for enterprise<br/>Microsoft integration<br/>Native Power BI<br/>Azure OpenAI"]
    end
```

| | 🟠 AWS | 🔵 GCP | 🔷 Azure |
|-|--------|--------|---------|
| **Win when...** | You need the broadest ecosystem and flexibility | Analytics and ML are the core | You already use Microsoft and Power BI |
| **Avoid when...** | Simplicity is the main priority | You want the broadest overall ecosystem | You do not use Active Directory |
| **Iconic service** | S3 | BigQuery | Power BI + Synapse |
| **Streaming** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| **Batch Analytics** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **ML/AI** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Enterprise** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Storage cost** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Documentation** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |


## References

- [AWS Data Analytics Overview](https://aws.amazon.com/big-data/datalakes-and-analytics/)
- [Google Cloud Data Analytics](https://cloud.google.com/solutions/data-analytics)
- [Azure Analytics Services](https://azure.microsoft.com/en-us/solutions/data-analytics/)
- [Gartner Magic Quadrant for Cloud Database Management Systems](https://www.gartner.com/en/documents/cloud-dbms)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)


← [Frameworks and Ecosystem](./16-frameworks-and-ecosystem.md) · [Back to index](./0-data-engineering.md) · [Advanced Techniques →](./18-advanced-techniques.md)


*Documentation in progress · Personal portfolio*
