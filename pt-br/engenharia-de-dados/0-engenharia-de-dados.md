# Engenharia de Dados

[English](../../en/data-engineering/0-data-engineering.md) | Português (Brasil)

> *"Dados são o novo petróleo — mas petróleo bruto precisa ser refinado para ter valor."*


## O que é Engenharia de Dados?

Engenharia de Dados é a disciplina responsável por **projetar, construir e manter a infraestrutura e os sistemas** que permitem a coleta, armazenamento, transformação e disponibilização de dados de forma confiável, escalável e eficiente.

Enquanto cientistas de dados analisam e extraem insights dos dados, são os engenheiros de dados que garantem que esses dados existam, estejam acessíveis e com qualidade suficiente para serem usados. Em outras palavras: **o engenheiro de dados constrói os canos; outros bebem a água**.

O papel evoluiu a partir da engenharia de software e da administração de bancos de dados, mas ganhou identidade própria com o crescimento exponencial do volume de dados e a necessidade de pipelines robustos em tempo real ou em lote (*batch*).


## Principais Conceitos

A seguir, uma trilha em ordem lógica para formar o raciocínio de um engenheiro de dados: entender, desenhar, implantar a base, capturar, guardar, transformar, automatizar, confiabilizar, governar e servir dados ao negócio.


### 🏗️ [Arquitetura de Dados](./1-arquitetura-de-dados.md)

Define como os componentes de um sistema de dados se organizam e se comunicam. Inclui decisões sobre onde os dados são armazenados, como trafegam entre sistemas e quais padrões arquiteturais são adotados.

Conceitos-chave:
- Data Warehouse
- Data Lake
- Data Lakehouse
- Lambda Architecture
- Kappa Architecture
- Medallion Architecture


### 🧱 [Modelagem de Dados](./2-modelagem-de-dados.md)

Fundamentos e decisões de modelagem conceitual, lógica e física. Inclui entidades, relacionamentos, chaves, cardinalidade, normalização, modelagem dimensional, Data Vault, modelagem para Lakehouse e checklist de produção.


### ☁️ [Cloud e Infraestrutura](./3-cloud-e-infraestrutura.md)

A maioria dos sistemas de dados modernos roda em nuvem. Compreender serviços, redes, IAM, IaC, containers, serverless, custo e escalabilidade é essencial para projetar plataformas confiáveis.

Provedores principais: AWS, Google Cloud (GCP), Microsoft Azure.


### 🏢 [Plataformas de Dados Modernas](./4-plataformas-de-dados-modernas.md)

Aprofundamento em plataformas integradas como Databricks, Snowflake, BigQuery, Redshift, Microsoft Fabric, Synapse, Glue, EMR, Dataproc e Dataflow. Mostra como elas combinam frameworks, serviços, governança, compute, storage e experiências de desenvolvimento.


### 📥 [Ingestão de Dados](./5-ingestao-de-dados.md)

É o processo de **capturar e importar dados** de diversas fontes, como bancos de dados, APIs, arquivos, eventos e streams, para o ambiente de dados centralizado.

Ferramentas comuns: Apache Kafka, AWS Kinesis, Airbyte, Fivetran, NiFi.


### 🌊 [Streaming e Eventos](./6-streaming-e-eventos.md)

Aprofundamento em arquiteturas orientadas a eventos, Kafka, particionamento, Schema Registry, CDC, Debezium, event time, watermarks, semânticas de entrega, DLQ e replay.


### 🗄️ [Armazenamento de Dados](./7-armazenamento-de-dados.md)

Abrange as tecnologias e estratégias para **persistir dados** de forma eficiente, considerando custo, performance e padrão de acesso.

Principais categorias:
- Bancos relacionais (OLTP)
- Data Warehouses (OLAP)
- Data Lakes e Lakehouses
- Bancos NoSQL
- Formatos de arquivo


### ⚙️ [Processamento de Dados](./8-processamento-de-dados.md)

Envolve **transformar dados brutos em dados úteis**, aplicando limpeza, agregações, enriquecimento e regras de negócio. É nessa etapa que os dados ganham significado.

Ferramentas comuns: Apache Spark, dbt, Flink, Trino, DuckDB, Pandas.


### 🧮 [SQL Avançado](./9-sql-avancado.md)

Técnicas avançadas de SQL usadas em engenharia de dados: CTEs, window functions, deduplicação, joins, modelos incrementais, MERGE, performance, EXPLAIN e boas práticas para queries analíticas.


### 🔁 [Pipelines de Dados](./10-pipelines-de-dados.md)

Um pipeline de dados é o **conjunto de etapas automatizadas** que movem e transformam dados desde a fonte até o destino final, como dashboards, modelos de ML, APIs ou produtos de dados.

Boas práticas incluem: idempotência, rastreabilidade, tratamento de erros e reprocessamento.


### 🎼 [Orquestração](./11-orquestacao.md)

Orquestração é o **gerenciamento e agendamento da execução dos pipelines**, garantindo que as tarefas rodem na ordem correta, no momento certo, e com tratamento adequado de falhas.

Ferramentas comuns: Apache Airflow, Prefect, Dagster, Mage.


### 🚀 [DataOps e CI/CD para Dados](./12-dataops-e-cicd.md)

Práticas de engenharia para versionar, testar, publicar e operar pipelines de dados com segurança. Inclui Git, ambientes, testes, CI/CD, secrets, deploy, rollback e observabilidade operacional.


### ✅ [Qualidade de Dados](./13-qualidade-de-dados.md)

Garante que os dados sejam **precisos, completos, consistentes e confiáveis**. Sem qualidade, qualquer análise, métrica ou modelo é comprometido.

Ferramentas comuns: Great Expectations, Soda, dbt tests, Monte Carlo.


### 🔍 [Observabilidade e Monitoramento](./14-observabilidade.md)

Envolve **monitorar a saúde dos pipelines e dos dados** em produção, detectando anomalias, falhas e desvios antes que impactem os consumidores.

Inclui: logs, métricas, alertas, lineage, freshness e catálogo de dados.


### 🔐 [Governança e Segurança](./15-governanca-e-seguranca.md)

Define **políticas, papéis e controles** para garantir que os dados sejam usados de forma ética, segura e em conformidade com regulações como LGPD e GDPR.

Abrange: controle de acesso, criptografia, mascaramento, catalogação, classificação e políticas de retenção.


### 🧰 [Frameworks e Ecossistema](./16-frameworks-e-ecossistema.md)

Aprofundamento nos principais frameworks e ferramentas usados em engenharia de dados, como Spark, PySpark, Hadoop, Kafka, Flink, dbt, Airflow, Delta Lake, Iceberg, Hudi, Trino e ferramentas de qualidade, catálogo e infraestrutura.


### 🌐 [Comparativo de Clouds para Engenharia de Dados](./17-comparativo-clouds.md)

Comparação dos principais provedores de nuvem (AWS, GCP, Azure) em relação aos serviços de dados que oferecem, custos, facilidade de uso e casos de uso recomendados.


### 🧪 [Técnicas Avançadas](./18-tecnicas-avancadas.md)

Conjunto de práticas para tornar pipelines mais robustos em produção: particionamento, compactação, tuning de Spark, CDC, upserts, idempotência, backfill, watermarks, contratos de dados, observabilidade e otimização de custo.


### 📐 [Camada Semântica e Analytics Engineering](./19-camada-semantica-e-analytics-engineering.md)

Como transformar tabelas em métricas, dimensões e conceitos de negócio consistentes. Inclui data marts, métricas certificadas, dbt Semantic Layer, LookML, Cube, contratos e governança de métricas.


## Como usar esta documentação

Cada seção acima é um ponto de entrada para um documento dedicado com maior profundidade. A ideia é que você navegue pelos tópicos conforme sua necessidade:

- 🟢 **Iniciante?** Comece por [Arquitetura](./1-arquitetura-de-dados.md), [Modelagem](./2-modelagem-de-dados.md), [Ingestão](./5-ingestao-de-dados.md) e [Armazenamento](./7-armazenamento-de-dados.md).
- 🟡 **Intermediário?** Aprofunde-se em [Processamento](./8-processamento-de-dados.md), [SQL Avançado](./9-sql-avancado.md), [Pipelines](./10-pipelines-de-dados.md), [Orquestração](./11-orquestacao.md) e [Qualidade](./13-qualidade-de-dados.md).
- 🔴 **Avançado?** Explore [DataOps](./12-dataops-e-cicd.md), [Observabilidade](./14-observabilidade.md), [Governança](./15-governanca-e-seguranca.md), [Frameworks](./16-frameworks-e-ecossistema.md), [Técnicas Avançadas](./18-tecnicas-avancadas.md) e [Camada Semântica](./19-camada-semantica-e-analytics-engineering.md).


## Referências e Leituras Recomendadas

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- **Designing Data-Intensive Applications** — Martin Kleppmann (O'Reilly)
- **The Data Engineering Cookbook** — Andreas Kretz
- [DataEngineering.wiki](https://dataengineering.wiki)
- [awesome-data-engineering (GitHub)](https://github.com/igorbarinov/awesome-data-engineering)


*Documentação em construção · Portfólio pessoal*
