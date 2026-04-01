# Qualidade de Dados

> *"Um dashboard bonito construído sobre dados ruins é apenas uma mentira bem apresentada."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## O que é Qualidade de Dados?

Qualidade de dados é o grau em que os dados são **adequados para o uso pretendido**. Dados de alta qualidade são precisos, completos, consistentes e confiáveis o suficiente para suportar decisões de negócio, alimentar modelos de ML e produzir relatórios corretos.

Qualidade não é um estado binário (bom ou ruim) — é uma característica multidimensional que deve ser continuamente monitorada e gerenciada ao longo de todo o ciclo de vida dos dados.

O custo de dados de má qualidade é alto: decisões erradas, modelos tendenciosos, relatórios inconsistentes, perda de confiança nas equipes de dados e, em casos extremos, danos financeiros e regulatórios.


## As 6 Dimensões de Qualidade

### 1. Completude
Os dados contêm todas as informações necessárias? Campos obrigatórios estão preenchidos?

**Exemplos de problemas:** campo `email` nulo em 30% dos registros, pedidos sem produto associado, transações sem valor.

**Como medir:** percentual de registros com campos críticos não nulos.


### 2. Unicidade
Cada entidade do mundo real aparece **uma única vez** nos dados? Não há duplicatas indevidas?

**Exemplos de problemas:** cliente cadastrado duas vezes com IDs diferentes, mesmo pedido processado duas vezes por falha de ingestão.

**Como medir:** contagem de registros com chave primária duplicada; razão entre registros totais e registros únicos por chave de negócio.


### 3. Validade
Os dados seguem as **regras de formato e domínio esperadas**?

**Exemplos de problemas:** data de nascimento no futuro, CPF com formato incorreto, valor de desconto maior que o valor do pedido, status com valor fora do conjunto esperado.

**Como medir:** percentual de registros que violam regras de negócio definidas.


### 4. Consistência
Os dados são **coerentes entre si** e entre diferentes sistemas?

**Exemplos de problemas:** total de vendas no CRM diferente do total no ERP para o mesmo período, produto marcado como "ativo" no catálogo mas com estoque zerado no sistema de inventário.

**Como medir:** cruzamento de métricas entre fontes diferentes; verificação de integridade referencial entre tabelas.


### 5. Atualidade (Timeliness / Freshness)
Os dados estão **atualizados** de acordo com o que o caso de uso exige?

**Exemplos de problemas:** dashboard exibindo dados de ontem quando deveria exibir dados de hoje, pipeline de ingestão com atraso de 6 horas sem detecção.

**Como medir:** tempo desde a última atualização (`max(updated_at)` vs. horário atual); comparação com SLA definido.


### 6. Acurácia
Os dados **refletem corretamente a realidade** que pretendem representar?

**Exemplos de problemas:** endereço de cliente desatualizado após mudança, preço de produto incorreto por erro de digitação, metadados de arquivo com coordenadas GPS erradas.

**Como medir:** amostragem manual, reconciliação com sistemas de origem, cruzamento com fontes externas confiáveis.


## Estratégias de Garantia de Qualidade

### Validação na Ingestão (Shift Left)
A abordagem mais eficiente é detectar problemas **o mais cedo possível** no pipeline — idealmente na ingestão, antes que dados ruins se propaguem para camadas downstream.

**Técnicas:**
- Schema validation ao chegarem os dados
- Rejeitar registros inválidos com dead letter queue
- Alertas imediatos quando volume esperado não chega

### Testes em Transformações (dbt Tests)
O dbt oferece testes nativos que rodam após cada transformação:

```yaml
# schema.yml
models:
  - name: stg_orders
    columns:
      - name: order_id
        tests:
          - not_null
          - unique
      - name: status
        tests:
          - accepted_values:
              values: ['pending', 'completed', 'cancelled', 'refunded']
      - name: customer_id
        tests:
          - not_null
          - relationships:
              to: ref('stg_customers')
              field: customer_id
      - name: amount
        tests:
          - not_null
          - dbt_utils.expression_is_true:
              expression: ">= 0"
```

**Tipos de teste dbt:**
- `not_null`: coluna não pode ter valores nulos
- `unique`: todos os valores devem ser únicos
- `accepted_values`: valores devem pertencer a um conjunto definido
- `relationships`: integridade referencial entre tabelas


### Great Expectations
Framework Python de código aberto para definir, documentar e validar **expectativas** sobre os dados.

```python
import great_expectations as gx

context = gx.get_context()

# Define expectativas
suite = context.add_expectation_suite("vendas_suite")

validator = context.get_validator(
    batch_request=batch_request,
    expectation_suite_name="vendas_suite"
)

validator.expect_column_values_to_not_be_null("order_id")
validator.expect_column_values_to_be_unique("order_id")
validator.expect_column_values_to_be_between("amount", min_value=0, max_value=1_000_000)
validator.expect_column_values_to_be_in_set("status", ["pending", "completed", "cancelled"])
validator.expect_table_row_count_to_be_between(min_value=1000, max_value=10_000_000)

# Salva as expectativas
validator.save_expectation_suite()

# Valida e gera relatório
checkpoint = context.add_checkpoint(name="vendas_checkpoint", ...)
results = checkpoint.run()
```


### Soda
Plataforma de qualidade de dados com DSL própria (SodaCL) para definir checks em YAML, mais acessível para times não-Python.

```yaml
# soda_checks.yml
checks for orders:
  - row_count > 0
  - missing_count(order_id) = 0
  - duplicate_count(order_id) = 0
  - invalid_count(status) = 0:
      valid values: [pending, completed, cancelled]
  - avg(amount) between 50 and 500
  - freshness(created_at) < 25h
```


## Monitoramento Contínuo de Qualidade

Testes pontuais não são suficientes. Dados que estavam corretos ontem podem estar errados hoje. O monitoramento contínuo detecta **anomalias e regressões ao longo do tempo**.

### Monitoramento de Volume
Detecta quando o número de registros de um pipeline está fora do padrão esperado.

```sql
-- Alerta se o volume de hoje estiver 20% abaixo da média dos últimos 7 dias
SELECT
    COUNT(*) AS volume_hoje,
    AVG(volume) OVER (ORDER BY data ROWS BETWEEN 7 PRECEDING AND 1 PRECEDING) AS media_7d
FROM daily_volumes
WHERE ABS(volume_hoje - media_7d) / media_7d > 0.20
```

### Monitoramento de Freshness
Detecta quando os dados não foram atualizados dentro do prazo esperado.

```sql
-- Alerta se dados não foram atualizados nas últimas 25 horas
SELECT
    MAX(updated_at) AS ultima_atualizacao,
    NOW() - MAX(updated_at) AS tempo_desde_update
FROM fct_vendas
HAVING NOW() - MAX(updated_at) > INTERVAL '25 hours'
```

### Detecção de Anomalias Estatísticas
Ferramentas como Monte Carlo e Bigeye monitoram automaticamente distribuições, valores nulos, cardinalidade e outros indicadores, alertando quando há desvios estatisticamente significativos sem necessidade de definir thresholds manualmente.


## Ferramentas de Qualidade de Dados

| Ferramenta | Tipo | Destaque |
|------------|------|----------|
| **dbt tests** | Integrado ao dbt | Testes SQL declarativos em YAML |
| **Great Expectations** | Open source Python | Framework completo e flexível |
| **Soda** | Open source / Cloud | DSL amigável, fácil adoção |
| **Monte Carlo** | Comercial | Monitoramento automático e ML-based |
| **Bigeye** | Comercial | Observabilidade e alertas automáticos |
| **Anomalo** | Comercial | Detecção de anomalias estatísticas |
| **Elementary** | Open source | Observabilidade nativa para dbt |


## Quarentena de Dados

Quando dados com problemas de qualidade são detectados, eles não devem simplesmente ser descartados ou propagados. A **quarentena** é uma área separada onde dados problemáticos são isolados para investigação, correção e eventual reprocessamento.

``` mermaid
flowchart LR

    ING["Ingestão"]
    VAL{"Validação"}

    BR["Bronze"]
    SI["Silver"]
    GO["Gold"]

    QUA["Zona de Quarentena"]

    ALERT["Alerta<br/>time"]
    FIX["Investigação<br/>& Correção"]

    ING --> VAL
    VAL -->|OK| BR --> SI --> GO

    VAL -->|FAIL| QUA
    QUA --> ALERT
    QUA --> FIX

    %% Styles
    classDef ingest fill:#17a2b8,stroke:#0f4c5c,color:#ffffff,stroke-width:2px;
    classDef decision fill:#9467bd,stroke:#5a3e85,color:#ffffff,stroke-width:2px;
    classDef bronze fill:#cd7f32,stroke:#8c4a1f,color:#ffffff,stroke-width:2px;
    classDef silver fill:#c0c0c0,stroke:#6e6e6e,color:#000000,stroke-width:2px;
    classDef gold fill:#ffd700,stroke:#b89600,color:#000000,stroke-width:2px;
    classDef quarantine fill:#d62728,stroke:#7f1d1d,color:#ffffff,stroke-width:2px;
    classDef action fill:#ff7f0e,stroke:#b45309,color:#ffffff,stroke-width:2px;

    class ING ingest;
    class VAL decision;
    class BR bronze;
    class SI silver;
    class GO gold;
    class QUA quarantine;
    class ALERT,FIX action;
```


## Cultura de Qualidade de Dados

Qualidade de dados não é responsabilidade exclusiva do time de engenharia. É um esforço colaborativo:

- **Engenharia de dados:** implementa os controles técnicos e monitora os pipelines
- **Times de negócio:** definem o que é "dado correto" para cada caso de uso
- **Analistas:** reportam inconsistências que descobrem nas análises
- **Proprietários de sistemas fonte:** garantem que os sistemas de origem produzem dados confiáveis

**Data contracts** são um padrão emergente: acordos formais entre produtores e consumidores de dados que documentam o schema, qualidade esperada e SLAs — funcionando como uma API contract, mas para dados.


## Boas Práticas

✅ Defina testes antes de colocar um pipeline em produção, não depois.

✅ Trate falhas de qualidade como falhas de pipeline — pare o fluxo e alerte.

✅ Monitore tendências, não apenas snapshots. Um indicador que piora gradualmente é tão perigoso quanto uma falha súbita.

✅ Documente as regras de qualidade junto com a documentação do modelo de dados.

✅ Use thresholds percentuais em vez de absolutos para testes de volume (dados crescem com o tempo).

✅ Separe testes críticos (bloqueantes) de testes de warning (informativos).


## Referências

- [Great Expectations Documentation](https://docs.greatexpectations.io/)
- [Soda Documentation](https://docs.soda.io/)
- [dbt Testing Guide](https://docs.getdbt.com/docs/build/data-tests)
- [Elementary Data (open source dbt observability)](https://www.elementary-data.com/)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)


← [Orquestração](./6-orquestacao.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Observabilidade →](./8-observabilidade.md)


*Documentação em construção · Portfólio pessoal*
