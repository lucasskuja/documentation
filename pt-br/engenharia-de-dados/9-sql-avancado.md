# SQL Avançado para Engenharia de Dados

> *"SQL é a linguagem franca dos dados; dominar SQL avançado é dominar boa parte do trabalho analítico e operacional."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## Por que SQL Avançado importa?

SQL aparece em Data Warehouses, Lakehouses, engines distribuídas, dbt, ferramentas de BI, pipelines ELT e validações de qualidade. Mesmo quando o processamento usa Spark ou Python, SQL continua sendo a forma mais comum de expressar transformações analíticas.

SQL avançado ajuda a:

- Deduplicar dados.
- Criar modelos incrementais.
- Calcular métricas com janelas.
- Investigar anomalias.
- Otimizar custos.
- Validar qualidade.
- Construir marts e camadas Gold.


## CTEs

CTEs (`WITH`) ajudam a quebrar queries complexas em etapas legíveis.

```sql
WITH vendas_validas AS (
    SELECT *
    FROM raw.vendas
    WHERE status = 'completed'
),
vendas_por_cliente AS (
    SELECT
        cliente_id,
        SUM(valor) AS receita_total
    FROM vendas_validas
    GROUP BY cliente_id
)
SELECT *
FROM vendas_por_cliente
WHERE receita_total > 1000;
```

**Boas práticas:**

- Nomeie CTEs pelo papel que exercem.
- Evite CTEs enormes com muitas responsabilidades.
- Use CTEs para documentar o raciocínio da transformação.
- Verifique se a engine materializa ou otimiza CTEs automaticamente.


## Window Functions

Window functions calculam valores considerando uma janela de linhas sem colapsar o resultado como `GROUP BY`.

```sql
SELECT
    cliente_id,
    pedido_id,
    data_pedido,
    valor,
    SUM(valor) OVER (
        PARTITION BY cliente_id
        ORDER BY data_pedido
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS receita_acumulada
FROM analytics.pedidos;
```

**Funções úteis:**

| Função | Uso |
|---|---|
| `ROW_NUMBER()` | Deduplicação e ranking determinístico |
| `RANK()` / `DENSE_RANK()` | Ranking com empates |
| `LAG()` / `LEAD()` | Comparar linha atual com anterior/próxima |
| `SUM() OVER` | Acumulados |
| `AVG() OVER` | Médias móveis |
| `FIRST_VALUE()` | Primeiro valor da janela |
| `LAST_VALUE()` | Último valor da janela |


## Deduplicação

Deduplicação exige uma regra clara de sobrevivência.

```sql
WITH ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY pedido_id
            ORDER BY updated_at DESC, ingestion_at DESC
        ) AS rn
    FROM raw.pedidos
)
SELECT *
FROM ranked
WHERE rn = 1;
```

**Perguntas importantes:**

- Qual chave define duplicidade?
- Qual registro deve vencer?
- O timestamp é confiável?
- Empates são possíveis?
- Deletes precisam ser considerados?


## Joins Avançados

| Tipo | Uso |
|---|---|
| `INNER JOIN` | Apenas registros com correspondência |
| `LEFT JOIN` | Preserva todos os registros da esquerda |
| `FULL OUTER JOIN` | Compara conjuntos completos |
| `SEMI JOIN` | Retorna registros que possuem correspondência |
| `ANTI JOIN` | Retorna registros sem correspondência |
| `CROSS JOIN` | Combina todos com todos; use com cuidado |

**Anti-join comum:**

```sql
SELECT c.*
FROM dim_cliente c
LEFT JOIN fato_venda v
    ON c.cliente_id = v.cliente_id
WHERE v.cliente_id IS NULL;
```

Esse padrão encontra clientes sem vendas.


## Modelos Incrementais

Queries incrementais processam apenas dados novos ou alterados.

```sql
SELECT *
FROM raw.eventos
WHERE updated_at >= (
    SELECT COALESCE(MAX(updated_at), TIMESTAMP '1900-01-01')
    FROM analytics.eventos_processados
);
```

**Cuidados:**

- Use janela de segurança para eventos atrasados.
- Combine incremental com `MERGE` quando houver updates.
- Controle deletes quando a origem suporta CDC.
- Monitore lacunas entre execuções.


## MERGE e Upsert

`MERGE` combina insert, update e delete em uma operação lógica.

```sql
MERGE INTO silver.clientes AS target
USING staging.clientes AS source
ON target.cliente_id = source.cliente_id
WHEN MATCHED THEN UPDATE SET
    nome = source.nome,
    email = source.email,
    updated_at = source.updated_at
WHEN NOT MATCHED THEN INSERT (
    cliente_id, nome, email, updated_at
) VALUES (
    source.cliente_id, source.nome, source.email, source.updated_at
);
```

**Use quando:** registros existentes podem mudar e o destino precisa refletir o estado mais recente.


## Agregações e Granularidade

Toda agregação precisa respeitar o grão do dado.

```sql
SELECT
    DATE_TRUNC('month', data_venda) AS mes,
    produto_id,
    SUM(valor_total) AS receita
FROM fato_venda
GROUP BY 1, 2;
```

**Erro comum:** misturar métricas em granularidades diferentes, como receita por pedido e quantidade de clientes distintos por mês, sem deixar claro o nível de agregação.


## Performance

Boas queries reduzem dados lidos, shuffles e joins desnecessários.

**Técnicas:**

- Filtrar cedo.
- Selecionar apenas colunas necessárias.
- Usar particionamento e clustering.
- Evitar funções em colunas de filtro quando isso impede pruning.
- Pré-agregar antes de joins grandes.
- Evitar `SELECT *` em tabelas amplas.
- Usar materialized views quando a engine suporta.
- Analisar plano de execução.


## EXPLAIN e Plano de Execução

`EXPLAIN` mostra como a engine pretende executar a query.

**Procure por:**

- Full scans inesperados.
- Joins muito caros.
- Filtros aplicados tarde.
- Shuffle/distribution excessivo.
- Falta de pruning em partições.
- Estimativas de linhas muito diferentes do real.


## Anti-patterns

- `SELECT *` em pipelines produtivos.
- Filtros de data sem usar a coluna particionada.
- Joins sem chave clara.
- Deduplicação sem ordenação determinística.
- CTEs gigantes difíceis de revisar.
- Recalcular histórico completo sem necessidade.
- Métricas duplicadas em várias queries.
- Casting silencioso de tipos incompatíveis.
- Usar `DISTINCT` para esconder problema de modelagem.


## Checklist

- A query deixa claro o grão?
- Os filtros usam partições quando possível?
- Os joins têm cardinalidade esperada?
- Há regra determinística de deduplicação?
- O custo de scan é aceitável?
- A query pode ser incremental?
- Métricas têm definição centralizada?
- O plano de execução foi analisado?
- A query tem testes de qualidade?


## Referências

- [PostgreSQL Documentation - Queries](https://www.postgresql.org/docs/current/queries.html)
- [BigQuery Query Syntax](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax)
- [Snowflake SQL Reference](https://docs.snowflake.com/en/sql-reference)
- [Databricks SQL Language Reference](https://docs.databricks.com/sql/language-manual/)
- [dbt SQL Models](https://docs.getdbt.com/docs/build/sql-models)


← [Processamento de Dados](./8-processamento-de-dados.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Pipelines de Dados →](./10-pipelines-de-dados.md)


*Documentação em construção · Portfólio pessoal*

