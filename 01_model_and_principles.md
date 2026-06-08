# Data Lake Pattern Model and Principles

## 1. Context

UCL's Integration approach was successful because it created a safe design language between architecture and implementation.

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

The Data Lake equivalent should be:

```text
Business / reporting / analytics / data use case
        ↓
Data Lake pattern catalogue
        ↓
Data Lake molecules / reusable building blocks
        ↓
Low-level Fabric / Purview implementation pattern
        ↓
Centrally managed notebooks, pipelines, templates, Terraform/IaC and configuration
        ↓
Standardised governed data product
```

The goal is not simply to create technical documentation. The objective is to create an **architecture-to-implementation system** that enables safe federation.

## 2. Main goal

The main goal is to allow:

- **Solution Architects** to safely design end-to-end Data Lake use cases without needing deep Fabric, data engineering or Purview knowledge;
- **federated users and delivery teams** to safely implement or configure solutions using centrally managed building blocks;
- **Data Architecture** to provide standards, patterns and assurance instead of designing every solution from scratch;
- **Data Platform** to provide reusable assets instead of bespoke implementation for every requirement.

## 3. Design principles

### 3.1 Pattern first, technology second

Patterns should describe the reusable architecture decision first. Fabric/Purview implementation should be mapped underneath, not drive the pattern language.

### 3.2 Safe design for Solution Architects

A Solution Architect should be able to select and combine patterns such as:

```text
Database Table to Bronze
→ Bronze to Silver Standardisation
→ Silver Conformance to Domain Model
→ Gold Star Schema Model
→ Semantic Model / Power BI Dataset
→ Purview Registration
→ Monitoring and Alerting
```

without needing to design every notebook, workspace, pipeline, service principal, metadata table or deployment step manually.

**Be honest about where the framework gives leverage.** Two distinct levels of help exist, and each pattern must declare which it provides (see *Leverage type* in `03_pattern_template.md`):

- **Reusable asset** — the SA *configures* a centrally managed building block; little or no bespoke engineering. This is realistic for most Ingestion, Governance and Operational patterns.
- **Guided design** — the framework supplies guardrails, decision questions and reference implementations, but genuine design judgement remains. This is the honest position for most Data Product (modelling) patterns and some Transformation patterns: a star schema or domain conformance cannot be fully templatised. The framework keeps these *safe*, not *automatic*.

Overstating the modelling layer as turnkey would set the wrong expectation with both architects and delivery partners.

### 3.3 Centrally managed implementation assets

The low-level implementation should be standardised through centrally managed assets, such as:

- Fabric workspace templates;
- Fabric pipeline templates;
- reusable notebooks;
- shared Python/PySpark libraries;
- metadata-driven configuration;
- Terraform/IaC modules;
- Purview registration helpers;
- monitoring and reconciliation framework;
- security and access templates.

### 3.4 Federation through configuration, not reinvention

Federated users should not build from scratch. They should configure approved patterns and building blocks.

### 3.5 Governance embedded by design

Governance must not be an afterthought. Each pattern must define:

- Data Owner;
- Data Steward;
- Data Custodian;
- glossary linkage;
- classification;
- lineage;
- data quality controls;
- access controls;
- operational ownership;
- support model;
- assurance checkpoints.

**Embedded must mean enforced, not just documented.** In a federated model, any control that is merely "expected" will drift. Where possible, governance controls are enforced as gates — e.g. an asset without owner, steward and classification cannot be promoted to production. The enforcement model (policy-as-code and deployment gates) is defined in `06_cross_cutting_concerns.md`.

### 3.6 Known foundational dependency: the Enterprise/Domain Data Model

Several Transformation and Data Product patterns (notably B2 Silver Conformance) depend on a mature Enterprise/Domain Data Model to conform to. The Integration approach had an established EDM; the Data Lake side must not assume one exists. Where the domain model is immature or absent, this is a critical dependency to resolve before those patterns can be completed — it is tracked as a framework-level risk in `08_operating_model.md`.

## 4. Pattern catalogue structure

The Data Lake pattern catalogue should be structured into five layers:

1. **Ingestion patterns** — how data enters the lake.
2. **Transformation patterns** — how raw data becomes trusted, conformed and curated.
3. **Data product patterns** — how trusted data is published for reporting, analytics and reuse.
4. **Governance patterns** — how ownership, quality, lineage, glossary, classification and access are applied.
5. **Operational patterns** — how solutions are monitored, reconciled, deployed, supported and controlled.

These are the patterns defined by the framework, providing enough low-level mapping to make them implementable in Microsoft Fabric and Purview.
