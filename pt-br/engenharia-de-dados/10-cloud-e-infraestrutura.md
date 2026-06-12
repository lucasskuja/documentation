# Cloud e Infraestrutura

> *"A nuvem não elimina a complexidade da infraestrutura — ela a abstrai para que você foque no que importa."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## Por que Cloud é Central na Engenharia de Dados?

A maioria dos sistemas de dados modernos roda em provedores de nuvem pública. A adoção de cloud transformou radicalmente a engenharia de dados:

- **Escalabilidade elástica:** cresce e diminui sob demanda, sem provisionar hardware antecipadamente
- **Separação de storage e compute:** armazene petabytes a baixo custo e processe apenas quando necessário
- **Serviços gerenciados:** bancos de dados, orquestradores, processadores — sem operar a infraestrutura subjacente
- **Modelo pay-as-you-go:** paga apenas pelo que usa, sem grandes investimentos iniciais (CapEx → OpEx)
- **Alta disponibilidade e resiliência** nativas, com SLAs de 99,9%+


## Os Três Grandes Provedores

### ☁️ Amazon Web Services (AWS)

O provedor com maior market share e ecossistema mais maduro. Pioneiro em cloud, com enorme variedade de serviços.

**Serviços principais para dados:**

| Serviço | Categoria | Descrição |
|---------|-----------|-----------|
| **S3** | Object Storage | Data Lake, armazenamento de arquivos |
| **Redshift** | Data Warehouse | OLAP, queries analíticas |
| **Glue** | ETL / Catálogo | Jobs Spark gerenciados + Data Catalog |
| **Athena** | Query Engine | SQL sobre S3 sem servidor |
| **Kinesis** | Streaming | Ingestão de streams em tempo real |
| **MSK** | Kafka gerenciado | Apache Kafka como serviço |
| **EMR** | Hadoop / Spark | Cluster Spark/Hadoop gerenciado |
| **Lake Formation** | Governança | Controle de acesso ao Data Lake |
| **RDS / Aurora** | Banco relacional | PostgreSQL, MySQL gerenciados |
| **DynamoDB** | NoSQL | Chave-valor e documento, serverless |
| **Step Functions** | Orquestração | Workflows serverless |
| **MWAA** | Airflow gerenciado | Apache Airflow como serviço |


### ☁️ Google Cloud Platform (GCP)

Reconhecido pela excelência em analytics e ML. BigQuery é amplamente considerado o Data Warehouse mais poderoso e fácil de operar.

**Serviços principais para dados:**

| Serviço | Categoria | Descrição |
|---------|-----------|-----------|
| **Cloud Storage (GCS)** | Object Storage | Data Lake, equivalente ao S3 |
| **BigQuery** | Data Warehouse | Serverless, extrema escala, ML integrado |
| **Dataflow** | Processamento | Apache Beam gerenciado (batch + streaming) |
| **Dataproc** | Hadoop / Spark | Cluster Spark/Hadoop gerenciado |
| **Pub/Sub** | Mensageria | Streaming de mensagens |
| **Looker** | BI | Ferramenta de BI com Semantic Layer |
| **Vertex AI** | ML Platform | Treinamento e deploy de modelos |
| **Cloud Composer** | Airflow gerenciado | Apache Airflow como serviço |
| **Cloud Spanner** | NewSQL | Banco relacional globalmente distribuído |
| **Bigtable** | NoSQL colunar | HBase gerenciado, IoT e time series |
| **Dataplex** | Governança | Catálogo e governança de Data Lake |


### ☁️ Microsoft Azure

Forte penetração em empresas com ecossistema Microsoft. Integração nativa com Power BI e ferramentas Office.

**Serviços principais para dados:**

| Serviço | Categoria | Descrição |
|---------|-----------|-----------|
| **ADLS Gen2** | Object Storage | Azure Data Lake Storage |
| **Azure Synapse** | Analytics integrado | DW + Spark + Pipeline em uma plataforma |
| **Azure Data Factory** | ETL / Orquestração | Pipelines de dados low-code |
| **Event Hubs** | Streaming | Ingestão de eventos (compatível com Kafka) |
| **HDInsight** | Hadoop / Spark | Cluster gerenciado |
| **Cosmos DB** | NoSQL | Multi-modelo, distribuído globalmente |
| **Azure SQL** | Banco relacional | SQL Server gerenciado |
| **Power BI** | BI | Ferramenta BI da Microsoft |
| **Purview** | Governança | Catálogo e lineage |
| **Databricks** | Lakehouse | Disponível em Azure, AWS e GCP |


## Comparativo por Caso de Uso

| Necessidade | AWS | GCP | Azure |
|-------------|-----|-----|-------|
| Data Warehouse | Redshift | **BigQuery** ⭐ | Synapse Analytics |
| Object Storage | **S3** ⭐ | GCS | ADLS Gen2 |
| Kafka gerenciado | MSK | — | Event Hubs |
| Spark gerenciado | EMR | Dataproc | HDInsight / Synapse |
| Airflow gerenciado | MWAA | Cloud Composer | — |
| BI integrado | QuickSight | **Looker** ⭐ | **Power BI** ⭐ |
| ML Platform | SageMaker | **Vertex AI** ⭐ | Azure ML |
| Ecossistema geral | **Mais amplo** ⭐ | Analytics | Enterprise Microsoft |


## Modelos de Serviço Cloud

### IaaS — Infrastructure as a Service
Você aluga infraestrutura (VMs, storage, rede) e gerencia o sistema operacional, middleware e aplicações. Máximo controle, máxima responsabilidade operacional.

**Exemplos:** EC2 (AWS), Compute Engine (GCP), Azure VMs.

**Quando usar em dados:** clusters Spark self-managed, bancos de dados não disponíveis como serviço gerenciado.

### PaaS — Platform as a Service
O provedor gerencia a infraestrutura subjacente; você gerencia apenas a aplicação e os dados. Menos controle, menos overhead operacional.

**Exemplos:** Cloud Composer, MWAA, RDS, BigQuery.

**Quando usar:** a maioria dos serviços de dados modernos.

### SaaS — Software as a Service
Solução completa gerenciada pelo fornecedor. Você apenas usa.

**Exemplos:** Snowflake, Fivetran, dbt Cloud, Looker.

**Quando usar:** quando o custo operacional de self-hosting supera o custo da solução gerenciada.

### Serverless
Você paga apenas pelo tempo de execução real, sem gerenciar servidores ou alocar capacidade. Ideal para cargas variáveis.

**Exemplos:** BigQuery (queries), Athena, AWS Lambda, Cloud Functions.


## Infrastructure as Code (IaC)

IaC é a prática de **gerenciar e provisionar infraestrutura através de código**, não de processos manuais. Assim como o código de aplicação é versionado e revisado, a infraestrutura também deve ser.

**Benefícios:**
- Reprodutibilidade: recria o ambiente identicamente em qualquer cloud ou região
- Versionamento: histórico de mudanças na infraestrutura no Git
- Revisão: infraestrutura passa por code review antes de ser aplicada
- Automação: provisiona e destrói ambientes automaticamente (CI/CD)

### Terraform
A ferramenta de IaC mais popular. Multi-cloud, com suporte a AWS, GCP, Azure e centenas de outros provedores.

```hcl
# Cria um bucket S3 para o Data Lake
resource "aws_s3_bucket" "datalake_bronze" {
  bucket = "minha-empresa-datalake-bronze"

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

# Cluster Redshift
resource "aws_redshift_cluster" "analytics" {
  cluster_identifier = "analytics-cluster"
  node_type          = "ra3.xlplus"
  number_of_nodes    = 2
  database_name      = "analytics"
  master_username    = var.redshift_user
  master_password    = var.redshift_password
}
```

### Outros IaC
- **AWS CDK / CloudFormation:** IaC nativo da AWS
- **Pulumi:** IaC em linguagens de programação convencionais (Python, TypeScript)
- **Ansible:** automação de configuração de servidores


## Containers e Kubernetes

### Docker
Containers Docker empacotam aplicações e suas dependências em unidades portáveis e reprodutíveis. Fundamentais para garantir que um pipeline que funciona no laptop funcione identicamente em produção.

```dockerfile
# Dockerfile para pipeline Python
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY src/ .

CMD ["python", "pipeline.py"]
```

**Uso em dados:** empacotar jobs Spark, scripts de ingestão, workers do Airflow, modelos de ML.

### Kubernetes (K8s)
Orquestrador de containers para deploy, escalonamento e gerenciamento de aplicações containerizadas em clusters.

**Uso em dados:**
- Executar workers do Airflow (KubernetesExecutor)
- Deploy de aplicações de streaming (Flink, Spark Streaming)
- Feature stores e APIs de modelos ML
- Pipelines de dados efêmeros (um pod por execução)

**Serviços gerenciados:** EKS (AWS), GKE (GCP), AKS (Azure).


## Redes e Segurança de Infraestrutura

### VPC — Virtual Private Cloud
Rede privada isolada dentro da cloud. Todos os recursos de dados sensíveis devem rodar dentro de uma VPC, não na internet pública.

### Subnets Públicas e Privadas
- **Subnet pública:** recursos com acesso à internet (load balancers, NAT gateways)
- **Subnet privada:** recursos sem acesso direto à internet (bancos de dados, clusters de processamento, dados sensíveis)

### VPC Peering / PrivateLink
Conexão privada entre VPCs ou entre sua VPC e serviços cloud, sem trafegar pela internet pública. Essencial para mover dados com segurança.

### IAM — Identity and Access Management
Sistema de controle de acesso da cloud. Define quais usuários, grupos e serviços têm acesso a quais recursos.

**Boas práticas:**
- Nunca use a conta root para operações rotineiras
- Crie roles com permissões mínimas para cada serviço
- Habilite MFA para todos os usuários com acesso a dados sensíveis
- Rotacione credenciais regularmente


## Custo em Cloud

Custo é uma das maiores preocupações em arquiteturas cloud de dados. Algumas estratégias para otimização:

**Separação storage/compute:** com BigQuery ou Snowflake, você paga pelo storage sempre, mas pelo compute apenas quando processa. Configure queries eficientes para reduzir dados escaneados.

**Spot / Preemptible instances:** instâncias interrompíveis custam 60-90% menos. Ideais para jobs batch tolerantes a interrupções (Spark, EMR).

**Auto-scaling:** configure clusters para crescer e diminuir automaticamente com a carga, evitando pagar por capacidade ociosa.

**Storage tiers:** mova dados antigos para camadas frias (S3 Glacier, GCS Coldline) automaticamente com lifecycle policies.

**Particionamento e clustering:** em BigQuery e Snowflake, tabelas bem particionadas e clusterizadas reduzem drasticamente o volume de dados escaneados por query — e consequentemente o custo.

**FinOps:** estabeleça orçamentos (budgets), alertas de custo e revisões periódicas de gastos. Tagging consistente de recursos permite atribuir custos por time, produto ou pipeline.


## Padrão de Arquitetura Cloud para Dados

```
┌─────────────────────────────────────────────────────────────────┐
│                        VPC Privada                              │
│                                                                 │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────┐    │
│  │   Ingestão   │   │  Processam.  │   │  Orquestração    │    │
│  │              │   │              │   │                  │    │
│  │ Kafka (MSK)  │   │  Spark (EMR) │   │ Airflow (MWAA)   │    │
│  │ Fivetran     │   │  dbt Cloud   │   │                  │    │
│  └──────┬───────┘   └──────┬───────┘   └──────────────────┘    │
│         │                 │                                     │
│         ▼                 ▼                                     │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    Object Storage (S3)                   │   │
│  │    Bronze/Raw    │    Silver/Refined    │    Gold/Curated │   │
│  └──────────────────────────────────────────────────────────┘   │
│                             │                                   │
│                             ▼                                   │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────┐    │
│  │  Data Warehouse│  │  Catálogo    │   │  Monitoramento   │    │
│  │  (Redshift /  │  │  (DataHub)   │   │  (CloudWatch +   │    │
│  │   BigQuery)   │  │              │   │   Grafana)       │    │
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


## Boas Práticas de Infraestrutura

✅ **Tudo como código:** toda infraestrutura provisionada via Terraform ou equivalente, versionada no Git.

✅ **Ambientes separados:** desenvolvimento, staging e produção com isolamento total de dados e credenciais.

✅ **Princípio do menor privilégio:** cada serviço e usuário com permissões mínimas necessárias.

✅ **Monitoramento de custos:** alertas de budget e revisões periódicas de gastos.

✅ **Backup e disaster recovery:** defina RPO (Recovery Point Objective) e RTO (Recovery Time Objective) para dados críticos.

✅ **Criptografia em trânsito e repouso:** padrão para todos os dados, especialmente os sensíveis.

✅ **Tags em todos os recursos:** facilita gerenciamento de custos, propriedade e automação.


## Referências

- [AWS Data Analytics Learning Path](https://aws.amazon.com/training/learn-about/analytics/)
- [Google Cloud Data Engineering](https://cloud.google.com/training/data-engineering-and-analytics)
- [Terraform Documentation](https://developer.hashicorp.com/terraform/docs)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- [FinOps Foundation](https://www.finops.org/)


← [Governança e Segurança](./9-governanca-e-seguranca.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Comparativo de Clouds para Engenharia de Dados →](./11-comparativo-clouds.md)


*Documentação em construção · Portfólio pessoal*
