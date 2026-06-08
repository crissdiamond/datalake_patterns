# Data Lake Patterns Model

## 1. Context

UCL previously used an architecture pattern approach for Integration. The Integration approach separated complex integration delivery into reusable concepts that could be safely used by Solution Architects and consistently implemented by developers.

The key Integration model was:

```text
Business / architecture need
        ↓
Integration pattern
        ↓
Molecule-based design language
        ↓
Low-level implementation pattern
        ↓
EASIKit / Python / Terraform reusable code
        ↓
Standardised deployed integration
```

The goal for the Data Lake is to apply the same architecture method, not to copy the Integration patterns literally.

The proposed Data Lake equivalent is:

```text
Business / reporting / analytics / data use case
        ↓
Data Lake pattern
        ↓
Data product / pipeline molecule design language
        ↓
Low-level Fabric / Purview implementation pattern
        ↓
Centrally managed notebooks, pipelines, templates, Terraform/IaC and configuration
        ↓
Standardised governed data product
```

## 2. Strategic objective

The objective is to allow Solution Architects and federated delivery teams to safely succeed in Data Lake design and delivery without requiring every person to have deep expertise in Microsoft Fabric, Purview, data engineering, semantic modelling, data governance, security and operational support.

The model should provide:

- A common design language for end-to-end Data Lake use cases.
- A reusable pattern catalogue for Solution Architects.
- A reusable molecule catalogue for common data platform capabilities.
- Centrally managed implementation building blocks for federated delivery teams.
- Clear mapping from high-level architecture to low-level implementation.
- A consistent assurance model for Data Architecture, Data Platform and Governance.

## 3. What should be copied from the Integration approach

The following aspects of the Integration approach should be reused:

| Integration approach | Data Lake equivalent |
|---|---|
| Producer and consumer flows | Source-to-lake and data-product consumption flows |
| Enterprise API / distribution channel | Governed data product, semantic model, extract, API, sharing or reporting endpoint |
| Enterprise Data Model | EDM, domain model, business glossary and data product contract |
| Molecule catalogue | Data Lake molecule catalogue |
| Integration pattern catalogue | Data Lake pattern catalogue |
| EASIKit | DataLakeKit / FabricKit / RAMKit equivalent |
| Python reusable code | Shared PySpark/Python libraries and reusable notebooks |
| Terraform reusable modules | Fabric/Purview/workspace/security/deployment modules |
| Interface HLD | Data Product / Data Lake HLD |
| Low-level implementation pattern | Fabric/Purview implementation pattern |

## 4. What should change for the Data Lake

Integration patterns are primarily concerned with:

- receiving requests or events;
- transforming payloads;
- routing messages;
- connecting systems;
- exposing APIs or channels.

Data Lake patterns are concerned with:

- acquiring data;
- landing data;
- validating data;
- storing data;
- transforming data;
- curating data;
- governing data;
- publishing data products;
- enabling reporting and analytics;
- monitoring quality, lineage, freshness and usage.

Therefore, Data Lake molecules must combine technical implementation, data governance and operational control.

## 5. Data Lake design abstraction

A Solution Architect should not need to design directly in terms of every Fabric technical asset.

They should not need to start with:

```text
Fabric pipeline + Lakehouse + Warehouse + notebook + semantic model + workspace role + service principal + Purview scan + deployment pipeline + monitoring job
```

They should be able to design at the pattern/molecule level, for example:

```text
Receive Source Extract
→ Land in Bronze
→ Validate Schema
→ Apply Data Quality Rules
→ Standardise to Domain Model
→ Curate Silver
→ Build Gold Data Product
→ Publish Semantic Model
→ Register in Purview
→ Monitor Freshness and Quality
```

Each molecule then maps to a standard low-level implementation.

## 6. Definition of a Data Lake pattern

A Data Lake pattern is an end-to-end repeatable architecture for a common class of data use case.

A pattern should describe:

- the business scenario;
- when to use it;
- when not to use it;
- logical architecture;
- molecule sequence;
- data ownership and stewardship requirements;
- governance and Purview requirements;
- Fabric implementation approach;
- security and access model;
- data quality expectations;
- monitoring and operational support;
- implementation building blocks;
- assurance checkpoints;
- acceptance criteria.

## 7. Definition of a Data Lake molecule

A Data Lake molecule is a reusable design unit representing a common data platform capability.

It should be:

- understandable by Solution Architects;
- precise enough to map to low-level implementation;
- reusable across use cases;
- governed centrally;
- supported by standard implementation assets;
- configurable rather than bespoke wherever possible.

A molecule should include:

- purpose;
- inputs;
- outputs;
- preconditions;
- design guidance;
- governance implications;
- security implications;
- operational implications;
- implementation mapping;
- reusable code/templates/modules;
- configuration options;
- acceptance criteria.

## 8. Role of the central implementation kit

The Data Lake equivalent of EASIKit should provide centrally governed implementation building blocks.

Possible names:

- DataLakeKit;
- FabricKit;
- RAMKit;
- Data Product Kit;
- Lakehouse Accelerator.

Its role should be:

```text
Pattern catalogue
        ↓
Molecule catalogue
        ↓
Configuration
        ↓
Generated / deployed Fabric implementation
        ↓
Governed data product
```

It should include:

- reusable Fabric pipeline templates;
- reusable notebooks;
- shared Python/PySpark libraries;
- metadata-driven configuration;
- data quality rule templates;
- schema validation functions;
- audit and reconciliation structures;
- Purview registration helpers;
- workspace/environment templates;
- Terraform/IaC or deployment modules;
- monitoring and alerting framework;
- operational runbooks;
- worked examples.

## 9. Federation model

Federated users should be enabled, but not left to improvise.

The preferred model is:

- central Data Architecture defines patterns, guardrails and assurance;
- central Data Platform defines approved implementation assets;
- Data Governance defines ownership, glossary, quality and access expectations;
- federated teams configure and use approved building blocks;
- exceptions are reviewed through appropriate architecture/governance forums.

Federated teams should mostly vary configuration, not implementation logic.

Example configuration:

```yaml
source_system: SITS
source_type: database_extract
target_domain: Student
load_type: incremental
watermark_column: updated_at
bronze_retention_days: 90
dq_ruleset: student_core_v1
target_model: student_application_silver
publish_to: gold_reporting
purview_registration: true
owner: Student Data Owner
steward: Student Data Steward
```

## 10. Target outcome

The desired outcome is a reusable Data Lake pattern framework that allows UCL to move from bespoke, expert-led data platform design to a safe, repeatable and federated model.

The model should reduce:

- inconsistent designs;
- duplicated pipelines;
- local variation;
- over-reliance on scarce Data Architecture and Data Platform specialists;
- late design intervention;
- unclear ownership;
- ungoverned reporting datasets;
- weak lineage and data quality controls.

The model should increase:

- Solution Architect confidence;
- delivery consistency;
- platform reuse;
- governance-by-design;
- speed of onboarding;
- assurance quality;
- operational supportability;
- trust in data products.
