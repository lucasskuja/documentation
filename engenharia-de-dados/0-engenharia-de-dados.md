# Engenharia de Dados

> *"Dados são o novo petróleo — mas petróleo bruto precisa ser refinado para ter valor."*


## O que é Engenharia de Dados?

Engenharia de Dados é a disciplina responsável por **projetar, construir e manter a infraestrutura e os sistemas** que permitem a coleta, armazenamento, transformação e disponibilização de dados de forma confiável, escalável e eficiente.

Enquanto cientistas de dados analisam e extraem insights dos dados, são os engenheiros de dados que garantem que esses dados existam, estejam acessíveis e com qualidade suficiente para serem usados. Em outras palavras: **o engenheiro de dados constrói os canos; outros bebem a água**.

O papel evoluiu a partir da engenharia de software e da administração de bancos de dados, mas ganhou identidade própria com o crescimento exponencial do volume de dados e a necessidade de pipelines robustos em tempo real ou em lote (*batch*).


## Principais Conceitos

A seguir, uma visão panorâmica dos pilares que estruturam a Engenharia de Dados. Cada tópico possui sua própria documentação aprofundada.


### 🏗️ [Arquitetura de Dados](./1-arquitetura-de-dados.md)

Define como os componentes de um sistema de dados se organizam e se comunicam. Inclui decisões sobre onde os dados são armazenados, como trafegam entre sistemas e quais padrões arquiteturais são adotados.

Conceitos-chave: 
- Data Warehouse
- Data Lake
- Data Lakehouse
- Lambda Architecture
- Kappa Architecture
- Medallion Architecture


### 📥 [Ingestão de Dados](./2-ingestao-de-dados.md)

É o processo de **capturar e importar dados** de diversas fontes (bancos de dados, APIs, arquivos, streams, etc.) para o ambiente de dados centralizado.

Pode ocorrer de duas formas principais:
- **Batch (em lote):** dados coletados em intervalos periódicos.
- **Streaming (tempo real):** dados capturados e processados continuamente.

Ferramentas comuns: Apache Kafka, AWS Kinesis, Airbyte, Fivetran, NiFi.


### 🗄️ [Armazenamento de Dados](./3-armazenamento-de-dados.md)

Abrange as tecnologias e estratégias para **persistir dados** de forma eficiente, considerando custo, performance e padrão de acesso.

Principais categorias:
- Bancos relacionais (OLTP)
- Data Warehouses (OLAP)
- Data Lakes (armazenamento bruto em object storage)
- Bancos NoSQL (documentos, colunar, grafos, chave-valor)

Ferramentas comuns: BigQuery, Snowflake, Redshift, Delta Lake, S3, PostgreSQL.


### ⚙️ [Processamento de Dados](./4-processamento-de-dados.md)

Envolve **transformar dados brutos em dados úteis**, aplicando limpeza, agregações, enriquecimento e regras de negócio. É nessa etapa que os dados ganham significado.

Paradigmas: processamento distribuído, in-memory, batch vs. streaming.

Ferramentas comuns: Apache Spark, dbt, Flink, Trino, Pandas.


### 🔁 [Pipelines de Dados](./5-pipelines-de-dados.md)

Um pipeline de dados é o **conjunto de etapas automatizadas** que movem e transformam dados desde a fonte até o destino final (dashboards, modelos de ML, APIs, etc.).

Um pipeline típico segue o padrão:

```
Fonte → Ingestão → Armazenamento → Transformação → Destino
```

Boas práticas incluem: idempotência, rastreabilidade, tratamento de erros e reprocessamento.


### 🎼 [Orquestração](./6-orquestacao.md)

Orquestração é o **gerenciamento e agendamento da execução dos pipelines**, garantindo que as tarefas rodem na ordem correta, no momento certo, e com tratamento adequado de falhas.

Conceitos-chave: DAGs (Grafos Acíclicos Dirigidos), dependências entre tarefas, retries, alertas.

Ferramentas comuns: Apache Airflow, Prefect, Dagster, Mage.


### ✅ [Qualidade de Dados](./7-qualidade-de-dados.md)

Garante que os dados sejam **precisos, completos, consistentes e confiáveis**. Sem qualidade, qualquer análise ou modelo é comprometido.

Dimensões de qualidade: completude, unicidade, validade, consistência, atualidade, acurácia.

Ferramentas comuns: Great Expectations, Soda, dbt tests, Monte Carlo.


### 🔍 [Observabilidade e Monitoramento](./8-observabilidade.md)

Envolve **monitorar a saúde dos pipelines e dos dados** em produção, detectando anomalias, falhas e desvios antes que impactem os consumidores.

Inclui: logs, métricas, alertas, lineage (rastreabilidade da origem dos dados) e catálogo de dados.

Ferramentas comuns: DataHub, OpenLineage, Monte Carlo, Grafana.


### 🔐 [Governança e Segurança](./9-governanca-e-seguranca.md)

Define **políticas, papéis e controles** para garantir que os dados sejam usados de forma ética, segura e em conformidade com regulações (LGPD, GDPR).

Abrange: controle de acesso, criptografia, mascaramento de dados, catalogação e políticas de retenção.


### ☁️ [Cloud e Infraestrutura](./10-cloud-e-infraestrutura.md)

A maioria dos sistemas de dados modernos roda em nuvem. Compreender os serviços e modelos de infraestrutura é essencial para o engenheiro de dados.

Provedores principais: AWS, Google Cloud (GCP), Microsoft Azure.

Conceitos-chave: IaC (Infrastructure as Code), containers, serverless, escalabilidade horizontal.

### 🌐 [Comparativo de Clouds para Engenharia de Dados](./11-comparativo-clouds.md)

Comparação dos principais provedores de nuvem (AWS, GCP, Azure) em relação aos serviços de dados que oferecem, custos, facilidade de uso e casos de uso recomendados.


## Como usar esta documentação

Cada seção acima é um ponto de entrada para um documento dedicado com maior profundidade. A ideia é que você navegue pelos tópicos conforme sua necessidade:

- 🟢 **Iniciante?** Comece por [Arquitetura](./1-arquitetura-de-dados.md) e [Pipelines](./pipelines-de-dados.md).
- 🟡 **Intermediário?** Aprofunde-se em [Orquestração](./orquestacao.md) e [Qualidade](./qualidade-de-dados.md).
- 🔴 **Avançado?** Explore [Observabilidade](./observabilidade.md) e [Governança](./governanca-e-seguranca.md).


## Referências e Leituras Recomendadas

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- **Designing Data-Intensive Applications** — Martin Kleppmann (O'Reilly)
- **The Data Engineering Cookbook** — Andreas Kretz
- [DataEngineering.wiki](https://dataengineering.wiki)
- [awesome-data-engineering (GitHub)](https://github.com/igorbarinov/awesome-data-engineering)


*Documentação em construção · Portfólio pessoal*