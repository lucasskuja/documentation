# Técnicas Avançadas em Engenharia de Dados

> *"O pipeline maduro não é o que roda uma vez; é o que falha, reprocessa, escala e continua confiável."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## Objetivo

Este capítulo reúne técnicas usadas para tornar pipelines mais eficientes, confiáveis, auditáveis e baratos em produção. Elas aparecem em diferentes ferramentas, como Spark, dbt, Kafka, Flink, Airflow, Data Lakes e Data Warehouses cloud.


## Particionamento

Particionamento organiza dados em diretórios, arquivos ou blocos lógicos para reduzir leitura desnecessária.

**Exemplo comum em Data Lake:**

```text
s3://datalake/vendas/silver/ano=2026/mes=07/dia=07/
```

**Boas práticas:**
- Particione por colunas usadas com frequência em filtros.
- Evite partições com cardinalidade alta demais, como `user_id`.
- Evite partições grandes demais ou pequenas demais.
- Monitore pruning: o motor deve ler apenas as partições necessárias.

**Armadilha comum:** particionar por muitas colunas cria milhares de arquivos pequenos e degrada a performance.


## Arquivos Colunares e Compressão

Formatos colunares são padrão para analytics porque leem apenas as colunas necessárias.

| Formato | Uso recomendado |
|---|---|
| Parquet | Padrão mais comum em Data Lakes e Lakehouses |
| ORC | Muito usado no ecossistema Hive/Hadoop |
| Avro | Bom para eventos e schemas evolutivos |
| JSON/CSV | Úteis para troca simples, ruins para analytics em escala |

**Compressões comuns:** Snappy, ZSTD, Gzip.

Para analytics, Parquet com Snappy ou ZSTD costuma oferecer boa relação entre custo, velocidade e compressão.


## Compactação de Pequenos Arquivos

Muitos arquivos pequenos tornam queries lentas porque o motor precisa abrir, listar e planejar milhares de leituras.

**Causas frequentes:**
- Streaming gravando micro-batches pequenos.
- Jobs particionando demais.
- Pipelines incrementais sem manutenção.
- Escrita paralela com excesso de partições.

**Técnicas:**
- Jobs periódicos de compactação.
- `OPTIMIZE` em Delta Lake.
- Rewrite de data files em Iceberg.
- Ajuste do tamanho alvo dos arquivos.
- Separação entre ingestão rápida e camada otimizada para leitura.


## Clustering, Bucketing e Z-Ordering

Essas técnicas organizam fisicamente os dados para acelerar filtros e joins.

| Técnica | Ideia |
|---|---|
| Clustering | Agrupa dados com valores semelhantes próximos fisicamente |
| Bucketing | Distribui registros em buckets por hash de uma chave |
| Z-Ordering | Reorganiza dados para melhorar filtros multidimensionais |

**Quando usar:** tabelas grandes filtradas repetidamente por colunas como `cliente_id`, `produto_id`, `data`, `regiao` ou `tenant_id`.


## Processamento Incremental

Processamento incremental evita recalcular todo o histórico a cada execução.

**Estratégias comuns:**
- Filtro por coluna de atualização, como `updated_at`.
- Watermark persistido em tabela de controle.
- Merge/upsert no destino.
- Reprocessamento de janela móvel, como últimos 3 dias.

**Exemplo de regra:**

```text
Processar registros onde updated_at > ultima_execucao_sucesso
```

**Cuidado:** sistemas de origem podem enviar eventos atrasados. Por isso, muitos pipelines reprocessam uma janela recente para corrigir atrasos.


## Idempotência

Um pipeline idempotente pode rodar mais de uma vez com o mesmo input sem gerar duplicidade ou inconsistência.

**Técnicas:**
- Usar chaves naturais ou surrogate keys estáveis.
- Gravar em tabela temporária e trocar partição no final.
- Usar `MERGE` em vez de `INSERT` cego.
- Sobrescrever partições específicas.
- Registrar execução, input, output e status.

Idempotência é essencial para retries, backfills e recuperação de falhas.


## Backfill e Reprocessamento

Backfill é o reprocessamento de dados históricos, geralmente para corrigir bugs, recalcular métricas ou preencher lacunas.

**Boas práticas:**
- Parametrize pipelines por data ou intervalo.
- Separe lógica de negócio da agenda.
- Execute backfills em janelas controladas.
- Monitore custo e impacto no cluster/warehouse.
- Valide contagens e métricas antes de publicar.

**Risco comum:** reprocessar histórico usando regras atuais sem considerar mudanças de regra ao longo do tempo.


## CDC e Upserts

CDC (Change Data Capture) captura inserções, atualizações e deleções a partir dos sistemas de origem.

**Fluxo típico:**

```text
Banco transacional → Debezium → Kafka → Lakehouse → tabela silver/gold
```

**Cuidados:**
- Ordenação dos eventos por chave.
- Tratamento de deletes.
- Deduplicação por chave e timestamp.
- Evolução de schema.
- Reprocessamento a partir de offsets.

Table formats como Delta Lake, Iceberg e Hudi ajudam a aplicar upserts e deletes no Data Lake.


## SCD Type 1 e Type 2

Slowly Changing Dimensions são técnicas para lidar com mudanças em dimensões analíticas.

| Tipo | Comportamento | Exemplo |
|---|---|---|
| SCD Type 1 | Sobrescreve o valor anterior | Atualizar endereço atual do cliente |
| SCD Type 2 | Preserva histórico com vigência | Manter histórico de endereço por período |

**Campos comuns em SCD Type 2:**
- `valid_from`
- `valid_to`
- `is_current`
- `version`

Use Type 2 quando análises históricas precisam refletir o contexto da época.


## Watermarks e Eventos Atrasados

Em streaming, eventos podem chegar fora de ordem. Watermark define até quando o sistema espera eventos atrasados antes de fechar uma janela.

**Exemplo:**

```text
Calcular vendas por janela de 10 minutos aceitando atraso de até 5 minutos.
```

**Trade-off:** quanto maior a tolerância a atraso, maior o estado mantido e maior o custo operacional.


## Semânticas de Entrega

| Semântica | Significado |
|---|---|
| At-most-once | Pode perder eventos, mas não duplica |
| At-least-once | Não perde eventos, mas pode duplicar |
| Exactly-once | Resultado final sem perda nem duplicidade observável |

Na prática, exactly-once depende da combinação entre origem, processamento, destino, checkpoints, transações e idempotência.


## Data Contracts e Schema Evolution

Data contracts definem expectativas entre produtores e consumidores de dados.

**Contrato pode incluir:**
- Schema e tipos.
- Campos obrigatórios.
- Chaves primárias.
- Regras de validade.
- Frequência de atualização.
- SLA de entrega.
- Dono do dataset.

Schema evolution permite mudanças controladas, como adicionar colunas sem quebrar consumidores. Mudanças destrutivas, como renomear ou remover campos, exigem versionamento e plano de migração.


## Tuning de Spark

**Pontos de atenção:**
- Evitar shuffles desnecessários.
- Usar broadcast join para dimensões pequenas.
- Reparticionar dados antes de escritas grandes.
- Ajustar número de partições.
- Resolver data skew com salting ou estratégias específicas.
- Evitar `collect()` em datasets grandes.
- Usar cache apenas quando o DataFrame será reutilizado.
- Conferir plano físico com `explain()`.

**Sinal de problema:** poucos executores trabalham muito enquanto outros ficam ociosos. Isso geralmente indica skew ou particionamento ruim.


## Observabilidade Operacional

Pipelines maduros emitem sinais claros sobre execução e dados.

**Métricas úteis:**
- Duração do job.
- Linhas lidas e gravadas.
- Tamanho dos dados processados.
- Quantidade de arquivos gerados.
- Freshness do dataset.
- Taxa de erro.
- Custo por execução.

**Alertas úteis:**
- Pipeline falhou.
- Volume caiu ou subiu fora do esperado.
- Dataset atrasado.
- Schema mudou.
- Qualidade caiu abaixo do limite definido.


## Otimização de Custo

Em cloud, performance e custo andam juntos.

**Técnicas:**
- Evitar full scans desnecessários.
- Usar particionamento e pruning.
- Compactar arquivos pequenos.
- Escolher formato colunar.
- Desligar clusters ociosos.
- Preferir autoscaling quando fizer sentido.
- Separar workloads exploratórios e produtivos.
- Definir TTL para dados temporários.
- Monitorar custo por pipeline, domínio e produto de dados.


## Checklist de Produção

- O pipeline é idempotente?
- Existe estratégia de retry?
- Existe estratégia de backfill?
- Há testes de schema e qualidade?
- A tabela está particionada corretamente?
- O número de arquivos é saudável?
- O custo por execução é conhecido?
- Owners e SLAs estão documentados?
- Mudanças de schema são controladas?
- Falhas geram alerta acionável?


## Referências

- [Apache Spark Tuning Guide](https://spark.apache.org/docs/latest/tuning.html)
- [Spark Structured Streaming Programming Guide](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)
- [Delta Lake Optimization](https://docs.delta.io/latest/optimizations-oss.html)
- [Apache Iceberg Maintenance](https://iceberg.apache.org/docs/latest/maintenance/)
- [dbt Incremental Models](https://docs.getdbt.com/docs/build/incremental-models)
- [Debezium Documentation](https://debezium.io/documentation/)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)


← [Comparativo de Clouds](./17-comparativo-clouds.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Camada Semântica →](./19-camada-semantica-e-analytics-engineering.md)


*Documentação em construção · Portfólio pessoal*
