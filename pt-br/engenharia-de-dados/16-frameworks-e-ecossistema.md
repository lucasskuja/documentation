# Frameworks e Ecossistema de Engenharia de Dados

> *"Frameworks não substituem fundamentos; eles empacotam decisões arquiteturais para problemas recorrentes."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## Por que conhecer o ecossistema?

Engenharia de dados moderna combina ferramentas de ingestão, armazenamento, processamento, orquestração, qualidade, governança e infraestrutura. Conhecer os principais frameworks ajuda a escolher a tecnologia certa para o volume, latência, custo, maturidade do time e requisitos de confiabilidade.

Um bom engenheiro de dados não escolhe Spark, Kafka ou Airflow porque são populares; escolhe porque entende o problema que cada ferramenta resolve e as trocas envolvidas.


## Mapa do Ecossistema

| Categoria | Ferramentas comuns | Problema que resolvem |
|---|---|---|
| Processamento distribuído | Apache Spark, PySpark, Hadoop MapReduce | Transformar grandes volumes de dados em cluster |
| Armazenamento distribuído | HDFS, S3, GCS, ADLS | Persistir dados escaláveis e baratos |
| Mensageria e streaming | Apache Kafka, Kafka Connect, Debezium | Capturar eventos e mudanças em tempo quase real |
| Stream processing | Apache Flink, Spark Structured Streaming, Kafka Streams | Processar eventos continuamente com baixa latência |
| Lakehouse e table formats | Delta Lake, Apache Iceberg, Apache Hudi | Adicionar transações, versionamento e evolução de schema ao Data Lake |
| SQL distribuído | Trino, Presto, Hive | Consultar dados distribuídos usando SQL |
| Transformação analytics | dbt | Versionar, testar e documentar transformações SQL |
| Orquestração | Airflow, Dagster, Prefect | Agendar e coordenar workflows |
| Qualidade de dados | Great Expectations, Soda, dbt tests | Validar expectativas e regras sobre os dados |
| Catálogo e lineage | DataHub, OpenLineage, Marquez | Documentar datasets, owners e dependências |
| Infraestrutura | Docker, Kubernetes, Terraform | Empacotar, escalar e provisionar ambientes |

Frameworks e engines resolvem partes específicas do problema. Plataformas como Databricks, Snowflake, BigQuery, Redshift e Microsoft Fabric combinam várias dessas capacidades em um ambiente integrado. Para esse aprofundamento, veja [Plataformas de Dados Modernas](./4-plataformas-de-dados-modernas.md).


## Apache Spark e PySpark

Apache Spark é um motor unificado para processamento distribuído. Ele suporta batch, SQL, streaming, machine learning e grafos. PySpark é a API Python do Spark, muito usada em times que já trabalham com Python.

**Use quando:**
- O volume excede a capacidade confortável de uma única máquina.
- O pipeline precisa combinar arquivos grandes, joins pesados e agregações distribuídas.
- O ambiente já usa Databricks, EMR, Dataproc, Synapse ou Kubernetes.

**Evite quando:**
- O dataset cabe facilmente em DuckDB, Polars, Pandas ou no próprio Data Warehouse.
- A complexidade operacional de cluster não se justifica.
- A transformação é puramente SQL e roda bem com dbt no warehouse.

**Conceitos essenciais:**
- DataFrame API
- Lazy evaluation
- Partitions
- Shuffle
- Broadcast join
- Cache e persist
- Adaptive Query Execution (AQE)
- Spark Structured Streaming

**Exemplo mental:**

```text
Ler Parquet bruto → filtrar registros válidos → agregar por chave → gravar camada gold
```

Spark brilha quando essas etapas precisam ser distribuídas entre muitas máquinas.


## Apache Hadoop

Hadoop foi o ecossistema que popularizou processamento e armazenamento distribuído em larga escala. Seus blocos clássicos são:

| Componente | Função |
|---|---|
| HDFS | Sistema de arquivos distribuído |
| MapReduce | Modelo de processamento distribuído em lote |
| YARN | Gerenciador de recursos do cluster |
| Hive | Camada SQL sobre dados distribuídos |

Hoje, Hadoop puro é menos comum em projetos novos, mas seus conceitos continuam importantes. HDFS, YARN, Hive Metastore, particionamento e arquivos colunares influenciaram diretamente Data Lakes, Spark e plataformas cloud.

**Use quando:** você trabalha em ambientes legados, clusters on-premise ou plataformas que ainda dependem de HDFS/YARN.

**Evite quando:** a empresa já tem object storage cloud e serviços gerenciados mais simples de operar.


## Apache Kafka e Kafka Connect

Kafka é uma plataforma de event streaming usada para publicar, armazenar e consumir eventos com alta vazão.

**Principais usos:**
- Ingestão de eventos de aplicações.
- Integração assíncrona entre sistemas.
- Streaming de logs, cliques, transações e sensores.
- CDC com Debezium.

**Conceitos essenciais:**
- Topic
- Partition
- Producer
- Consumer group
- Offset
- Retention
- Schema Registry

Kafka Connect complementa Kafka com conectores prontos para bancos, arquivos, object storage, Data Warehouses e sistemas SaaS.


## Apache Flink

Flink é um motor de processamento de streams stateful. Diferente de engines centradas em batch, Flink foi desenhado para tratar fluxos contínuos de eventos com estado, janelas, event time e baixa latência.

**Use quando:**
- O caso exige decisões em tempo real.
- Há estado complexo por usuário, conta, sessão ou dispositivo.
- Eventos podem chegar atrasados e precisam de tratamento por event time.

**Exemplos:** detecção de fraude, monitoramento operacional, alertas em tempo real, enriquecimento contínuo de eventos.


## dbt

dbt é uma ferramenta SQL-first para transformação analytics. Ela permite criar modelos SQL versionados, testáveis e documentados, geralmente sobre BigQuery, Snowflake, Redshift, Databricks, PostgreSQL ou DuckDB.

**Use quando:**
- A transformação acontece dentro do Data Warehouse ou Lakehouse.
- O time domina SQL.
- O projeto precisa de lineage, testes e documentação de métricas.

**Evite quando:**
- O processamento exige lógica Python complexa.
- O volume precisa de um job distribuído fora do warehouse.
- A latência é de streaming contínuo.


## Airflow, Dagster e Prefect

Ferramentas de orquestração coordenam a execução dos pipelines. Elas não devem concentrar toda a lógica de transformação; seu papel principal é definir dependências, agenda, retries, parâmetros e observabilidade operacional.

| Ferramenta | Perfil |
|---|---|
| Airflow | Padrão de mercado, maduro, excelente para workflows batch |
| Dagster | Forte em assets, tipagem, testes e visão de plataforma de dados |
| Prefect | Boa experiência de desenvolvimento e execução flexível |


## Delta Lake, Apache Iceberg e Apache Hudi

Table formats adicionam recursos de banco de dados ao Data Lake:

- Transações ACID
- Time travel
- Evolução de schema
- Upserts e deletes
- Compactação
- Controle de snapshots
- Integração com múltiplas engines

| Formato | Pontos fortes |
|---|---|
| Delta Lake | Forte integração com Spark e Databricks |
| Apache Iceberg | Ampla adoção multi-engine e desenho moderno de metadados |
| Apache Hudi | Forte em ingestão incremental, upserts e CDC |

Esses formatos são centrais em arquiteturas Lakehouse.


## Trino, Presto e Hive

Trino e Presto são engines SQL distribuídas usadas para consultar dados em diferentes fontes, como object storage, bancos relacionais e catálogos Hive/Iceberg/Delta.

**Use quando:**
- Você precisa consultar dados sem mover tudo para um único warehouse.
- O caso é exploração SQL, federação ou camada semântica.
- Os dados estão em Parquet/ORC no Data Lake.

Hive continua importante pelo legado e pelo Hive Metastore, ainda usado como catálogo em muitos ambientes.


## Ferramentas de Ingestão

| Ferramenta | Uso comum |
|---|---|
| Airbyte | Conectores EL open source para APIs, bancos e SaaS |
| Fivetran | Ingestão gerenciada com baixo esforço operacional |
| Apache NiFi | Fluxos visuais, roteamento e transformação leve de dados |
| Debezium | CDC a partir de logs de bancos relacionais |
| Kafka Connect | Conectores de origem e destino integrados ao Kafka |


## Como Escolher

| Pergunta | Tendência de escolha |
|---|---|
| Cabe em uma máquina? | DuckDB, Polars ou Pandas |
| É transformação SQL no warehouse? | dbt |
| É grande volume batch distribuído? | Spark/PySpark |
| É streaming com estado complexo? | Flink |
| É evento ou CDC em larga escala? | Kafka + Debezium |
| É Data Lake com transações? | Delta Lake, Iceberg ou Hudi |
| É coordenação de jobs? | Airflow, Dagster ou Prefect |
| É consulta federada? | Trino/Presto |


## Boas Práticas

- Escolha ferramentas pelo problema, não pela popularidade.
- Prefira serviços gerenciados quando operação de cluster não for diferencial competitivo.
- Mantenha transformação pesada fora do orquestrador.
- Use formatos colunares como Parquet ou ORC para analytics.
- Padronize nomes, partições, camadas e contratos de dados.
- Monitore custo, tempo de execução, volume processado e falhas.
- Documente owners, SLAs, linhagem e consumidores de cada dataset relevante.


## Referências

- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [Apache Hadoop Documentation](https://hadoop.apache.org/docs/current/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Apache Flink Documentation](https://nightlies.apache.org/flink/flink-docs-stable/)
- [dbt Documentation](https://docs.getdbt.com/)
- [Apache Airflow Documentation](https://airflow.apache.org/docs/apache-airflow/stable/)
- [Delta Lake Documentation](https://docs.delta.io/)
- [Apache Iceberg Documentation](https://iceberg.apache.org/docs/latest/)
- [Apache Hudi Documentation](https://hudi.apache.org/docs/overview/)
- [Trino Documentation](https://trino.io/docs/current/)


← [Governança e Segurança](./15-governanca-e-seguranca.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Comparativo de Clouds →](./17-comparativo-clouds.md)


*Documentação em construção · Portfólio pessoal*
