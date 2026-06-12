# Data Governance and Security

> *"Data without governance is an asset waiting to become a liability."*

← [Back to index](./0-data-engineering.md)


## What Is Data Governance?

Data governance is the **set of policies, processes, roles, and standards** that ensure an organization’s data is managed responsibly, securely, and in alignment with business goals and applicable regulations.

It is not just about technology — it is mainly a matter of **people and processes** supported by technology.

Good governance answers:
- Who has access to which data?
- Who is responsible for guaranteeing the quality of each dataset?
- How long should data be retained?
- How is sensitive data protected?
- How do we ensure compliance with LGPD, GDPR, and other regulations?


## Why Governance Is Essential

**Regulatory compliance:** LGPD (Brazil), GDPR (Europe), CCPA (California), and other laws establish legal obligations for how personal data is collected, processed, stored, and discarded. Violations result in severe fines and reputational damage.

**Security:** poorly protected data is an attack vector. Data breaches have very high financial, legal, and reputational costs.

**Reliability:** without clear responsibilities, no one knows who fixes a quality issue. With governance, every data asset has an owner.

**Organizational scalability:** the larger the organization and the greater the data volume, the more critical governance becomes to avoid chaos and inconsistency.


## LGPD — General Data Protection Law

Enacted in 2018 and effective since 2020, LGPD is the Brazilian law that regulates the processing of personal data of individuals in Brazil, inspired by the European GDPR.

**Core concepts:**

**Personal data:** any information related to an identified or identifiable natural person (name, national ID, email, IP, location, and so on).

**Sensitive personal data:** data about racial origin, religious belief, health, biometrics, sexual orientation, among others. These require additional protection.

**Data subject:** the person to whom the data belongs.

**Controller:** the entity that determines how and why data is processed.

**Operator:** the entity that processes data on behalf of the controller.

**Legal bases for processing:** consent, legal obligation, contract execution, legitimate interest, protection of life, among others. **Every processing activity needs a legal basis**.

**Data subject rights:** confirmation of processing, access to data, correction, portability, deletion, consent withdrawal.

**ANPD:** National Data Protection Authority, responsible for oversight.

**Implications for data engineering:**
- Map which tables and columns contain personal data
- Ensure personal data is protected across all layers
- Implement deletion and portability mechanisms
- Document the legal basis for each processing activity
- Define retention periods and ensure deletion after the deadline


## Fundamental Security Concepts

### Access Control

#### RBAC — Role-Based Access Control
Permissions are assigned to **roles**, not to individual users. Users receive roles that carry the corresponding permissions.

```text
User   → Role         → Permissions
Ana    → BI Analyst   → SELECT on Gold tables
Bruno  → Engineer     → SELECT, INSERT on Bronze and Silver
Carlos → Admin        → All privileges
```

**Advantage:** easier to manage. Revoking access for a user who left the company is simply removing their role.

#### ABAC — Attribute-Based Access Control
Permissions based on **attributes** of the user, the data, and the context. More granular and flexible than RBAC, but also more complex.

Example: an analyst can view data from any region, but a regional manager sees only data from their own region.

#### Principle of Least Privilege
Each user, service, or system should have **only the minimum permissions necessary** to perform its function. Do not give `ADMIN` to someone who only needs `SELECT`.


### Encryption

**In transit:** data is encrypted while moving between systems. TLS/HTTPS is the standard. Data should never travel in plain text.

**At rest:** data is encrypted while stored on disk. Cloud providers offer native encryption for object storage and databases (AWS KMS, Google CMEK, Azure Key Vault).

**Key management:** whoever controls the encryption keys controls the data. Use managed KMS (Key Management Service) offerings instead of managing keys manually.


### Masking and Anonymization

Techniques to **protect sensitive data** while still allowing analytical use:

**Masking (Data Masking):** replaces real data with fictitious but realistic data. Used in development and test environments.
```text
Original ID:  123.456.789-00
Masked ID:    ***.***.789-**
```

**Pseudonymization:** replaces direct identifiers with pseudonyms. The data can still be re-identified using the mapping table (which must be kept secure).

**Anonymization:** irreversible removal of identifying information. Truly anonymous data falls outside the scope of LGPD. In practice, complete anonymization is difficult to guarantee.

**Tokenization:** replaces sensitive data with tokens with no semantic value. Widely used for credit card data (PCI-DSS).

**Generalization:** reduces data precision to make re-identification harder (for example: birth year instead of full date, city instead of full address).


### Auditing

Recording **who accessed which data, when, and what they did** with it. Essential for regulatory compliance and incident investigation.

**What should be audited:**
- Logins and login attempts (success and failure)
- Queries run against sensitive data
- Changes to access policies and permissions
- Large-volume data exports
- Access to sensitive data categories

Most modern Data Warehouses (BigQuery, Snowflake, Redshift) offer native audit logs.


## Data Classification

Before protecting data, it is necessary to know which data exists and how sensitive it is. Classification creates a category scheme:

| Classification | Description | Examples | Controls |
|---------------|-----------|----------|-----------|
| **Public** | Can be freely shared | Product catalog, list prices | None specific |
| **Internal** | Internal use, without severe restrictions | Performance reports, aggregated data | Basic authentication |
| **Confidential** | Access restricted to specific teams | Financial data, strategy, salaries | RBAC, auditing |
| **Restricted / Sensitive** | Personal or regulated data | National ID, email, health data | Encryption, masking, full auditing |


## Data Retention and Disposal

LGPD and other regulations require personal data to be retained **only for as long as necessary** for the purpose that justified its collection.

**Retention policies:**
- Define deadlines by data category (for example: access logs for 90 days, customer data for 5 years after contract end)
- Implement automatic deletion at the end of the retention period
- Document deadlines and legal bases

**Deletion in data systems:** deleting data from a transactional database is simple. In a Data Lake with multiple layers (Bronze, Silver, Gold), backups, and snapshots, complete deletion requires planning. Formats like Delta Lake and Iceberg support `DELETE` operations on historical data.


## Data Catalog and Documentation

A **data catalog** is the central governance tool — the inventory of all data assets with metadata, classifications, owners, and lineage. See [Observability](./8-observability.md).

**Essential metadata for governance:**
- Sensitivity classification by column
- Responsible owner (data owner)
- Legal basis for processing (LGPD)
- Retention period
- Business description and glossary


## Data Mesh and Federated Governance

In Data Mesh architectures, governance is **federated**: each domain is responsible for governing its own data, but within a global policy framework. See [Data Architecture](./1-data-architecture.md).

This requires a self-service platform that automates enforcement of global policies (for example: mandatory encryption, access logging) without depending on a central team for every decision.


## Frameworks and Best Practices

### Privacy by Design
Incorporating privacy protection **from the beginning** of system and pipeline design, not as a later add-on. A fundamental principle of LGPD and GDPR.

### Data Contracts
Formal agreements between data producers and consumers that specify schema, quality, SLAs, and responsibilities. They prevent silent breakages when the source changes.

### Least Privilege for Services
Service accounts used by pipelines should have only the minimum required permissions. A pipeline that only reads from table X should not have write access or access to other tables.

### Secrets Management
Credentials, tokens, and API keys should never be stored in code or exposed environment variables. Use:
- AWS Secrets Manager
- Google Secret Manager
- HashiCorp Vault
- Azure Key Vault


## Governance and Security Checklist

- [ ] All tables and columns containing personal data are mapped and classified
- [ ] Access control implemented with RBAC by layer (Bronze, Silver, Gold)
- [ ] Encryption in transit (TLS) and at rest enabled
- [ ] Personal data masked in development and test environments
- [ ] Audit logs enabled for access to sensitive data
- [ ] Retention policies defined and automated
- [ ] Credentials managed by a secrets manager (never in code)
- [ ] Data catalog with defined owners and classifications
- [ ] Documented process to handle data subject requests (access, correction, deletion)
- [ ] Team trained on LGPD and security best practices


## References

- [Lei Geral de Proteção de Dados — LGPD (Law No. 13.709/2018)](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
- [ANPD — National Data Protection Authority](https://www.gov.br/anpd/)
- [GDPR — General Data Protection Regulation](https://gdpr.eu/)
- **Fundamentals of Data Engineering** — Joe Reis & Matt Housley (O'Reilly)
- [DAMA-DMBOK: Data Management Body of Knowledge](https://www.dama.org/cpages/body-of-knowledge)


← [Observability](./8-observability.md) · [Back to index](./0-data-engineering.md) · [Cloud and Infrastructure →](./10-cloud-and-infrastructure.md)


*Documentation in progress · Personal portfolio*
