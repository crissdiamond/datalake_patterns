# Example End-to-End Data Lake Patterns

## 1. Purpose

This document gives example Data Lake patterns to clarify the expected pattern style.

Each pattern should combine molecules into an end-to-end architecture that a Solution Architect can use safely, while also mapping to low-level Fabric/Purview implementation.

---

# Pattern DL-P01: Source File to Governed Gold Data Product

## Intent

Use this pattern when a source system or supplier provides scheduled files that need to be ingested, governed, transformed and published as a Gold reporting data product.

## When to use

Use when:

- source data is provided as files;
- the data is required for operational or analytical reporting;
- the output needs to be reusable and governed;
- lineage, ownership, quality and access controls are required.

## When not to use

Do not use when:

- the source requires real-time integration;
- the use case is a one-off unmanaged extract;
- the data does not need to be stored or reused;
- the data is not approved for platform ingestion.

## Molecule sequence

```text
M01 Receive File
→ M04 Land Raw Data
→ M05 Capture Source Metadata
→ M07 Validate Schema
→ M08 Apply Data Quality Rules
→ M09 Quarantine Invalid Data
→ M10 Standardise Source Data
→ M11 Map to EDM / Domain Model
→ M13 Create Silver Curated Dataset
→ M16 Build Gold Dimensional Model
→ M18 Publish Semantic Model
→ M19 Register Data Product in Purview
→ M20 Link to Business Glossary
→ M21 Capture Lineage
→ M22 Apply Access Policy
→ M25 Monitor Freshness
→ M26 Reconcile Counts and Totals
```

## High-level architecture

```text
Source File
   ↓
Approved Landing Mechanism
   ↓
Bronze Raw Zone
   ↓
Validation and Data Quality
   ↓
Silver Curated Domain Dataset
   ↓
Gold Dimensional Model
   ↓
Semantic Model / Power BI
   ↓
Purview Registration, Lineage, Ownership and Access Controls
```

## Low-level implementation mapping

| Layer | Fabric/Purview implementation |
|---|---|
| Landing | Fabric pipeline / approved landing storage |
| Bronze | Lakehouse raw table/folder with metadata |
| Validation | Reusable notebook/library and schema configuration |
| Data quality | Configurable DQ rules and DQ result table |
| Silver | Lakehouse Delta tables or equivalent curated structure |
| Gold | Warehouse/Lakehouse dimensional model |
| Consumption | Power BI semantic model, Direct Lake or Import decision |
| Governance | Purview asset registration, glossary, owner/steward, classification |
| Security | Entra groups, workspace/item permissions, RLS/OLS if required |
| Operations | Monitoring dashboard, alerts, runbook, reconciliation outputs |

## Required configuration

```yaml
pattern_id: DL-P01
source_system: string
source_owner: string
source_file_pattern: string
load_frequency: daily|weekly|monthly|ad_hoc
schema_definition: path_or_registry_reference
dq_ruleset: string
target_domain: string
silver_dataset_name: string
gold_product_name: string
semantic_model_required: true|false
purview_collection: string
access_group: string
freshness_sla: string
retention_policy: string
```

## Acceptance criteria

- Source files are landed with full ingestion metadata.
- Raw data is preserved in Bronze.
- Schema validation is applied.
- Data quality results are recorded.
- Invalid records are quarantined or flagged.
- Silver dataset is standardised and mapped to domain concepts.
- Gold product is fit for reporting.
- Semantic model is published where required.
- Purview registration is complete.
- Owner and steward are recorded.
- Access control is applied.
- Freshness and reconciliation monitoring are available.
- Support runbook is documented.

---

# Pattern DL-P02: API Source to Silver Curated Data Product

## Intent

Use this pattern when data is retrieved from a source API and curated into a reusable Silver dataset.

## Molecule sequence

```text
M02 Request API Data
→ M04 Land Raw Data
→ M05 Capture Source Metadata
→ M06 Apply Watermark
→ M07 Validate Schema
→ M08 Apply Data Quality Rules
→ M10 Standardise Source Data
→ M11 Map to EDM / Domain Model
→ M13 Create Silver Curated Dataset
→ M19 Register Data Product in Purview
→ M21 Capture Lineage
→ M22 Apply Access Policy
→ M24 Monitor Pipeline Run
→ M25 Monitor Freshness
```

## Key design decisions

- Authentication method for the source API.
- Pagination and rate-limit handling.
- Full load versus incremental load.
- Watermark strategy.
- API response versioning.
- Error handling and retry policy.
- Silver product ownership.
- Purview registration and lineage capture.

## Low-level implementation mapping

| Capability | Implementation |
|---|---|
| API request | Fabric/Data Factory pipeline with configured API connector |
| Authentication | Managed identity, OAuth or key vault backed secret |
| Pagination | Reusable API ingestion library/pattern |
| Watermark | Metadata-driven watermark table |
| Landing | Bronze raw API response table/folder |
| Validation | Schema validation notebook/library |
| Silver | Curated Lakehouse table |
| Governance | Purview asset, glossary terms, lineage and owner/steward metadata |
| Monitoring | Pipeline run log, freshness check and alerting |

---

# Pattern DL-P03: Bronze to Silver Standardisation and Quality

## Intent

Use this pattern when raw data already exists in Bronze and needs to be transformed into a trusted Silver dataset.

## Molecule sequence

```text
M07 Validate Schema
→ M08 Apply Data Quality Rules
→ M09 Quarantine Invalid Data
→ M10 Standardise Source Data
→ M11 Map to EDM / Domain Model
→ M12 Enrich with Reference Data
→ M13 Create Silver Curated Dataset
→ M19 Register Data Product in Purview
→ M20 Link to Business Glossary
→ M21 Capture Lineage
→ M26 Reconcile Counts and Totals
```

## Key design decisions

- Definition of Silver for the domain.
- Required level of EDM/domain conformance.
- Mandatory data quality rules.
- Handling of rejected records.
- Reference data ownership.
- Reconciliation requirements.
- Steward sign-off.

## Acceptance criteria

- Silver dataset has defined business purpose.
- Source-to-Silver transformation is documented.
- Data quality rules are configured and executed.
- Exceptions are visible and actionable.
- Glossary linkage exists for critical fields.
- Dataset is registered in Purview.
- Lineage is available.
- Owner/steward details are captured.

---

# Pattern DL-P04: Silver to Gold Dimensional Reporting Model

## Intent

Use this pattern when curated Silver data needs to be modelled into a Gold dimensional model for reporting and analytics.

## Molecule sequence

```text
M13 Create Silver Curated Dataset
→ M15 Apply Slowly Changing Dimension Logic
→ M16 Build Gold Dimensional Model
→ M18 Publish Semantic Model
→ M19 Register Data Product in Purview
→ M20 Link to Business Glossary
→ M21 Capture Lineage
→ M22 Apply Access Policy
→ M28 Expose to Power BI
→ M25 Monitor Freshness
```

## Key design decisions

- Fact and dimension grain.
- Conformed dimensions.
- Slowly changing dimension approach.
- Measure definitions.
- Semantic model ownership.
- Direct Lake versus Import mode.
- Report certification/endorsement route.
- Security and row-level access.

## Low-level implementation mapping

| Capability | Implementation |
|---|---|
| Dimensional model | Fabric Warehouse or Lakehouse tables |
| Transformations | Reusable notebook/PySpark/SQL pattern |
| SCD | Configurable SCD library |
| Semantic model | Power BI semantic model |
| Measures | Centrally reviewed DAX or semantic definitions |
| Security | Entra groups, RLS/OLS as required |
| Governance | Purview registration, glossary and lineage |
| Monitoring | Refresh/freshness and usage monitoring |

---

# Pattern DL-P05: Governed Operational Reporting Product

## Intent

Use this pattern for controlled operational reporting where the output may not require a full dimensional model but still requires governance, quality and operational support.

## Molecule sequence

```text
M01/M02/M03 Source Acquisition
→ M04 Land Raw Data
→ M07 Validate Schema
→ M08 Apply Data Quality Rules
→ M10 Standardise Source Data
→ M17 Build Gold Operational Reporting Model
→ M18 Publish Semantic Model
→ M19 Register Data Product in Purview
→ M22 Apply Access Policy
→ M28 Expose to Power BI
→ M24 Monitor Pipeline Run
→ M25 Monitor Freshness
```

## Key design decisions

- Is this genuinely operational reporting, or should it become a reusable analytical product?
- What is the required freshness?
- Who owns the output?
- What level of quality assurance is needed?
- Is the report local, portfolio-level or enterprise-level?
- Does it require certification?

## Acceptance criteria

- Operational purpose is documented.
- Report/data product owner is defined.
- Data quality rules are applied proportionately.
- Access is controlled.
- Refresh/freshness is monitored.
- Purview registration exists where required.
- Support route is clear.
