# Governança e Segurança de Dados

> *"Dados sem governança são um ativo à espera de se tornar um passivo."*

← [Voltar ao índice](./0-engenharia-de-dados.md)


## O que é Governança de Dados?

Governança de dados é o **conjunto de políticas, processos, papéis e padrões** que garantem que os dados de uma organização sejam gerenciados de forma responsável, segura e alinhada aos objetivos de negócio e às regulações aplicáveis.

Não se trata apenas de tecnologia — é principalmente uma questão de **pessoas e processos** suportados por tecnologia.

Uma boa governança responde:
- Quem tem acesso a quais dados?
- Quem é responsável por garantir a qualidade de cada conjunto de dados?
- Por quanto tempo os dados devem ser retidos?
- Como dados sensíveis são protegidos?
- Como garantir conformidade com LGPD, GDPR e outras regulações?


## Por que Governança é Essencial?

**Conformidade regulatória:** LGPD (Brasil), GDPR (Europa), CCPA (Califórnia) e outras leis estabelecem obrigações legais sobre como dados pessoais são coletados, processados, armazenados e descartados. Violações resultam em multas severas e danos reputacionais.

**Segurança:** dados mal protegidos são um vetor de ataque. Vazamentos de dados têm custos financeiros, legais e de reputação altíssimos.

**Confiabilidade:** sem responsabilidades claras, ninguém sabe quem corrige um problema de qualidade. Com governança, cada ativo de dados tem um dono.

**Escalabilidade organizacional:** quanto maior a organização e o volume de dados, mais crítica se torna a governança para evitar caos e inconsistências.


## LGPD — Lei Geral de Proteção de Dados

Sancionada em 2018 e em vigor desde 2020, a LGPD é a lei brasileira que regula o tratamento de dados pessoais de indivíduos no Brasil, inspirada no GDPR europeu.

**Conceitos centrais:**

**Dado pessoal:** qualquer informação relacionada a pessoa natural identificada ou identificável (nome, CPF, e-mail, IP, localização, etc.).

**Dado pessoal sensível:** dados sobre origem racial, convicção religiosa, saúde, biometria, orientação sexual, entre outros. Requerem proteção adicional.

**Titular:** a pessoa a quem os dados pertencem.

**Controlador:** quem decide como e por que os dados são tratados.

**Operador:** quem processa dados em nome do controlador.

**Bases legais para tratamento:** consentimento, cumprimento de obrigação legal, execução de contrato, legítimo interesse, proteção da vida, entre outras. **Todo tratamento precisa de uma base legal**.

**Direitos dos titulares:** confirmação de tratamento, acesso aos dados, correção, portabilidade, eliminação, revogação de consentimento.

**ANPD:** Autoridade Nacional de Proteção de Dados, responsável pela fiscalização.

**Implicações para engenharia de dados:**
- Mapear quais tabelas e colunas contêm dados pessoais
- Garantir que dados pessoais são protegidos em todas as camadas
- Implementar mecanismos de exclusão e portabilidade
- Documentar bases legais para cada tratamento
- Definir prazos de retenção e garantir eliminação após o prazo


## Conceitos Fundamentais de Segurança

### Controle de Acesso

#### RBAC — Role-Based Access Control
Permissões são atribuídas a **papéis (roles)**, não a usuários individuais. Usuários recebem papéis que carregam as permissões correspondentes.

```
Usuário → Papel (Role) → Permissões
Ana      → Analista BI  → SELECT em tabelas Gold
Bruno    → Engenheiro   → SELECT, INSERT em Bronze e Silver
Carlos   → Admin        → Todos os privilégios
```

**Vantagem:** mais fácil de gerenciar. Revogar acesso de um usuário que saiu da empresa é simplesmente remover seu papel.

#### ABAC — Attribute-Based Access Control
Permissões baseadas em **atributos** do usuário, do dado e do contexto. Mais granular e flexível que RBAC, mas também mais complexo.

Exemplo: um analista pode ver dados de qualquer região, mas um gerente regional só vê dados da sua região.

#### Princípio do Menor Privilégio
Cada usuário, serviço ou sistema deve ter **apenas as permissões mínimas necessárias** para realizar suas funções. Não dar `ADMIN` a quem só precisa de `SELECT`.


### Criptografia

**Em trânsito (in transit):** dados são criptografados enquanto trafegam entre sistemas. TLS/HTTPS é o padrão. Dados nunca devem trafegar em texto plano.

**Em repouso (at rest):** dados são criptografados enquanto armazenados em disco. Provedores cloud oferecem criptografia nativa de object storage e bancos de dados (AWS KMS, Google CMEK, Azure Key Vault).

**Gerenciamento de chaves:** quem controla as chaves de criptografia controla os dados. Use serviços gerenciados de KMS (Key Management Service) em vez de gerenciar chaves manualmente.


### Mascaramento e Anonimização

Técnicas para **proteger dados sensíveis** enquanto ainda permitem uso analítico:

**Mascaramento (Data Masking):** substitui dados reais por dados fictícios mas realistas. Usado em ambientes de desenvolvimento e teste.
```
CPF original:  123.456.789-00
CPF mascarado: ***.***.789-**
```

**Pseudonimização:** substitui identificadores diretos por pseudônimos. O dado ainda pode ser re-identificado com a tabela de mapeamento (que deve ser mantida segura).

**Anonimização:** remoção irreversível de informações identificadoras. Dados verdadeiramente anônimos saem do escopo da LGPD. É difícil garantir anonimização completa na prática.

**Tokenização:** substitui dados sensíveis por tokens sem valor semântico. Muito usada para dados de cartão de crédito (PCI-DSS).

**Generalização:** reduz a precisão dos dados para dificultar re-identificação (ex: ano de nascimento em vez de data completa, cidade em vez de endereço completo).


### Auditoria

Registro de **quem acessou quais dados, quando e o que fez** com eles. Essencial para conformidade regulatória e investigação de incidentes.

**O que deve ser auditado:**
- Logins e tentativas de login (sucesso e falha)
- Queries executadas em dados sensíveis
- Alterações em políticas de acesso e permissões
- Exportações de dados em volume
- Acesso a dados de categorias sensíveis

A maioria dos Data Warehouses modernos (BigQuery, Snowflake, Redshift) oferece logs de auditoria nativos.


## Classificação de Dados

Antes de proteger os dados, é preciso saber quais dados existem e quão sensíveis são. A classificação cria um esquema de categorias:

| Classificação | Descrição | Exemplos | Controles |
|---------------|-----------|----------|-----------|
| **Público** | Pode ser compartilhado livremente | Catálogo de produtos, preços de lista | Nenhum específico |
| **Interno** | Uso interno, sem restrições severas | Relatórios de performance, dados agregados | Autenticação básica |
| **Confidencial** | Acesso restrito a times específicos | Dados financeiros, estratégia, salários | RBAC, auditoria |
| **Restrito / Sensível** | Dados pessoais, regulados | CPF, e-mail, dados de saúde | Criptografia, mascaramento, auditoria completa |


## Retenção e Descarte de Dados

A LGPD e outras regulações exigem que dados pessoais sejam mantidos **apenas pelo tempo necessário** para o propósito que justificou sua coleta.

**Políticas de retenção:**
- Defina prazos por categoria de dado (ex: logs de acesso por 90 dias, dados de clientes por 5 anos após o contrato)
- Implemente deleção automática ao final do prazo
- Documente os prazos e as bases legais

**Deleção em sistemas de dados:** deletar um dado de um banco de dados transacional é simples. Em um Data Lake com múltiplas camadas (Bronze, Silver, Gold), backups e snapshots, a deleção completa exige planejamento. Formatos como Delta Lake e Iceberg suportam operações de `DELETE` sobre dados históricos.


## Data Catalog e Documentação

Um **catálogo de dados** é a ferramenta central de governança — o inventário de todos os ativos de dados com metadados, classificações, responsáveis e linhagem. Ver [Observabilidade](./8-observabilidade.md).

**Metadados essenciais para governança:**
- Classificação de sensibilidade por coluna
- Proprietário (data owner) responsável
- Base legal para tratamento (LGPD)
- Prazo de retenção
- Descrição e glossário de negócio


## Data Mesh e Governança Federada

Em arquiteturas Data Mesh, a governança é **federada**: cada domínio é responsável pela governança de seus próprios dados, mas dentro de um framework global de políticas. Ver [Arquitetura de Dados](./1-arquitetura-de-dados.md).

Isso exige uma plataforma self-service que automatize a aplicação das políticas globais (ex: criptografia obrigatória, logging de acesso) sem depender de um time central para cada decisão.


## Frameworks e Melhores Práticas

### Privacy by Design
Incorporar proteção de privacidade **desde o início** do design de sistemas e pipelines, não como um add-on posterior. Princípio fundamental da LGPD e GDPR.

### Data Contracts
Acordos formais entre produtores e consumidores de dados que especificam schema, qualidade, SLAs e responsabilidades. Evitam quebras silenciosas quando a fonte muda.

### Least Privilege em Serviços
Contas de serviço (service accounts) de pipelines devem ter apenas as permissões mínimas necessárias. Um pipeline que só lê da tabela X não deve ter permissão de escrita ou acesso a outras tabelas.

### Secrets Management
Credenciais, tokens e chaves de API nunca devem estar no código ou em variáveis de ambiente expostas. Use:
- AWS Secrets Manager
- Google Secret Manager
- HashiCorp Vault
- Azure Key Vault


## Checklist de Governança e Segurança

- [ ] Todas as tabelas e colunas com dados pessoais estão mapeadas e classificadas
- [ ] Controle de acesso implementado com RBAC por camada (Bronze, Silver, Gold)
- [ ] Criptografia em trânsito (TLS) e em repouso habilitada
- [ ] Dados pessoais mascarados em ambientes de desenvolvimento e teste
- [ ] Logs de auditoria habilitados para acesso a dados sensíveis
- [ ] Políticas de retenção definidas e automatizadas
- [ ] Credenciais gerenciadas por secrets manager (nunca em código)
- [ ] Catálogo de dados com proprietários e classificações definidos
- [ ] Processo documentado para atender requisições de titulares (acesso, correção, exclusão)
- [ ] Time treinado sobre LGPD e boas práticas de segurança


## Referências

- [Lei Geral de Proteção de Dados — LGPD (Lei nº 13.709/2018)](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
- [ANPD — Autoridade Nacional de Proteção de Dados](https://www.gov.br/anpd/)
- [GDPR — General Data Protection Regulation](https://gdpr.eu/)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- [DAMA-DMBOK: Data Management Body of Knowledge](https://www.dama.org/cpages/body-of-knowledge)


← [Observabilidade](./8-observabilidade.md) · [Voltar ao índice](./0-engenharia-de-dados.md) · [Cloud e Infraestrutura →](./10-cloud-e-infraestrutura.md)


*Documentação em construção · Portfólio pessoal*
