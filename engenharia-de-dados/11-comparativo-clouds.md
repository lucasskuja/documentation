# Comparativo de Clouds para Engenharia de Dados

> *"A melhor cloud é aquela que resolve o seu problema — não a que está na moda."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## Visão Geral dos Provedores

```mermaid
mindmap
  root((Cloud para<br/>Dados))
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


## Market Share e Posicionamento

```mermaid
quadrantChart
    title Posicionamento das Clouds em Dados
    x-axis Facilidade de Uso --> Alta Complexidade
    y-axis Menor Ecossistema --> Maior Ecossistema
    quadrant-1 "Poderoso e Complexo"
    quadrant-2 "Poderoso e Acessível"
    quadrant-3 "Simples mas Limitado"
    quadrant-4 "Complexo e Nichado"
    AWS: [0.75, 0.95]
    GCP: [0.35, 0.75]
    Azure: [0.60, 0.80]
    Snowflake: [0.20, 0.55]
    Databricks: [0.55, 0.60]
```

| Critério | AWS | GCP | Azure |
|----------|-----|-----|-------|
| **Market Share Cloud Geral** | ~33% | ~11% | ~22% |
| **Ponto forte em dados** | Ecossistema completo | Analytics e ML | Enterprise e Microsoft |
| **Melhor serviço de dados** | S3 + Glue | BigQuery | Synapse + Power BI |
| **Curva de aprendizado** | Alta | Média | Média-Alta |
| **Integração Microsoft** | Baixa | Baixa | Nativa |
| **Maturidade** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |


## Comparativo de Serviços por Categoria

### 🗄️ Object Storage (Data Lake)

```mermaid
graph LR
    subgraph AWS
        S3["🪣 Amazon S3<br/>99.999999999%<br/>durabilidade<br/>lifeycle policies<br/>S3 Intelligent-Tiering"]
    end
    subgraph GCP
        GCS["🪣 Cloud Storage<br/>99.999999999%<br/>durabilidade<br/>Multi-regional<br/>Autoclass"]
    end
    subgraph Azure
        ADLS["🪣 ADLS Gen2<br/>Hierarchical<br/>Namespace<br/>ACLs granulares<br/>Integrado ao AD"]
    end

    S3 -->|"Vencedor em<br/>ecossistema"| Winner["⭐ Referência da Indústria"]
    GCS -->|"Melhor<br/>integração"| Winner
    ADLS -->|"Melhor para<br/>enterprise AD"| Winner
```

| Critério | Amazon S3 | Google Cloud Storage | Azure Data Lake Gen2 |
|----------|-----------|----------------------|----------------------|
| **Durabilidade** | 99.999999999% | 99.999999999% | 99.999999999% |
| **Disponibilidade** | 99.99% | 99.99% | 99.9% |
| **Custo storage (GB/mês)** | ~$0.023 | ~$0.020 | ~$0.018 |
| **Camadas de storage** | Standard, IA, Glacier, Deep Archive | Standard, Nearline, Coldline, Archive | Hot, Cool, Archive |
| **Controle de acesso** | IAM + Bucket Policies | IAM + ACLs | RBAC + ACLs hierárquicas |
| **Versionamento** | ✅ | ✅ | ✅ |
| **Notificações de eventos** | SNS, SQS, Lambda | Pub/Sub, Cloud Functions | Event Grid, Functions |
| **Namespace hierárquico** | Emulado (prefixos) | Emulado (prefixos) | ✅ Nativo |
| **Integração com AD** | ❌ | ❌ | ✅ Azure AD |
| **Melhor para** | Ecossistema AWS, referência | Custo-benefício, GCP nativo | Enterprise com AD |


### 📊 Data Warehouse (OLAP)

```mermaid
graph TD
    subgraph Redshift["Amazon Redshift"]
        R1["Armazenamento Colunar (RA3)"]
        R2["Redshift Spectrum (S3 queries)"]
        R3["Aqua (cache acelerado)"]
        R4["Concurrency Scaling"]
    end

    subgraph BigQuery["Google BigQuery"]
        B1["Serverless — sem cluster"]
        B2["Separação storage/compute total"]
        B3["BQML — ML dentro do SQL"]
        B4["BI Engine (cache in-memory)"]
        B5["Omni — multi-cloud queries"]
    end

    subgraph Synapse["Azure Synapse"]
        S1["Dedicated SQL Pool"]
        S2["Serverless SQL Pool"]
        S3["Apache Spark Pool"]
        S4["Data Explorer Pool"]
    end
```

| Critério | Amazon Redshift | Google BigQuery | Azure Synapse |
|----------|-----------------|-----------------|---------------|
| **Modelo de cobrança** | Por hora de cluster | Por TB escaneado / slot | Por DWU ou serverless |
| **Serverless** | ✅ Redshift Serverless | ✅ Nativo | ✅ Serverless SQL Pool |
| **Separação storage/compute** | ✅ Nós RA3 | ✅ Total | Parcial |
| **Suporte a ML integrado** | ❌ | ✅ BQML | ✅ via Synapse ML |
| **Performance out-of-the-box** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| **Facilidade de operação** | Média | Alta | Média |
| **Suporte a dados semi-estruturados** | Parcial (SUPER type) | ✅ JSON nativo | ✅ |
| **Concorrência** | Concurrency Scaling | Automático | Workload Management |
| **Integração BI nativa** | QuickSight | Looker | Power BI |
| **Melhor para** | Workloads AWS pesados | Analytics de escala máxima | Enterprise Microsoft |


### 🔄 ETL / Ingestão Gerenciada

```mermaid
flowchart LR
    Sources["🗃️ Fontes<br/>CRM · ERP<br/>BDs · APIs"]

    subgraph AWS_ETL["AWS"]
        Glue["AWS Glue<br/>(Spark serverless)"]
        DMS["AWS DMS<br/>(migração e CDC)"]
        AthenaQ["Athena<br/>(query S3)"]
    end

    subgraph GCP_ETL["GCP"]
        Dataflow["Dataflow<br/>(Apache Beam)"]
        DataFusion["Cloud Data Fusion<br/>(low-code ETL)"]
        Composer["Cloud Composer<br/>(Airflow gerenciado)"]
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

| Critério | AWS Glue | GCP Dataflow | Azure Data Factory |
|----------|----------|--------------|--------------------|
| **Paradigma** | Spark serverless | Apache Beam | Low-code + code |
| **Linguagem** | Python, Scala | Python, Java | JSON + Python |
| **Curva de aprendizado** | Média | Alta | Baixa |
| **Batch** | ✅ | ✅ | ✅ |
| **Streaming** | Limitado | ✅ Nativo | ✅ via Event Hubs |
| **Catálogo integrado** | ✅ Glue Data Catalog | ✅ Dataplex | ✅ Purview |
| **Conector nativo para outros clouds** | Limitado | Limitado | ✅ via ADF |
| **Custo** | Por DPU-hora | Por vCPU-hora | Por atividade |
| **Melhor para** | Workloads Spark na AWS | Streaming complexo | Integração enterprise |


### ⚡ Streaming e Mensageria

```mermaid
graph TB
    subgraph Producers["📤 Produtores"]
        App["Aplicações"]
        IoT["IoT Devices"]
        CDC["CDC / Logs"]
    end

    subgraph AWS_S["AWS"]
        Kinesis["Kinesis Data Streams<br/>(streams gerenciados)"]
        KF["Kinesis Firehose<br/>(entrega para S3/Redshift)"]
        MSK["Amazon MSK<br/>(Kafka gerenciado)"]
        SQS["Amazon SQS<br/>(fila de mensagens)"]
    end

    subgraph GCP_S["GCP"]
        PubSub["Cloud Pub/Sub<br/>(mensageria global)"]
        DF2["Dataflow<br/>(processamento stream)"]
    end

    subgraph Azure_S["Azure"]
        EH["Event Hubs<br/>(compatível Kafka)"]
        SB["Service Bus<br/>(fila enterprise)"]
        ASA["Stream Analytics<br/>(processamento SQL)"]
    end

    App --> Kinesis
    App --> PubSub
    App --> EH
    IoT --> MSK
    IoT --> EH
    CDC --> MSK
```

| Critério | AWS Kinesis | AWS MSK (Kafka) | GCP Pub/Sub | Azure Event Hubs |
|----------|-------------|-----------------|-------------|-----------------|
| **Compatibilidade Kafka** | ❌ | ✅ Total | ❌ | ✅ Parcial |
| **Retenção de mensagens** | 7-365 dias | Configurável | 7 dias (max 31) | 1-7 dias |
| **Replay de mensagens** | ✅ | ✅ | Limitado | ✅ |
| **Escala automática** | ✅ | Manual (brokers) | ✅ Automático | ✅ Auto-inflate |
| **Overhead operacional** | Baixo | Médio | Baixo | Baixo |
| **Custo** | Por shard/hora | Por broker/hora | Por GB | Por TU/hora |
| **Latência** | ~200ms | < 10ms | ~100ms | ~100ms |
| **Melhor para** | Integração AWS simples | Kafka-native apps | Apps GCP serverless | Apps Azure / Kafka compat |


### 🔥 Processamento Distribuído (Spark)

```mermaid
graph LR
    subgraph Managed_Spark["Spark Gerenciado por Cloud"]
        EMR["Amazon EMR<br/>• Mais configurável<br/>• Suporte a Hadoop<br/>• Spot instances<br/>• EC2 ou EKS"]
        Dataproc["GCP Dataproc<br/>• Setup em 90s<br/>• Integrado ao BigQuery<br/>• Preemptible VMs<br/>• GKE opcional"]
        HDInsight["Azure HDInsight<br/>• Legado Hadoop<br/>• Integrado ao ADLS<br/>• Em declínio de adoção"]
        ADB["Azure Databricks<br/>• Melhor UX<br/>• Delta Lake nativo<br/>• Unity Catalog<br/>• Multi-cloud"]
    end

    Databricks["🏆 Databricks<br/>(disponível nas 3 clouds)"] --> EMR
    Databricks --> Dataproc
    Databricks --> ADB
```

| Critério | Amazon EMR | GCP Dataproc | Azure HDInsight | Databricks (todas) |
|----------|------------|--------------|-----------------|---------------------|
| **Tempo de startup do cluster** | 5-10 min | ~90 segundos | 15-20 min | 2-5 min |
| **Facilidade de uso** | Média | Alta | Baixa | Alta |
| **Delta Lake nativo** | Configurável | Configurável | Configurável | ✅ Total |
| **Notebook integrado** | EMR Studio | Dataproc Hub | Básico | ✅ Databricks Notebooks |
| **Auto-scaling** | ✅ | ✅ | Limitado | ✅ |
| **Instâncias spot/preemptível** | ✅ | ✅ | ✅ | ✅ |
| **Serverless Spark** | ✅ EMR Serverless | ✅ Dataproc Serverless | ❌ | ✅ |
| **Multi-cloud** | ❌ | ❌ | ❌ | ✅ |
| **Melhor para** | Flexibilidade AWS | Velocidade + GCP | Legado | Lakehouse moderno |


### 🎼 Orquestração

```mermaid
graph TD
    subgraph Cloud_Orchestration["Orquestração Gerenciada"]
        MWAA["Amazon MWAA<br/>Airflow na AWS<br/>Integrado a IAM/VPC<br/>Setup complexo"]
        Composer["Cloud Composer<br/>Airflow no GCP<br/>Mais fácil de configurar<br/>Baseado em GKE"]
        ADF2["Azure Data Factory<br/>Low-code visual<br/>Não é Airflow<br/>Conectores nativos Azure"]
    end

    subgraph SaaS_Orchestration["Orquestração SaaS (multi-cloud)"]
        Astronomer["Astronomer<br/>(Airflow cloud)"]
        Prefect["Prefect Cloud"]
        Dagster["Dagster Cloud"]
    end

    MWAA --> Airflow["Apache Airflow"]
    Composer --> Airflow
    ADF2 --> LowCode["Paradigma<br/>Low-Code"]
```

| Critério | Amazon MWAA | GCP Cloud Composer | Azure Data Factory | Astronomer |
|----------|-----------|--------------------|-------------------|------------|
| **Baseado em Airflow** | ✅ | ✅ | ❌ (proprietário) | ✅ |
| **Facilidade de setup** | Baixa | Média | Alta | Alta |
| **Custo base** | ~$0.49/hora | ~$0.37/hora | Por execução | Por tarefa |
| **Escalabilidade** | Auto | Auto (GKE) | Automática | Auto |
| **Suporte a DAGs como código** | ✅ | ✅ | Parcial (JSON) | ✅ |
| **Integração nativa cloud** | ✅ AWS | ✅ GCP | ✅ Azure | Multi-cloud |
| **Atualizações de versão** | Manual | Manual | Automática | Automática |
| **Melhor para** | Airflow na AWS | Airflow no GCP | Integração Azure visual | Airflow multi-cloud |


## Comparativo de Arquiteturas por Cloud

### Arquitetura Data Lake

```mermaid
graph TD
    subgraph AWS_Lake["🟠 AWS — Data Lake"]
        A_src["Fontes"] --> A_ing["Kinesis / DMS / Glue"]
        A_ing --> A_s3["S3 — Bronze/Silver/Gold"]
        A_s3 --> A_glue["Glue Catalog"]
        A_s3 --> A_athena["Athena — queries ad hoc"]
        A_s3 --> A_emr["EMR — processamento"]
        A_emr --> A_red["Redshift — DW"]
        A_red --> A_qs["QuickSight — BI"]
    end
```

```mermaid
graph TD
    subgraph GCP_Lake["🔵 GCP — Data Lake"]
        G_src["Fontes"] --> G_ing["Pub/Sub / Dataflow / Transfer"]
        G_ing --> G_gcs["GCS — Bronze/Silver/Gold"]
        G_gcs --> G_dp["Dataproc — processamento"]
        G_gcs --> G_bq["BigQuery — DW + ad hoc"]
        G_bq --> G_looker["Looker — BI"]
        G_bq --> G_vertex["Vertex AI — ML"]
    end
```

```mermaid
graph TD
    subgraph Azure_Lake["🔷 Azure — Data Lake"]
        Az_src["Fontes"] --> Az_ing["Event Hubs / ADF / DMS"]
        Az_ing --> Az_adls["ADLS Gen2 — Bronze/Silver/Gold"]
        Az_adls --> Az_syn["Synapse — DW + Spark"]
        Az_syn --> Az_pbi["Power BI — BI"]
        Az_adls --> Az_db["Databricks — processamento"]
        Az_db --> Az_syn
    end
```


### Arquitetura Lakehouse

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


### Pipeline Streaming por Cloud

```mermaid
sequenceDiagram
    participant Fonte
    participant Stream
    participant Processor
    participant Storage
    participant DW

    Note over Fonte,DW: 🟠 AWS
    Fonte->>Stream: Kinesis Data Streams
    Stream->>Processor: Lambda / Flink on EMR
    Processor->>Storage: S3 (Delta/Iceberg)
    Storage->>DW: Redshift Spectrum

    Note over Fonte,DW: 🔵 GCP
    Fonte->>Stream: Cloud Pub/Sub
    Stream->>Processor: Dataflow (Apache Beam)
    Processor->>Storage: GCS (BigLake)
    Storage->>DW: BigQuery Streaming Insert

    Note over Fonte,DW: 🔷 Azure
    Fonte->>Stream: Event Hubs
    Stream->>Processor: Stream Analytics / Databricks
    Processor->>Storage: ADLS Gen2 (Delta)
    Storage->>DW: Synapse Analytics
```


### Pipeline Batch por Cloud

```mermaid
gantt
    title Pipeline Batch Diário — Comparativo de Componentes
    dateFormat HH:mm
    axisFormat %H:%M

    section 🟠 AWS
    Extração (DMS/Glue)         :a1, 02:00, 30m
    Processamento (EMR/Glue)    :a2, after a1, 60m
    Carga (Redshift COPY)       :a3, after a2, 20m
    BI disponível (QuickSight)  :milestone, after a3, 0m

    section 🔵 GCP
    Extração (Dataflow/Transfer):b1, 02:00, 20m
    Processamento (Dataproc)    :b2, after b1, 45m
    Carga (BQ Load Job)         :b3, after b2, 10m
    BI disponível (Looker)      :milestone, after b3, 0m

    section 🔷 Azure
    Extração (ADF Pipeline)     :c1, 02:00, 25m
    Processamento (Databricks)  :c2, after c1, 50m
    Carga (Synapse COPY)        :c3, after c2, 15m
    BI disponível (Power BI)    :milestone, after c3, 0m
```


## Comparativo de Ferramentas Open Source vs Cloud Nativa

```mermaid
graph LR
    subgraph OpenSource["🔓 Open Source / Multi-cloud"]
        Kafka["Apache Kafka\n(mensageria)"]
        Spark2["Apache Spark\n(processamento)"]
        Airflow2["Apache Airflow\n(orquestração)"]
        dbt2["dbt\n(transformação)"]
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

| Ferramenta | AWS Equivalente | GCP Equivalente | Azure Equivalente | Lock-in? |
|------------|----------------|----------------|-------------------|----------|
| Apache Kafka | MSK | — (Pub/Sub diferente) | Event Hubs | Baixo (MSK é Kafka puro) |
| Apache Spark | EMR / Glue | Dataproc | HDInsight / Databricks | Baixo |
| Apache Airflow | MWAA | Cloud Composer | — | Baixo |
| dbt | dbt Cloud / Redshift | dbt Cloud / BigQuery | dbt Cloud / Synapse | Nenhum |
| Apache Flink | Kinesis Analytics | Dataflow | Stream Analytics | Médio |
| Apache Iceberg | Glue + Athena | BigLake | Synapse / Databricks | Nenhum |


## Custo Comparativo

### Modelo de Cobrança por Serviço

```mermaid
graph TD
    subgraph StorageCost["Storage (por GB/mês)"]
        S_AWS["AWS S3<br/>~$0.023"]
        S_GCP["GCS<br/>~$0.020"]
        S_Az["ADLS Gen2<br/>~$0.018"]
    end

    subgraph DW_Cost["Data Warehouse (processamento)"]
        DW_AWS["Redshift<br/>~$0.25/hora por nó<br/>ou serverless por RPU"]
        DW_GCP["BigQuery<br/>~$5 por TB escaneado<br/>ou slots reservados"]
        DW_Az["Synapse<br/>~$1.20 por DWU/hora"]
    end

    subgraph Stream_Cost["Streaming (por hora)"]
        St_AWS["Kinesis<br/>~$0.015/shard/hora"]
        St_GCP["Pub/Sub<br/>~$0.04/GB"]
        St_Az["Event Hubs<br/>~$0.10/TU/hora"]
    end
```

| Cenário | AWS | GCP | Azure | Observações |
|---------|-----|-----|-------|-------------|
| **Storage 100 TB/mês** | ~$230 | ~$200 | ~$180 | Preços variam por região |
| **DW: 10 TB scaneados/dia** | ~$500/mês (Redshift) | ~$1.500/mês (on-demand) | ~$800/mês (Synapse) | BigQuery tem slots reservados mais baratos em alto volume |
| **Spark: 100 horas/mês** | ~$200 (EMR Spot) | ~$150 (Dataproc preemptível) | ~$180 (HDInsight Spot) | Spot/preemptível reduz custo em 60-80% |
| **Kafka: 100 GB/dia** | ~$400/mês (MSK) | ~$120/mês (Pub/Sub) | ~$200/mês (Event Hubs) | MSK mais caro por ser Kafka gerenciado completo |
| **Airflow gerenciado** | ~$350/mês (MWAA mínimo) | ~$270/mês (Composer mínimo) | Via ADF (por execução) | Todos têm custo base alto |

### Estratégias de Redução de Custo

```mermaid
flowchart TD
    Cost["💰 Redução de Custo em Cloud"] --> S1["Storage Tiers<br/>Mova dados frios para<br/>Glacier / Coldline / Archive"]
    Cost --> S2["Spot / Preemptível<br/>60-80% mais barato<br/>para jobs tolerantes a falhas"]
    Cost --> S3["Separação Storage/Compute<br/>Pague compute só quando processa"]
    Cost --> S4["Particionamento eficiente<br/>Reduz dados escaneados no DW"]
    Cost --> S5["Reserved / Committed Use<br/>Desconto de 30-60% com compromisso"]
    Cost --> S6["Auto-scaling<br/>Evita capacidade ociosa"]
    Cost --> S7["Compressão e formato<br/>Parquet reduz 5-10x vs CSV"]
```


## Governança e Segurança por Cloud

```mermaid
graph LR
    subgraph AWS_Gov["🟠 AWS Governance"]
        IAM_A["IAM<br/>(identidade e acesso)"]
        Lake_F["Lake Formation<br/>(controle do Data Lake)"]
        Macie["Macie<br/>(detecção de dados sensíveis)"]
        CloudT["CloudTrail<br/>(auditoria)"]
        KMS_A["AWS KMS<br/>(chaves de criptografia)"]
    end

    subgraph GCP_Gov["🔵 GCP Governance"]
        IAM_G["Cloud IAM<br/>(identidade e acesso)"]
        Dataplex2["Dataplex<br/>(governança do Lake)"]
        DLP["Cloud DLP<br/>(dados sensíveis)"]
        Audit["Audit Logs<br/>(auditoria)"]
        KMS_G["Cloud KMS<br/>(chaves)"]
    end

    subgraph Az_Gov["🔷 Azure Governance"]
        RBAC_Az["Azure RBAC<br/>(identidade e acesso)"]
        Purview2["Microsoft Purview<br/>(catálogo + governança)"]
        Defender["Defender for Data<br/>(segurança)"]
        Monitor["Azure Monitor<br/>(auditoria)"]
        KV["Key Vault<br/>(chaves e segredos)"]
    end
```

| Critério | AWS | GCP | Azure |
|----------|-----|-----|-------|
| **Gestão de identidade** | AWS IAM | Cloud IAM | Azure AD + RBAC |
| **Controle do Data Lake** | Lake Formation | Dataplex | ADLS ACLs + Purview |
| **Catálogo de dados** | Glue Data Catalog | Dataplex / Data Catalog | Microsoft Purview |
| **Detecção de dados sensíveis** | Amazon Macie | Cloud DLP | Microsoft Purview |
| **Criptografia gerenciada** | AWS KMS | Cloud KMS | Azure Key Vault |
| **Auditoria** | CloudTrail | Cloud Audit Logs | Azure Monitor |
| **Compliance frameworks** | SOC, PCI, LGPD, HIPAA | SOC, PCI, LGPD, HIPAA | SOC, PCI, LGPD, HIPAA |
| **Integração com AD corporativo** | ❌ (via SSO) | ❌ (via SSO) | ✅ Nativo |
| **Diferencial** | Lake Formation granular | DLP automático | Purview unificado |


## Machine Learning e IA por Cloud

```mermaid
graph TD
    subgraph ML_Platforms["Plataformas de ML"]
        SageMaker["🟠 Amazon SageMaker<br/>• Studio (notebooks)<br/>• Feature Store<br/>• Model Registry<br/>• Autopilot (AutoML)<br/>• Pipelines"]
        VertexAI["🔵 Google Vertex AI<br/>• Workbench (notebooks)<br/>• Feature Store<br/>• Model Registry<br/>• AutoML<br/>• Pipelines<br/>• Gemini integrado"]
        AzureML["🔷 Azure Machine Learning<br/>• Studio (low-code)<br/>• Feature Store<br/>• Model Registry<br/>• AutoML<br/>• Pipelines<br/>• OpenAI integrado"]
    end
```

| Critério | Amazon SageMaker | Google Vertex AI | Azure ML |
|----------|-----------------|-----------------|----------|
| **Feature Store gerenciado** | ✅ | ✅ | ✅ |
| **AutoML** | ✅ Autopilot | ✅ AutoML | ✅ |
| **ML Pipelines** | ✅ | ✅ | ✅ |
| **Foundation Models / LLMs** | Bedrock (terceiros) | Gemini / PaLM | OpenAI (Azure OpenAI) |
| **MLOps integrado** | ✅ | ✅ | ✅ |
| **SQL para ML** | ❌ | ✅ BigQuery ML | ✅ Synapse ML |
| **Integração com dados** | S3 + Redshift | BigQuery nativo | ADLS + Synapse |
| **Diferencial** | Mais completo | BigQuery ML + Gemini | OpenAI + Microsoft |


## Quando Escolher Cada Cloud?

```mermaid
flowchart TD
    Start(["Qual cloud escolher?"]) --> Q1{"Já usa<br/>produtos Microsoft?"}

    Q1 -->|Sim| Q1A{"Power BI e<br/>Azure AD são<br/>críticos?"}
    Q1A -->|Sim| Azure["🔷 Azure<br/>Synapse + ADLS + Power BI"]
    Q1A -->|Não| Q2

    Q1 -->|Não| Q2{"Analytics é<br/>o foco principal?"}
    Q2 -->|Sim| Q2A{"Quer serverless<br/>sem gerenciar<br/>clusters?"}
    Q2A -->|Sim| GCP["🔵 GCP<br/>BigQuery + GCS + Dataflow"]
    Q2A -->|Não| Q3

    Q2 -->|Não| Q3{"Precisa do<br/>maior ecossistema<br/>possível?"}
    Q3 -->|Sim| AWS["🟠 AWS<br/>S3 + Redshift + Glue + EMR"]
    Q3 -->|Não| Q4{"Multi-cloud<br/>ou migração?"}
    Q4 -->|Sim| Multi["🌐 Multi-cloud<br/>Snowflake + Databricks<br/>+ dbt + Kafka"]
    Q4 -->|Não| AWS
```

| Cenário | Cloud Recomendada | Justificativa |
|---------|------------------|---------------|
| Startup de analytics sem legado | GCP | BigQuery serverless, menor overhead, excelente custo-benefício |
| Enterprise com Active Directory | Azure | Integração nativa com AD, Power BI, ecossistema Microsoft |
| E-commerce de alto volume | AWS | Maior ecossistema, Kinesis para streaming, maturidade de mercado |
| ML/IA como core do negócio | GCP | Vertex AI + BigQuery ML + Gemini, melhor integração dados-ML |
| Empresa com tudo na AWS | AWS | Integração nativa, menos overhead, economia de egress |
| Multi-cloud / portabilidade | Snowflake + Databricks | Evitar lock-in, rodam nas 3 clouds com a mesma API |
| Dados regulados (saúde/financeiro) | Qualquer uma | As 3 têm compliance. Depende do parceiro comercial |


## Resumo Executivo

```mermaid
graph LR
    subgraph Strengths["✅ Pontos Fortes"]
        AWS_S["🟠 AWS<br/>Maior ecossistema<br/>Maior comunidade<br/>Mais serviços disponíveis<br/>Melhor para generalistas"]
        GCP_S["🔵 GCP<br/>BigQuery incomparável<br/>Melhor analytics puro<br/>Custo-benefício<br/>ML integrado"]
        Az_S["🔷 Azure<br/>Melhor para enterprise<br/>Integração Microsoft<br/>Power BI nativo<br/>Azure OpenAI"]
    end
```

| | 🟠 AWS | 🔵 GCP | 🔷 Azure |
|-|--------|--------|---------|
| **Vença quando...** | Precisa do maior ecossistema e flexibilidade | Analytics e ML são o core | Já usa Microsoft e Power BI |
| **Evite quando...** | Simplicidade é prioridade | Quer maior ecossistema geral | Não usa Active Directory |
| **Serviço ícone** | S3 | BigQuery | Power BI + Synapse |
| **Streaming** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |
| **Batch Analytics** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **ML/IA** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **Enterprise** | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Custo storage** | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| **Documentação** | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |


## Referências

- [AWS Data Analytics Overview](https://aws.amazon.com/big-data/datalakes-and-analytics/)
- [Google Cloud Data Analytics](https://cloud.google.com/solutions/data-analytics)
- [Azure Analytics Services](https://azure.microsoft.com/en-us/solutions/data-analytics/)
- [Gartner Magic Quadrant for Cloud Database Management Systems](https://www.gartner.com/en/documents/cloud-dbms)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)


← [Cloud e Infraestrutura](./cloud-e-infraestrutura.md) · [Voltar ao índice](./0-engenharia-de-dados.md) 


*Documentação em construção · Portfólio pessoal*