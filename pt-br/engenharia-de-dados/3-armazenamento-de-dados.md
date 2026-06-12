# Armazenamento de Dados

> *"Onde você guarda seus dados define como — e se — você consegue usá-los."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## O que é Armazenamento de Dados?

Armazenamento de dados é a camada responsável por **persistir dados de forma durável, organizada e acessível**. É uma das decisões mais impactantes de uma arquitetura: o tipo de armazenamento escolhido determina performance, custo, escalabilidade e os tipos de consulta que serão possíveis.

Não existe um tipo de armazenamento ideal para todos os casos. A chave é entender o **padrão de acesso** (como e com que frequência os dados serão lidos e escritos) e escolher a tecnologia que melhor atende esse padrão.


## Categorias de Armazenamento

### 🗃️ Bancos de Dados Relacionais (OLTP)

Projetados para **transações operacionais** — sistemas que precisam de consistência, integridade referencial e suporte a muitas operações de leitura e escrita simultâneas.

**Características:**
- Modelo tabular com schema rígido
- Suporte a ACID (Atomicidade, Consistência, Isolamento, Durabilidade)
- Linguagem SQL padrão
- Ótimos para leituras pontuais e escritas frequentes
- Não escalam bem para consultas analíticas sobre grandes volumes

**Quando usar na Engenharia de Dados:** como fonte de dados (sistemas transacionais da empresa), ou como destino de dados processados que precisam ser consultados por aplicações.

**Principais ferramentas:** PostgreSQL, MySQL, SQL Server, Oracle.


### 📊 Data Warehouses (OLAP)

Otimizados para **consultas analíticas** sobre grandes volumes de dados históricos. Ao contrário dos bancos OLTP, priorizam leitura de muitas linhas com agregações em vez de escritas pontuais frequentes.

**Características:**
- Armazenamento colunar (lê apenas as colunas necessárias)
- Otimizado para scans e agregações
- Schema definido previamente (*schema-on-write*)
- Separação de storage e compute (nas versões modernas)
- Escalabilidade elástica

**Quando usar:** analytics, relatórios de BI, KPIs de negócio, dados modelados e curados.

**Principais ferramentas:**

| Ferramenta | Provedor | Destaque |
|------------|----------|----------|
| BigQuery | Google Cloud | Serverless, cobrança por query, escala ilimitada |
| Snowflake | Multi-cloud | Separação total storage/compute, fácil de operar |
| Redshift | AWS | Integrado ao ecossistema AWS, Redshift Spectrum |
| Azure Synapse | Microsoft Azure | Integrado com Power BI e Azure Data Factory |
| ClickHouse | Open source / Cloud | Extrema performance para analytics em tempo real |
| DuckDB | Open source (local) | Analytics local em arquivos, sem servidor |


### 🏞️ Object Storage (Data Lake)

Armazenamento de objetos de **baixo custo e alta escalabilidade**, projetado para guardar qualquer tipo de dado em seu formato bruto. É a base dos Data Lakes modernos.

**Características:**
- Custo muito baixo (centavos por GB/mês)
- Escalabilidade praticamente ilimitada
- Suporte a qualquer formato (CSV, JSON, Parquet, Avro, imagens, vídeos)
- Sem schema obrigatório
- Acesso via API HTTP (não SQL nativo)
- Durabilidade altíssima (99.999999999% — "11 noves")

**Quando usar:** armazenar dados brutos, logs, dados não estruturados, datasets para ML, backups, dados de baixo acesso.

**Principais ferramentas:**

| Ferramenta | Provedor |
|------------|----------|
| Amazon S3 | AWS |
| Google Cloud Storage (GCS) | Google Cloud |
| Azure Data Lake Storage (ADLS) | Microsoft Azure |
| MinIO | Open source (self-hosted) |


### 📁 Formatos de Arquivo

A escolha do formato de arquivo é tão importante quanto a escolha do storage. Formatos inadequados impactam diretamente performance e custo.

#### CSV / TSV
- Simples, legível por humanos, universalmente suportado
- Sem tipagem, sem compressão nativa, lento para analytics
- Use apenas para integração com sistemas legados ou exportações pontuais

#### JSON / JSONL
- Flexível para dados semi-estruturados e aninhados
- Sem compressão nativa, verbose
- JSONL (JSON Lines) é melhor para streaming e ingestão linha a linha

#### Parquet ⭐
- Formato colunar binário, **padrão da indústria para analytics**
- Compressão excelente (tipicamente 5-10x menor que CSV)
- Leitura seletiva de colunas (pushdown de projeção)
- Suporte nativo em Spark, BigQuery, Redshift, Athena, DuckDB
- Ideal para Data Lakes e Lakehouses

#### Avro
- Formato orientado a linha, com schema embutido
- Excelente para **streaming e ingestão** (Kafka usa Avro nativamente com Schema Registry)
- Suporte a evolução de schema

#### ORC
- Similar ao Parquet (colunar), mais comum no ecossistema Hive/Hadoop legado
- Preferido em alguns cenários com Hive e Presto

#### Delta Lake / Iceberg / Hudi
- Não são apenas formatos — são **camadas de tabela transacional** sobre Parquet
- Adicionam suporte a ACID, time travel, schema evolution, upserts e deletes
- Fundamentais para arquiteturas Lakehouse
- Apache Iceberg está emergindo como o padrão aberto dominante


### 🗂️ Bancos de Dados NoSQL

Projetados para casos de uso onde o modelo relacional não se encaixa bem — alta escalabilidade horizontal, dados sem schema fixo, ou padrões de acesso muito específicos.

**Tipos e casos de uso:**

#### Documento (Document Store)
Armazena dados como documentos JSON/BSON. Flexível para dados com estrutura variável.
- **Ferramentas:** MongoDB, Firestore, CouchDB
- **Uso em DE:** fonte de dados de aplicações web/mobile, catálogos de produtos

#### Chave-Valor (Key-Value)
Acesso ultra-rápido por chave. Simples e extremamente performático.
- **Ferramentas:** Redis, DynamoDB, Memcached
- **Uso em DE:** cache de resultados, sessões de usuário, filas simples

#### Colunar (Wide-Column)
Otimizado para escrita e leitura em larga escala com dados esparsos.
- **Ferramentas:** Apache Cassandra, HBase, Google Bigtable
- **Uso em DE:** séries temporais, dados de IoT, logs em altíssimo volume

#### Grafo (Graph)
Modelagem de entidades e relacionamentos como nós e arestas.
- **Ferramentas:** Neo4j, Amazon Neptune
- **Uso em DE:** redes sociais, detecção de fraude, recomendação


### 📈 Bancos de Dados de Séries Temporais (Time Series)

Especializados em dados indexados por tempo — métricas, telemetria, monitoramento.

**Características:** compressão otimizada para dados temporais, funções nativas de janelas de tempo, downsampling automático.

**Ferramentas:** InfluxDB, TimescaleDB (extensão do PostgreSQL), QuestDB, Amazon Timestream.


## Estratégias de Organização no Storage

### Particionamento
Organizar dados em diretórios por atributos comuns (data, região, categoria) para que as queries leiam apenas as partições relevantes.

```
s3://meu-datalake/vendas/
├── ano=2024/
│   ├── mes=01/
│   │   ├── dia=01/
│   │   │   ├── parte-00000.parquet
│   │   │   └── parte-00001.parquet
│   │   └── dia=02/
│   └── mes=02/
└── ano=2025/
```

**Boas práticas de particionamento:**
- Particione por colunas usadas frequentemente em filtros (`WHERE`)
- Evite **over-partitioning** (partições muito pequenas criam overhead de metadados)
- Datas são o particionamento mais comum em dados analíticos

### Compactação de Arquivos Pequenos
Um problema clássico em Data Lakes: muitos arquivos pequenos (o "small files problem") degradam a performance de leitura. Periodicamente, arquivos pequenos devem ser compactados em arquivos maiores. Formatos como Delta Lake e Iceberg têm operações nativas de `OPTIMIZE` / `COMPACTION`.

### Clustering / Z-Ordering
Técnica que organiza fisicamente os dados dentro dos arquivos por colunas específicas, melhorando a eficiência de data skipping (pular blocos irrelevantes durante a leitura).


## Camadas de Acesso: Hot, Warm e Cold

A frequência de acesso aos dados deve guiar onde e como eles são armazenados:

| Camada | Acesso | Custo Storage | Custo Recuperação | Exemplo |
|--------|--------|---------------|-------------------|---------|
| **Hot** | Frequente | Alto | Baixo | Dados dos últimos 30 dias em SSD |
| **Warm** | Ocasional | Médio | Médio | Dados do último ano em HDD/S3 Standard |
| **Cold** | Raro | Muito baixo | Alto / Lento | Dados históricos em S3 Glacier, Coldline |

Políticas de ciclo de vida (*lifecycle policies*) automatizam a movimentação entre camadas.


## Comparativo Rápido

| Tipo | Melhor para | Exemplos |
|------|-------------|----------|
| Relacional (OLTP) | Transações operacionais | PostgreSQL, MySQL |
| Data Warehouse (OLAP) | Analytics e BI | BigQuery, Snowflake |
| Object Storage | Dados brutos, Data Lake | S3, GCS, ADLS |
| Documento | Dados flexíveis de aplicações | MongoDB, Firestore |
| Chave-Valor | Cache e acesso ultrarrápido | Redis, DynamoDB |
| Colunar NoSQL | IoT, logs, alta escrita | Cassandra, Bigtable |
| Séries Temporais | Métricas e telemetria | InfluxDB, TimescaleDB |
| Grafo | Redes e relacionamentos | Neo4j, Neptune |


## Referências

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- **Designing Data-Intensive Applications** — Martin Kleppmann (O'Reilly)
- [Apache Parquet Documentation](https://parquet.apache.org/docs/)
- [Apache Iceberg Documentation](https://iceberg.apache.org/docs/latest/)
- [Delta Lake Documentation](https://docs.delta.io/latest/index.html)


← [Ingestão de Dados](./2-ingestao-de-dados.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Processamento de Dados →](./4-processamento-de-dados.md)


*Documentação em construção · Portfólio pessoal*
