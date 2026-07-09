# Processamento de Dados

> *"Dado bruto é matéria-prima. Processamento é o que transforma minério em aço."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## O que é Processamento de Dados?

Processamento de dados é o conjunto de operações que **transformam dados brutos em informação útil e confiável**. É nessa etapa que os dados são limpos, enriquecidos, agregados, modelados e preparados para consumo — seja por analistas, dashboards, modelos de ML ou aplicações.

O processamento responde às perguntas:
- Como remover registros duplicados ou inválidos?
- Como combinar dados de múltiplas fontes?
- Como calcular métricas e agregações?
- Como garantir que os dados estejam no formato correto para o destino?


## ETL vs ELT

Dois paradigmas dominam a forma como o processamento se encaixa no fluxo de dados:

### ETL — Extract, Transform, Load
O modelo tradicional: os dados são **transformados antes de serem carregados** no destino. A transformação acontece em uma ferramenta intermediária ou servidor dedicado.

``` mermaid
flowchart LR

    SRC["Fonte"]
    EXT["Extração"]
    TRF["Transformação"]
    LOAD["Carga"]
    DW["Data Warehouse"]

    SRC --> EXT --> TRF --> LOAD --> DW

    %% Styles
    classDef source fill:#6c757d,stroke:#343a40,color:#ffffff,stroke-width:2px;
    classDef extract fill:#17a2b8,stroke:#0f4c5c,color:#ffffff,stroke-width:2px;
    classDef transform fill:#9467bd,stroke:#5a3e85,color:#ffffff,stroke-width:2px;
    classDef load fill:#ff7f0e,stroke:#b45309,color:#ffffff,stroke-width:2px;
    classDef warehouse fill:#1f77b4,stroke:#0d3b66,color:#ffffff,stroke-width:2px;

    class SRC source;
    class EXT extract;
    class TRF transform;
    class LOAD load;
    class DW warehouse;
```

**Vantagens:** controle total da transformação, dados já chegam limpos ao destino.

**Desvantagens:** pipeline mais rígido, difícil de re-executar, menor flexibilidade para exploração.

**Quando era comum:** ambientes on-premise com Data Warehouses caros onde carregar dados brutos era inviável.


### ELT — Extract, Load, Transform
O modelo moderno: os dados são carregados **primeiro no destino em sua forma bruta**, e a transformação acontece dentro do próprio Data Warehouse ou Data Lake, aproveitando seu poder de processamento.

``` mermaid
flowchart LR

    SRC["Fonte"]
    EXT["Extração"]
    LOAD["Carga"]
    DW["Data Warehouse"]
    TRF["Transformação"]

    SRC --> EXT --> LOAD --> DW --> TRF

    %% Styles
    classDef source fill:#6c757d,stroke:#343a40,color:#ffffff,stroke-width:2px;
    classDef extract fill:#17a2b8,stroke:#0f4c5c,color:#ffffff,stroke-width:2px;
    classDef load fill:#ff7f0e,stroke:#b45309,color:#ffffff,stroke-width:2px;
    classDef warehouse fill:#1f77b4,stroke:#0d3b66,color:#ffffff,stroke-width:2px;
    classDef transform fill:#9467bd,stroke:#5a3e85,color:#ffffff,stroke-width:2px;

    class SRC source;
    class EXT extract;
    class LOAD load;
    class DW warehouse;
    class TRF transform;
```

**Vantagens:** maior flexibilidade, dados brutos preservados, transformações podem ser versionadas e re-executadas facilmente, aproveita a escalabilidade do cloud.

**Desvantagens:** requer um destino com poder computacional suficiente, dados brutos ficam expostos no ambiente.

**Por que ELT domina hoje:** BigQuery, Snowflake e Redshift têm poder computacional elástico e barato. Não faz mais sentido transformar antes de carregar quando o destino é mais poderoso que qualquer servidor intermediário.

## Tipos de Transformações

### Limpeza de Dados
- Remoção de duplicatas
- Tratamento de valores nulos (preenchimento, descarte, flag)
- Padronização de formatos (datas, strings, telefones, CPFs)
- Remoção de caracteres especiais e encoding incorreto
- Validação de tipos de dados

### Enriquecimento
- Join com tabelas de referência (ex: adicionar nome do produto a partir do ID)
- Lookup de dados externos (ex: coordenadas geográficas a partir de CEP)
- Cálculo de campos derivados (ex: idade a partir da data de nascimento)

### Agregação
- Sumarizações (SUM, COUNT, AVG, MIN, MAX)
- Janelas de tempo (vendas por mês, usuários ativos por semana)
- Métricas de negócio (LTV, churn rate, conversion rate)

### Filtragem e Projeção
- Remoção de registros irrelevantes para o caso de uso
- Seleção de apenas as colunas necessárias

### Joins e Combinações
- Combinar dados de múltiplas fontes em uma visão unificada
- Detectar correspondências entre registros de sistemas diferentes

### Pivotamento e Unpivotamento
- Transformar linhas em colunas (pivot) ou colunas em linhas (unpivot)
- Normalizar estruturas aninhadas (explode de arrays em JSON)


## Processamento Batch vs Streaming

### Processamento Batch
Processa um **conjunto fixo de dados** de uma só vez. A query ou job tem início e fim definidos.

**Características:**
- Processa grandes volumes com eficiência
- Latência mais alta (resultado disponível ao final do job)
- Mais simples de implementar e depurar
- Re-execução e backfill mais fáceis

**Casos de uso:** transformações noturnas, cálculo de métricas diárias, geração de relatórios, treinamento de modelos ML.

### Processamento de Streaming
Processa dados **continuamente**, à medida que chegam, evento por evento ou em micro-batches.

**Características:**
- Baixa latência (resultados em segundos ou menos)
- Maior complexidade: gerenciamento de estado, watermarks, exactly-once
- Requer infraestrutura dedicada e sempre ativa
- Útil para casos onde a demora tem custo real de negócio

**Casos de uso:** detecção de fraude, alertas em tempo real, atualização de dashboards ao vivo, personalização imediata.


## Conceitos de Processamento Distribuído

Para grandes volumes de dados, o processamento em uma única máquina é inviável. O processamento distribuído divide o trabalho entre múltiplas máquinas em paralelo.

### Particionamento de Dados
Os dados são divididos em partições, cada uma processada por um nó diferente do cluster. A escolha da chave de particionamento impacta diretamente a performance.

### Shuffle
Operação de redistribuição de dados entre nós do cluster, necessária para joins e aggregations que precisam reunir dados relacionados. É a operação mais cara em termos de I/O e rede — deve ser minimizada.

### Map e Reduce
Paradigma clássico de processamento distribuído:
- **Map:** aplica uma transformação em cada registro de forma paralela
- **Reduce:** agrega os resultados de todos os nós em um resultado final

### Data Skew
Desequilíbrio na distribuição dos dados entre partições. Se uma partição tem muito mais dados que as outras, um nó fica sobrecarregado enquanto os demais ficam ociosos, degradando a performance.


## Ferramentas de Processamento

### 🔥 Apache Spark
O motor de processamento distribuído mais popular. Suporta batch, streaming (Structured Streaming), SQL, ML (MLlib) e grafos (GraphX) em uma única API unificada.

**Características:**
- Processamento in-memory (muito mais rápido que MapReduce)
- APIs em Python (PySpark), Scala, Java e R
- Integração nativa com Delta Lake, Iceberg, S3, HDFS, Kafka
- Disponível em plataformas gerenciadas: Databricks, EMR, Dataproc

**Quando usar:** processamento de grandes volumes, transformações complexas, pipelines de ML, ambientes que já usam o ecossistema Spark.

```python
# Exemplo PySpark
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, sum, to_date

spark = SparkSession.builder.appName("vendas").getOrCreate()

df = spark.read.parquet("s3://datalake/vendas/raw/")

resultado = (
    df
    .filter(col("status") == "completed")
    .withColumn("data", to_date(col("created_at")))
    .groupBy("data", "produto_id")
    .agg(sum("valor").alias("receita_total"))
)

resultado.write.parquet("s3://datalake/vendas/gold/receita_por_dia/")
```


### 🔧 dbt (data build tool)
Ferramenta de transformação SQL-first que traz práticas de engenharia de software para o mundo das transformações de dados: versionamento, testes, documentação e modularidade.

**Características:**
- Transformações em SQL puro (com Jinja para templating)
- Modelos como arquivos `.sql` versionados no Git
- Testes nativos (not null, unique, accepted values, relationships)
- Geração automática de documentação e lineage
- Roda dentro do Data Warehouse (BigQuery, Snowflake, Redshift, DuckDB)

**Quando usar:** transformações analíticas dentro do Data Warehouse (ELT), times que preferem SQL a Python, projetos que precisam de documentação e governança de transformações.

```sql
-- models/silver/vendas_limpas.sql
{{ config(materialized='incremental', unique_key='venda_id') }}

SELECT
    venda_id,
    cliente_id,
    produto_id,
    CAST(valor AS NUMERIC) AS valor,
    DATE(created_at) AS data_venda
FROM {{ source('raw', 'vendas') }}
WHERE status = 'completed'
  AND valor > 0
```


### 🐟 Apache Flink
Motor de processamento de streams stateful, de alta performance. Referência para casos de uso de streaming complexo.

**Quando usar:** streaming com estado complexo, processamento de eventos em tempo real, casos onde a latência importa muito.


### 🦆 DuckDB
Banco de dados OLAP embarcado (sem servidor). Processa arquivos Parquet, CSV e JSON diretamente com SQL de alta performance, rodando localmente ou em notebooks.

**Quando usar:** análises locais, prototipagem, pipelines leves sem infraestrutura, substituição do Pandas para datasets médios.

```python
import duckdb

resultado = duckdb.sql("""
    SELECT
        DATE_TRUNC('month', data_venda) AS mes,
        SUM(valor) AS receita
    FROM read_parquet('s3://datalake/vendas/*.parquet')
    GROUP BY 1
    ORDER BY 1
""").df()
```


### 🐼 Pandas
Biblioteca Python para manipulação de dados em memória. Amplamente usada para datasets pequenos a médios, prototipagem e pipelines locais.

**Limitação:** não escala para grandes volumes (tudo em memória de uma máquina). Para volumes maiores, considere **Polars** (alternativa mais rápida e moderna) ou **Dask** (Pandas distribuído).


### Trino / Presto
Motor de query SQL distribuído que permite consultar dados em múltiplas fontes heterogêneas (S3, bancos relacionais, NoSQL) com uma única query SQL. Ideal para federação de dados.


## Boas Práticas de Processamento

**Idempotência:** jobs devem poder ser re-executados sem criar duplicatas ou estados inconsistentes.

**Incrementalidade:** processe apenas os dados novos quando possível, evitando reprocessar tudo.

**Testes:** valide o resultado das transformações com testes automatizados (contagens, ranges esperados, integridade referencial).

**Logging e observabilidade:** registre tempo de execução, volume processado e erros para facilitar depuração.

**Separação de responsabilidades:** mantenha transformações simples e compostas em módulos menores e reutilizáveis.

**Evite lógica de negócio duplicada:** centralize definições de métricas para evitar inconsistências entre pipelines.


## Referências

- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [dbt Documentation](https://docs.getdbt.com/)
- [DuckDB Documentation](https://duckdb.org/docs/)
- [Apache Flink Documentation](https://nightlies.apache.org/flink/flink-docs-stable/)


← [Armazenamento de Dados](./7-armazenamento-de-dados.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [SQL Avançado →](./9-sql-avancado.md)


*Documentação em construção · Portfólio pessoal*
