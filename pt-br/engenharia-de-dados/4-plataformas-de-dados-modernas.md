# Plataformas de Dados Modernas

> *"Uma plataforma de dados integra engines, armazenamento, governança e operação para reduzir o esforço de montar tudo do zero."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## O que é uma Plataforma de Dados?

Uma plataforma de dados é um ambiente integrado que combina diferentes capacidades necessárias para construir, operar e governar soluções de dados. Diferente de um framework isolado, uma plataforma costuma reunir processamento, armazenamento, SQL analytics, notebooks, orquestração, segurança, catálogo, governança, compartilhamento e integração com ferramentas de BI, ML e IA.

**Exemplos de diferença:**

| Tipo | Exemplos | Papel |
|---|---|---|
| Framework / engine | Spark, Flink, Kafka, Trino, dbt | Executa uma função específica |
| Formato / padrão | Parquet, Delta Lake, Iceberg, Hudi | Define como dados são armazenados ou versionados |
| Serviço cloud | BigQuery, Redshift, Glue, EMR, Dataflow | Serviço gerenciado dentro de uma cloud |
| Plataforma integrada | Databricks, Snowflake, Microsoft Fabric | Combina múltiplas capacidades em um produto/plataforma |

Na prática, muitas plataformas integram ou abstraem frameworks. Databricks usa Spark e Delta Lake como peças centrais; Snowflake integra SQL, warehouses, Snowpark, tarefas, streams e compartilhamento; Fabric integra OneLake, Data Factory, Data Engineering, Data Warehouse, Real-Time Intelligence e Power BI.


## Visão Geral

| Plataforma | Perfil principal | Melhor para |
|---|---|---|
| Databricks | Lakehouse e dados + IA | Spark, Delta Lake, ML, streaming, notebooks, governança unificada |
| Snowflake | Cloud Data Platform / Data Cloud | SQL analytics, ELT, compartilhamento de dados, governança e operação simples |
| BigQuery | Data Warehouse serverless | Analytics em escala, baixa operação, SQL, ML integrado e dados no GCP |
| Amazon Redshift | Data Warehouse AWS | Analytics no ecossistema AWS, integração com S3 e workloads SQL |
| Microsoft Fabric | Plataforma SaaS analytics | Organizações Microsoft, OneLake, Power BI e experiências integradas |
| Azure Synapse | Analytics integrado Azure | Ambientes Azure existentes com SQL, Spark e pipelines |
| AWS Glue | Integração serverless | ETL/ELT serverless, Data Catalog, crawlers e jobs Spark |
| Amazon EMR | Big data gerenciado | Spark/Hadoop/Flink com mais controle operacional |
| Dataproc | Spark/Hadoop gerenciado no GCP | Clusters ou Spark serverless no Google Cloud |
| Dataflow | Apache Beam gerenciado | Batch e streaming unificados, pipelines event-driven |


## Databricks

Databricks é uma plataforma Lakehouse/Data Intelligence voltada para engenharia de dados, analytics, machine learning e IA. Ela combina processamento distribuído, armazenamento em formato aberto, SQL analytics, notebooks, workflows e governança.

**Componentes importantes:**

| Componente | Função |
|---|---|
| Apache Spark | Engine principal para processamento distribuído |
| Delta Lake | Table format com transações, time travel, upserts e schema enforcement |
| Databricks SQL | Warehouses SQL para BI e analytics |
| Workflows | Orquestração de jobs, notebooks e pipelines |
| Unity Catalog | Catálogo, permissões, lineage e governança |
| Delta Live Tables | Pipelines declarativos para ingestão e transformação |
| MLflow | Ciclo de vida de modelos de machine learning |
| Notebooks | Desenvolvimento colaborativo em SQL, Python, Scala e R |

**Use quando:**
- O time precisa de Spark gerenciado com boa experiência de desenvolvimento.
- A arquitetura é Lakehouse baseada em Delta Lake.
- Há workloads combinando engenharia de dados, ML, IA e streaming.
- Governança centralizada de dados e modelos é importante.
- A organização quer uma camada multi-cloud relativamente consistente.

**Evite quando:**
- A maior parte do workload é SQL simples e cabe bem em um Data Warehouse serverless.
- O time não precisa de Spark, notebooks ou ML integrado.
- O custo de clusters e warehouses não será bem governado.

**Ponto de atenção:** Databricks reduz muito a complexidade de operar Spark, mas não elimina a necessidade de boas práticas de particionamento, tuning, permissões, custo e modelagem.


## Snowflake

Snowflake é uma plataforma cloud de dados com forte foco em SQL analytics, separação entre armazenamento e compute, operação simplificada, governança e compartilhamento de dados.

**Componentes importantes:**

| Componente | Função |
|---|---|
| Virtual Warehouses | Compute isolado para queries e workloads |
| Databases, schemas e tables | Organização lógica dos dados |
| Snowpark | Execução de código Python, Java ou Scala próximo aos dados |
| Streams | Rastreamento de mudanças em tabelas |
| Tasks | Agendamento e automação de SQL/workflows |
| Snowpipe | Ingestão contínua ou automatizada |
| Data Sharing | Compartilhamento seguro de dados |
| Time Travel | Recuperação e consulta histórica |
| Iceberg Tables | Integração com dados em formatos abertos |

**Use quando:**
- O core do uso é SQL analytics, BI e ELT.
- O time quer baixa operação e governança forte.
- Há muitos consumidores de dados e necessidade de compartilhamento.
- A organização trabalha em estratégia multi-cloud.
- A simplicidade operacional vale mais que controle fino da engine.

**Evite quando:**
- O workload depende fortemente de Spark nativo.
- O processamento exige bibliotecas ou padrões muito específicos fora do ecossistema Snowflake.
- A arquitetura exige controle detalhado de cluster, runtime e filesystem.

**Ponto de atenção:** Snowflake é excelente para analytics e ELT, mas ainda exige disciplina em modelagem, warehouses, clustering, custos e desenho de pipelines.


## Google BigQuery

BigQuery é o Data Warehouse serverless do Google Cloud. Ele é muito usado para analytics em larga escala com baixa necessidade de administração de infraestrutura.

**Componentes importantes:**

| Componente | Função |
|---|---|
| Datasets e tables | Organização lógica dos dados |
| Slots / reservations | Modelo de capacidade para workloads previsíveis |
| On-demand queries | Cobrança baseada em dados processados |
| Partitioning e clustering | Otimização de custo e performance |
| BigQuery ML | Modelos de ML usando SQL |
| BigQuery Data Transfer Service | Ingestão gerenciada de fontes comuns |
| BigLake | Governança e consulta sobre dados em object storage |
| Storage API | Leitura e escrita em alta performance |

**Use quando:**
- O time quer analytics serverless com pouco overhead operacional.
- Os dados e ferramentas já estão no GCP.
- Queries analíticas em grande escala são prioridade.
- O time quer integrar SQL analytics com ML e dados externos.

**Evite quando:**
- A previsibilidade de custo por query ainda não está bem governada.
- A organização está fortemente padronizada em outra cloud.
- O workload exige controle fino de runtime distribuído.


## Amazon Redshift

Amazon Redshift é o Data Warehouse gerenciado da AWS, disponível em clusters provisionados e modo serverless. Ele é forte para organizações que já concentram dados, segurança e operação no ecossistema AWS.

**Componentes importantes:**

| Componente | Função |
|---|---|
| Redshift Serverless | Execução sem gerenciar clusters provisionados |
| Clusters provisionados | Mais controle sobre capacidade e configuração |
| Redshift Spectrum | Consulta dados diretamente no S3 |
| RA3 nodes | Separação entre compute e storage gerenciado |
| COPY | Carga eficiente de dados |
| Materialized views | Otimização de consultas recorrentes |
| Integration com AWS | S3, Glue Data Catalog, IAM, Lake Formation |

**Use quando:**
- O ambiente é majoritariamente AWS.
- O time precisa de Data Warehouse SQL integrado ao S3.
- Há requisitos fortes de IAM, VPC, Lake Formation e serviços AWS.

**Evite quando:**
- O time quer uma experiência totalmente serverless e multi-cloud por padrão.
- O workload é mais Lakehouse/Spark/ML do que SQL warehouse.


## Microsoft Fabric e Azure Synapse

Microsoft Fabric é uma plataforma SaaS de analytics que integra experiências de dados em torno do OneLake e do ecossistema Microsoft. Azure Synapse continua comum em arquiteturas Azure existentes, combinando SQL, Spark e pipelines.

**Componentes importantes no Fabric:**

| Componente | Função |
|---|---|
| OneLake | Camada única de armazenamento para a organização |
| Data Factory | Ingestão e integração de dados |
| Data Engineering | Spark notebooks, lakehouses e pipelines |
| Data Warehouse | Workloads SQL analíticos |
| Real-Time Intelligence | Eventos, streaming e análise em tempo real |
| Power BI | Consumo, dashboards e camada semântica |
| Purview integration | Governança, catálogo e classificação |

**Use quando:**
- A organização já usa Microsoft 365, Azure, Power BI e Entra ID.
- O objetivo é reduzir fricção entre engenharia, analytics e BI.
- O time quer uma experiência SaaS mais integrada.

**Evite quando:**
- A organização precisa de portabilidade multi-cloud ampla.
- O time quer montar uma stack aberta com controle detalhado de cada peça.


## AWS Glue e Amazon EMR

Glue e EMR são serviços AWS que muitas vezes aparecem juntos em arquiteturas de dados.

| Serviço | Melhor para |
|---|---|
| AWS Glue | ETL/ELT serverless, catálogo, crawlers, jobs Spark e integração |
| Amazon EMR | Clusters gerenciados para Spark, Hadoop, Hive, Flink, Trino e workloads customizados |

**Use Glue quando:**
- Você quer menos operação.
- O job é ETL/ELT comum em Spark ou Python.
- O Glue Data Catalog é o catálogo central do Data Lake.

**Use EMR quando:**
- Você precisa de mais controle de cluster, versões e configurações.
- O workload exige frameworks variados ou tuning mais profundo.
- O time consegue operar clusters com disciplina de custo.


## Dataproc e Dataflow

No Google Cloud, Dataproc e Dataflow cobrem perfis diferentes de processamento.

| Serviço | Melhor para |
|---|---|
| Dataproc / Managed Service for Apache Spark | Spark/Hadoop gerenciado ou serverless |
| Dataflow | Pipelines Apache Beam batch e streaming |

**Use Dataproc quando:**
- O workload é Spark, PySpark, Hadoop, Hive, Flink ou Trino.
- Você quer migrar workloads Hadoop/Spark existentes para GCP.
- O time precisa de mais controle sobre runtime de big data.

**Use Dataflow quando:**
- O pipeline é event-driven ou streaming.
- Você quer um modelo unificado batch + streaming com Apache Beam.
- O foco é processamento contínuo com autoscaling gerenciado.


## Comparativo por Caso de Uso

| Caso de uso | Plataformas candidatas |
|---|---|
| BI e SQL analytics simples | BigQuery, Snowflake, Redshift, Fabric Warehouse |
| Lakehouse com Spark | Databricks, EMR, Dataproc, Synapse/Fabric Data Engineering |
| Multi-cloud | Snowflake, Databricks |
| Ecossistema AWS | Redshift, Glue, EMR, Athena, Lake Formation |
| Ecossistema GCP | BigQuery, Dataproc, Dataflow, Dataplex |
| Ecossistema Microsoft | Fabric, Synapse, Azure Databricks, Power BI |
| Streaming complexo | Flink gerenciado, Dataflow, Databricks Structured Streaming |
| ML/IA integrado aos dados | Databricks, BigQuery, Snowflake, Fabric |
| Data sharing externo | Snowflake, Databricks, BigQuery Analytics Hub |
| Menor operação possível | BigQuery, Snowflake, Fabric, Glue |


## Como Escolher uma Plataforma

| Pergunta | Direção provável |
|---|---|
| O workload é principalmente SQL? | BigQuery, Snowflake, Redshift, Fabric Warehouse |
| Precisa de Spark intensivo? | Databricks, EMR, Dataproc, Fabric/Synapse Spark |
| Precisa de notebooks e ML próximos aos dados? | Databricks, Fabric, BigQuery notebooks, Snowpark |
| Precisa ser multi-cloud? | Snowflake ou Databricks |
| A empresa já é AWS/GCP/Azure? | Comece pela plataforma nativa da cloud |
| O time é pequeno? | Prefira serverless e SaaS |
| O custo precisa ser muito previsível? | Use reservas/capacity planning e monitore workloads |
| Há forte regulação? | Priorize catálogo, lineage, IAM, auditoria e mascaramento |


## Open Source vs Plataforma Gerenciada

| Abordagem | Vantagens | Desvantagens |
|---|---|---|
| Open source montado internamente | Controle, portabilidade, customização | Mais operação, segurança e manutenção |
| Plataforma gerenciada | Menos operação, integração, suporte, governança pronta | Custo, lock-in e menos controle fino |
| Híbrida | Equilíbrio entre controle e produtividade | Exige arquitetura e governança bem definidas |

Uma decisão madura raramente é "open source ou gerenciado" de forma absoluta. O mais comum é combinar ferramentas abertas, serviços cloud e plataformas integradas conforme criticidade, custo, equipe e maturidade.


## Boas Práticas

- Defina workloads antes de escolher a plataforma.
- Diferencie storage, compute, catálogo, orquestração e consumo.
- Monitore custo por domínio, pipeline, workspace, warehouse ou cluster.
- Crie padrões de naming, camadas e permissões.
- Use ambientes separados para desenvolvimento, homologação e produção.
- Centralize governança, lineage e classificação de dados sensíveis.
- Evite colocar lógica crítica apenas em notebooks sem versionamento.
- Documente quando usar SQL, Spark, ELT, streaming ou notebooks.
- Reavalie a arquitetura quando volume, latência ou perfil de uso mudarem.


## Referências

- [Databricks Documentation](https://docs.databricks.com/)
- [Snowflake Documentation](https://docs.snowflake.com/)
- [Google BigQuery Documentation](https://cloud.google.com/bigquery/docs)
- [Amazon Redshift Documentation](https://docs.aws.amazon.com/redshift/)
- [Microsoft Fabric Documentation](https://learn.microsoft.com/en-us/fabric/)
- [AWS Glue Documentation](https://docs.aws.amazon.com/glue/)
- [Amazon EMR Documentation](https://docs.aws.amazon.com/emr/)
- [Google Cloud Managed Service for Apache Spark](https://cloud.google.com/dataproc/docs)
- [Google Cloud Dataflow Documentation](https://cloud.google.com/dataflow/docs)


← [Cloud e Infraestrutura](./3-cloud-e-infraestrutura.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Ingestão de Dados →](./5-ingestao-de-dados.md)


*Documentação em construção · Portfólio pessoal*
