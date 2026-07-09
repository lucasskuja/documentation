# Camada Semântica e Analytics Engineering

> *"A camada semântica transforma tabelas em linguagem de negócio: métricas, dimensões e regras compartilhadas."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## O que é Analytics Engineering?

Analytics Engineering é a prática de aplicar engenharia de software à construção de modelos analíticos, métricas e marts consumidos por BI, análise, produto e ciência de dados.

Ela fica entre engenharia de dados e análise de dados:

- Engenharia de dados garante ingestão, armazenamento e processamento confiável.
- Analytics Engineering modela dados para consumo analítico.
- BI e análise usam esses modelos para decisões de negócio.


## O que é Camada Semântica?

Camada semântica é a camada que define conceitos de negócio de forma centralizada, como métricas, dimensões, entidades e regras de cálculo.

**Sem camada semântica:**

- Cada dashboard calcula receita de um jeito.
- Times usam filtros diferentes para o mesmo KPI.
- Métricas mudam sem rastreabilidade.
- Ninguém sabe qual número é oficial.

**Com camada semântica:**

- Métricas têm definição única.
- Dimensões são padronizadas.
- Owners são claros.
- Ferramentas diferentes consomem a mesma lógica.


## Métrica, Dimensão e Entidade

| Conceito | Definição | Exemplo |
|---|---|---|
| Métrica | Valor mensurável agregado | Receita, churn, pedidos |
| Dimensão | Atributo usado para fatiar métricas | Região, produto, canal |
| Entidade | Objeto de negócio | Cliente, conta, pedido |
| Grão | Nível de detalhe do dado | Uma linha por pedido |
| Filtro | Regra aplicada ao cálculo | `status = 'completed'` |


## Métrica Certificada

Uma métrica certificada é uma métrica oficial, documentada e validada.

**Deve ter:**

- Nome de negócio.
- Definição.
- Fórmula.
- Grão.
- Filtros.
- Fonte.
- Owner.
- SLA.
- Exemplos de uso.
- Limitações conhecidas.

**Exemplo:**

```yaml
metric: receita_liquida
description: Receita total de pedidos concluídos descontando reembolsos.
owner: finance_analytics
grain: pedido
formula: sum(valor_pago - valor_reembolsado)
filters:
  - status = 'completed'
dimensions:
  - data_pedido
  - canal
  - pais
```


## Data Marts

Data marts são modelos orientados a consumo, geralmente organizados por domínio ou caso de uso.

| Mart | Consumidores | Exemplos |
|---|---|---|
| Financeiro | Finanças, diretoria | Receita, margem, inadimplência |
| Marketing | Growth, CRM | CAC, campanhas, conversão |
| Produto | Produto, UX | Adoção, retenção, funil |
| Operações | Logística, suporte | SLA, tickets, entregas |

Marts devem ser fáceis de consultar, bem documentados e alinhados ao vocabulário do negócio.


## dbt e Analytics Engineering

dbt popularizou Analytics Engineering ao trazer práticas de engenharia para SQL:

- Modelos versionados.
- Testes.
- Documentação.
- Lineage.
- Macros.
- Materializações.
- Camadas staging, intermediate e marts.

**Organização comum:**

```text
models/
|-- staging/
|-- intermediate/
`-- marts/
    |-- finance/
    |-- marketing/
    `-- product/
```


## Camada Semântica em Ferramentas

| Ferramenta | Papel |
|---|---|
| dbt Semantic Layer / MetricFlow | Métricas definidas como código |
| LookML | Modelo semântico do Looker |
| Cube | Camada semântica e APIs para métricas |
| Power BI semantic model | Modelo semântico para relatórios Power BI |
| Tableau data model | Relações, fontes e cálculos para Tableau |

O objetivo não é escolher a ferramenta mais popular, mas garantir consistência e governança das definições.


## Métricas vs Tabelas Gold

| Item | Papel |
|---|---|
| Tabela Gold | Dados modelados e prontos para consumo |
| Métrica | Cálculo de negócio sobre dados modelados |
| Dashboard | Visualização de métricas e dimensões |
| Camada semântica | Local onde a métrica é definida e governada |

Uma tabela Gold pode alimentar várias métricas. Uma métrica pode ser consumida por vários dashboards.


## Contratos de Métricas

Assim como datasets, métricas precisam de contratos.

**Contrato deve definir:**

- Fórmula.
- Fonte.
- Grão.
- Atualização.
- Tolerância de atraso.
- Dimensões permitidas.
- Regras de acesso.
- Owner técnico e owner de negócio.
- Processo de mudança.


## Governança de Métricas

**Boas práticas:**

- Defina owners por domínio.
- Certifique métricas oficiais.
- Versione mudanças relevantes.
- Deprecie métricas antigas com prazo.
- Documente diferenças entre métricas parecidas.
- Monitore freshness e qualidade das tabelas base.
- Evite múltiplas definições para o mesmo KPI.


## Anti-patterns

- KPI calculado diretamente no dashboard sem documentação.
- Cada time mantém uma versão da mesma métrica.
- Tabela Gold tratada como métrica oficial sem definição.
- Métricas sem owner.
- Mudanças silenciosas em fórmulas.
- Dimensões com nomes diferentes para o mesmo conceito.
- Uso de dados não certificados em apresentações executivas.


## Checklist

- A métrica tem definição clara?
- O grão está explícito?
- A fórmula está versionada?
- Existe owner técnico e de negócio?
- Dimensões permitidas estão documentadas?
- A métrica tem testes?
- A origem dos dados é rastreável?
- Existe SLA de atualização?
- Dashboards usam a métrica certificada?
- Mudanças passam por review?


## Referências

- [dbt Semantic Layer](https://docs.getdbt.com/docs/use-dbt-semantic-layer/dbt-sl)
- [MetricFlow Documentation](https://docs.getdbt.com/docs/build/about-metricflow)
- [Looker LookML](https://cloud.google.com/looker/docs/lookml-reference)
- [Cube Documentation](https://cube.dev/docs)
- [Microsoft Power BI Semantic Models](https://learn.microsoft.com/en-us/power-bi/connect-data/service-datasets-understand)


← [Técnicas Avançadas](./18-tecnicas-avancadas.md) · [Voltar ao índice](./0-engenharia-de-dados.md)


*Documentação em construção · Portfólio pessoal*

