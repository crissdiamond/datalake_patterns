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

## 4. Pattern catalogue structure

The Data Lake pattern catalogue should be structured into five layers:

1. **Ingestion patterns** — how data enters the lake.
2. **Transformation patterns** — how raw data becomes trusted, conformed and curated.
3. **Data product patterns** — how trusted data is published for reporting, analytics and reuse.
4. **Governance patterns** — how ownership, quality, lineage, glossary, classification and access are applied.
5. **Operational patterns** — how solutions are monitored, reconciled, deployed, supported and controlled.

These are the patterns defined by the framework, providing enough low-level mapping to make them implementable in Microsoft Fabric and Purview.
