# Draft Data Lake Molecule Catalogue

## 1. Purpose

This document proposes an initial Data Lake molecule catalogue.

A molecule is a reusable design unit that represents a common Data Lake capability. It should be used by Solution Architects as a safe design language and by developers/federated teams as a route to approved implementation building blocks.

Each molecule should eventually have:

- high-level design description;
- low-level Fabric/Purview implementation mapping;
- reusable code/template/module mapping;
- configuration options;
- governance requirements;
- operational requirements;
- assurance criteria.

## 2. Molecule categories

Proposed categories:

1. Source acquisition molecules.
2. Landing and Bronze molecules.
3. Validation and quality molecules.
4. Standardisation and conformance molecules.
5. Silver curation molecules.
6. Gold modelling and publication molecules.
7. Governance and Purview molecules.
8. Security and access molecules.
9. Operational and support molecules.
10. Consumption molecules.

---

# 3. Source acquisition molecules

## M01 - Receive File

### Purpose

Receive a file from a source system, supplier or managed upload process.

### SA-level meaning

A source provides data as a file. The platform receives it through an approved mechanism and prepares it for controlled landing.

### Implementation mapping

Possible implementation components:

- Fabric pipeline;
- Data Factory pipeline where applicable;
- OneLake/Lakehouse landing area;
- storage path convention;
- file metadata capture;
- trigger/schedule configuration;
- error handling and alerting.

### Configuration examples

```yaml
source_system: string
file_type: csv|json|parquet|xlsx
schedule: cron_or_manual
expected_file_pattern: string
landing_path: string
```

---

## M02 - Request API Data

### Purpose

Retrieve data from a source API.

### SA-level meaning

The platform calls an approved source API to retrieve data for ingestion.

### Implementation mapping

- Fabric/Data Factory pipeline;
- API connection configuration;
- authentication secret reference;
- pagination handling;
- rate-limit handling;
- response landing;
- audit logging.

### Configuration examples

```yaml
api_endpoint: string
auth_method: managed_identity|oauth|api_key
pagination: true|false
rate_limit_policy: string
```

---

## M03 - Extract Database Data

### Purpose

Extract data from a source database or operational store.

### SA-level meaning

The platform extracts one or more tables/views using an approved connector, schedule and load approach.

### Implementation mapping

- Fabric/Data Factory pipeline;
- gateway or private endpoint where required;
- incremental or full load logic;
- watermark configuration;
- schema capture;
- audit metadata.

### Configuration examples

```yaml
source_database: string
source_object: schema.table
load_type: full|incremental
watermark_column: string
```

---

# 4. Landing and Bronze molecules

## M04 - Land Raw Data

### Purpose

Store source data in a raw/Bronze area without business transformation.

### SA-level meaning

The original data is preserved for traceability, reprocessing and audit.

### Implementation mapping

- Fabric Lakehouse Bronze area;
- OneLake folder convention;
- Delta or file format policy;
- metadata capture;
- retention policy;
- immutable/raw storage approach where required.

---

## M05 - Capture Source Metadata

### Purpose

Capture technical metadata about the source ingestion.

### SA-level meaning

The platform records what was received, when, from where, by which process, and whether it succeeded.

### Implementation mapping

- ingestion metadata table;
- run identifier;
- source file/table/API metadata;
- record counts;
- checksum where appropriate;
- status and error messages.

---

## M06 - Apply Watermark

### Purpose

Support incremental loading based on a defined watermark.

### SA-level meaning

Only new or changed records are processed after the initial load.

### Implementation mapping

- metadata table for watermark state;
- reusable library function;
- source query parameterisation;
- late-arriving data handling;
- reprocessing controls.

---

# 5. Validation and data quality molecules

## M07 - Validate Schema

### Purpose

Check that incoming data matches the expected structure.

### SA-level meaning

The data cannot progress if its structure is invalid.

### Implementation mapping

- schema configuration;
- reusable validation library;
- notebook or pipeline validation step;
- schema drift handling;
- quarantine/rejection path;
- alerting.

---

## M08 - Apply Data Quality Rules

### Purpose

Apply standard data quality rules to a dataset.

### SA-level meaning

Data is checked against agreed quality expectations before being trusted or published.

### Implementation mapping

- configurable DQ rules;
- reusable DQ library;
- DQ result table;
- pass/warn/fail thresholds;
- exception handling;
- Data Quality Issue Log integration where applicable.

### Example rules

- mandatory field completeness;
- referential validity;
- duplicate detection;
- valid value lists;
- date range checks;
- uniqueness checks.

---

## M09 - Quarantine Invalid Data

### Purpose

Separate invalid or suspicious records for investigation.

### SA-level meaning

Bad data is not silently published. It is retained, visible and actionable.

### Implementation mapping

- quarantine table or folder;
- error reason capture;
- DQ result linkage;
- steward notification;
- reprocessing process.

---

# 6. Standardisation and conformance molecules

## M10 - Standardise Source Data

### Purpose

Convert source-specific structures into standard technical and naming conventions.

### SA-level meaning

The data is made consistent enough for reuse across the platform.

### Implementation mapping

- transformation notebook;
- standard naming convention;
- type conversion;
- date/time normalisation;
- null handling;
- code/value normalisation.

---

## M11 - Map to EDM / Domain Model

### Purpose

Map source-specific data into enterprise or domain concepts.

### SA-level meaning

The data is translated into UCL's common data language.

### Implementation mapping

- mapping configuration;
- transformation notebook;
- EDM/domain model table;
- Purview glossary linkage;
- Data Owner/Steward sign-off.

---

## M12 - Enrich with Reference Data

### Purpose

Enrich the dataset with controlled reference data.

### SA-level meaning

The data is made more useful and consistent through approved reference data.

### Implementation mapping

- reference data source;
- join/enrichment logic;
- validity date handling;
- unmatched value reporting;
- reference data ownership.

---

# 7. Silver curation molecules

## M13 - Create Silver Curated Dataset

### Purpose

Create a reusable curated dataset that is standardised, governed and suitable for downstream use.

### SA-level meaning

A trusted intermediate data product is created for reuse.

### Implementation mapping

- Silver Lakehouse table;
- Delta table convention;
- transformation notebook;
- metadata registration;
- DQ results;
- lineage capture.

---

## M14 - Deduplicate Records

### Purpose

Identify and resolve duplicate records.

### SA-level meaning

The dataset applies agreed rules to avoid duplicate business entities or events.

### Implementation mapping

- matching logic;
- survivorship rules;
- duplicate report;
- exception path;
- steward review where required.

---

## M15 - Apply Slowly Changing Dimension Logic

### Purpose

Preserve historical changes where required.

### SA-level meaning

The data keeps history in a controlled way rather than overwriting important changes.

### Implementation mapping

- SCD Type 1 / Type 2 configuration;
- surrogate key generation;
- effective dates;
- current flag;
- reusable transformation code.

---

# 8. Gold modelling and publication molecules

## M16 - Build Gold Dimensional Model

### Purpose

Create a Gold dimensional model for analytics and reporting.

### SA-level meaning

A reporting-ready model is created using facts, dimensions and agreed business definitions.

### Implementation mapping

- Fabric Warehouse or Lakehouse tables;
- fact/dimension design;
- semantic model preparation;
- surrogate keys;
- aggregation approach;
- performance considerations.

---

## M17 - Build Gold Operational Reporting Model

### Purpose

Create a Gold model optimised for operational reporting rather than full analytical dimensional modelling.

### SA-level meaning

A controlled, report-ready dataset is created for operational reporting needs.

### Implementation mapping

- curated reporting table/view;
- refresh schedule;
- report-specific but governed transformations;
- access controls;
- semantic model where required.

---

## M18 - Publish Semantic Model

### Purpose

Expose data through a governed semantic model.

### SA-level meaning

Users consume consistent measures, dimensions and business definitions.

### Implementation mapping

- Power BI semantic model;
- Direct Lake or Import mode decision;
- measure definitions;
- RLS/OLS where needed;
- endorsement/certification process.

---

# 9. Governance and Purview molecules

## M19 - Register Data Product in Purview

### Purpose

Register the data product and key assets in Purview.

### SA-level meaning

The data product is discoverable, owned and traceable.

### Implementation mapping

- Purview asset registration;
- collection assignment;
- owner/steward metadata;
- glossary term linkage;
- classification;
- lineage.

---

## M20 - Link to Business Glossary

### Purpose

Associate data attributes and products with approved business terms.

### SA-level meaning

Consumers understand the business meaning of the data.

### Implementation mapping

- Purview glossary terms;
- term-to-column mapping;
- steward review;
- approval workflow.

---

## M21 - Capture Lineage

### Purpose

Capture technical and business lineage across source, Bronze, Silver, Gold and consumption layers.

### SA-level meaning

Users can understand where the data came from and how it was transformed.

### Implementation mapping

- Fabric lineage;
- Purview scans;
- manual lineage enrichment where required;
- pipeline/notebook metadata;
- lineage review.

---

# 10. Security and access molecules

## M22 - Apply Access Policy

### Purpose

Apply approved access controls to a dataset or data product.

### SA-level meaning

Only authorised users can access the data, in line with classification and purpose.

### Implementation mapping

- Entra ID groups;
- Fabric workspace roles;
- item-level permissions;
- RLS/OLS;
- sensitivity labels;
- access approval workflow.

---

## M23 - Classify Sensitive Data

### Purpose

Identify and classify sensitive data.

### SA-level meaning

Sensitive data is recognised and controlled appropriately.

### Implementation mapping

- Purview classification;
- sensitivity labels;
- data protection review;
- restricted access pattern;
- masking/anonymisation where required.

---

# 11. Operational and support molecules

## M24 - Monitor Pipeline Run

### Purpose

Monitor pipeline execution and failures.

### SA-level meaning

The data process is observable and supportable.

### Implementation mapping

- run metadata;
- status table;
- alerting;
- dashboard;
- support runbook.

---

## M25 - Monitor Freshness

### Purpose

Track whether data is up to date against expected SLA/frequency.

### SA-level meaning

Consumers can understand whether the data is current enough to use.

### Implementation mapping

- freshness metadata;
- expected schedule configuration;
- alert thresholds;
- dashboard;
- product health indicator.

---

## M26 - Reconcile Counts and Totals

### Purpose

Compare source and target record counts or business totals.

### SA-level meaning

The platform checks whether data movement and transformation are complete and trustworthy.

### Implementation mapping

- reconciliation rules;
- source/target count capture;
- tolerance thresholds;
- exception reporting;
- DQ issue linkage.

---

## M27 - Reprocess Data

### Purpose

Re-run failed or corrected data loads safely.

### SA-level meaning

Data can be corrected or reloaded without uncontrolled manual intervention.

### Implementation mapping

- reprocessing parameter;
- idempotent load design;
- versioning;
- rollback/overwrite policy;
- audit trail.

---

# 12. Consumption molecules

## M28 - Expose to Power BI

### Purpose

Expose the data product for Power BI reporting.

### SA-level meaning

Report developers consume an approved and governed dataset.

### Implementation mapping

- semantic model;
- workspace assignment;
- access groups;
- endorsement/certification;
- refresh configuration;
- usage monitoring.

---

## M29 - Provide Data Extract

### Purpose

Provide an approved extract to a consuming system or user group.

### SA-level meaning

Data is shared in a controlled and repeatable way.

### Implementation mapping

- extract pipeline;
- file/API/share mechanism;
- security controls;
- audit logging;
- retention policy.

---

## M30 - Publish Data Product Contract

### Purpose

Define the contract for a governed data product.

### SA-level meaning

Consumers understand what the product provides, who owns it, how often it updates, and how it should be used.

### Implementation mapping

- data product metadata;
- schema definition;
- SLA/freshness definition;
- owner/steward details;
- known limitations;
- access route;
- Purview registration.
