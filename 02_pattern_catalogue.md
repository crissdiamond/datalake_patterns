# Proposed Data Lake Pattern Catalogue

## 1. Purpose

This is the main pattern catalogue that Deloitte and Simpson should respond to.

The catalogue is structured into five pattern groups:

1. Ingestion patterns
2. Transformation patterns
3. Data product patterns
4. Governance patterns
5. Operational patterns

Each pattern should eventually have:

- a high-level architecture description for Solution Architects;
- clear conditions for when to use and when not to use it;
- governance and assurance requirements;
- Fabric/Purview implementation mapping;
- reusable building blocks/molecules;
- configuration options;
- acceptance criteria;
- example implementation.

---

# A. Ingestion patterns

Ingestion patterns define how source data enters the Data Lake / Fabric platform and becomes available in the Bronze/raw layer.

## A1. File to Bronze

### Purpose
Load files from a source system, supplier or controlled location into the Bronze/raw layer.

### Typical use cases
- Scheduled CSV, Excel, JSON, XML or Parquet extracts.
- Supplier-provided files.
- Legacy system exports.
- Reporting migration where existing extracts are used as an interim source.

### Key design questions
- Is the file pushed or pulled?
- Is the file full load, delta or incremental?
- Is there a naming convention and delivery schedule?
- Does the file include sensitive data?
- What happens if the file is late, malformed or duplicated?

### Expected low-level mapping
- Fabric Data Pipeline or Data Factory pipeline.
- Bronze Lakehouse storage.
- Metadata capture table.
- Schema capture and validation.
- Audit and lineage logging.
- Error/quarantine area.
- Alerting for missing or failed files.

---

## A2. API to Bronze

### Purpose
Ingest data from an API into the Bronze/raw layer.

### Typical use cases
- SaaS platform API extraction.
- Internal enterprise API consumption.
- External reference data API.
- Scheduled API pulls for reporting/analytics.

### Key design questions
- Is the API synchronous or asynchronous?
- Is pagination required?
- Is there rate limiting?
- Is authentication standardised?
- Is the source payload stable and versioned?
- How are failed calls retried?

### Expected low-level mapping
- Fabric pipeline or notebook-based API extraction.
- Secret management.
- Parameterised endpoint configuration.
- Pagination/retry framework.
- Raw response storage.
- Metadata, audit and error logging.

---

## A3. Database Table to Bronze

### Purpose
Ingest one or more database tables into Bronze.

### Typical use cases
- Operational reporting source extraction.
- Data migration from legacy reporting stores.
- Controlled source system replication.
- Incremental loads using watermark columns.

### Key design questions
- Is the load full, incremental or CDC?
- What is the extraction frequency?
- Is the source system operationally sensitive?
- What is the query/load impact?
- What is the agreed data contract?

### Expected low-level mapping
- Fabric Data Pipeline / Data Factory connector.
- Watermark configuration.
- Source query template.
- Bronze landing table/files.
- Load audit table.
- Reconciliation counts.
- Error and retry handling.

---

## A4. Event Stream to Bronze

### Purpose
Ingest streaming or event-based data into the lake.

### Typical use cases
- Near-real-time operational events.
- Event-driven data products.
- Change events from systems.
- High-frequency telemetry or activity streams.

### Key design questions
- Is real-time needed, or would scheduled ingestion suffice?
- What event schema is used?
- Is ordering important?
- Are duplicate events possible?
- What is the retention and replay model?

### Expected low-level mapping
- Event stream / Event Hub / Fabric Real-Time Intelligence, as appropriate.
- Raw event capture.
- Schema validation.
- Deduplication approach.
- Replay handling.
- Monitoring and lag alerting.

---

## A5. Manual Upload to Governed Landing Area

### Purpose
Allow authorised users to upload files into a governed landing area.

### Typical use cases
- Controlled business uploads.
- Small reference datasets.
- Interim data collection before system integration exists.
- Research/admin data where source system automation is not available.

### Key design questions
- Who is authorised to upload?
- Is upload temporary or strategic?
- What validation is required before data enters Bronze?
- What is the approval process?
- How is misuse prevented?

### Expected low-level mapping
- Controlled landing workspace/location.
- Access group and upload permissions.
- File validation process.
- Metadata capture.
- Approval workflow where required.
- Audit trail.

---

## A6. External Vendor Extract to Bronze

### Purpose
Ingest data provided by an external vendor into Bronze.

### Typical use cases
- Managed service data extracts.
- Third-party application data.
- Vendor-hosted operational systems.
- External benchmarking or enrichment data.

### Key design questions
- What is the contractual delivery mechanism?
- What is the agreed data contract?
- What are the security and data protection constraints?
- Who owns the vendor relationship?
- What happens if extract format changes?

### Expected low-level mapping
- Secure transfer mechanism.
- Landing and validation pipeline.
- Data contract/schema validation.
- Exception handling.
- Supplier change notification process.
- Audit, lineage and support model.

---

# B. Transformation patterns

Transformation patterns define how Bronze data becomes standardised, conformed, curated and trusted.

## B1. Bronze to Silver Standardisation

### Purpose
Convert raw Bronze data into standardised Silver structures.

### Typical use cases
- Type conversion.
- Column naming standardisation.
- Date/time standardisation.
- Removal of source-specific formatting.
- Basic cleansing.

### Expected low-level mapping
- Reusable transformation notebook.
- Metadata-driven mapping rules.
- Standard output table format.
- Audit and error handling.
- Versioned transformation logic.

---

## B2. Silver Conformance to EDM / Domain Model

### Purpose
Translate standardised Silver data into UCL enterprise/domain language.

### Typical use cases
- Mapping source-specific fields to Enterprise Data Model concepts.
- Aligning with Business Glossary terms.
- Creating domain-level reusable entities.
- Supporting cross-portfolio consistency.

### Expected low-level mapping
- Mapping configuration.
- Domain model table design.
- Business glossary linkage.
- Transformation notebooks/libraries.
- Data Steward review/sign-off.

---

## B3. Data Quality Validation and Quarantine

### Purpose
Apply quality rules and separate invalid records for review or remediation.

### Typical use cases
- Mandatory field checks.
- Referential integrity checks.
- Range and format checks.
- Duplicate detection.
- Business rule validation.

### Expected low-level mapping
- Reusable data quality rule framework.
- Configurable rule sets.
- Quarantine tables/locations.
- Data quality score/output.
- Issue logging and alerting.
- Data Steward review workflow.

---

## B4. Deduplication and Survivorship

### Purpose
Identify duplicate records and determine the preferred/surviving record.

### Typical use cases
- Person/entity matching.
- Supplier/customer/student duplicates.
- Multiple source consolidation.
- Master/reference data preparation.

### Expected low-level mapping
- Matching rules/configuration.
- Survivorship rules.
- Exception review output.
- Audit trail of record decisions.
- Data Steward approval where required.

---

## B5. Reference Data Enrichment

### Purpose
Enrich datasets using controlled reference data.

### Typical use cases
- Code-to-description mapping.
- Organisational hierarchy enrichment.
- Academic year/term enrichment.
- Location, department or cost centre lookup.

### Expected low-level mapping
- Reference data source registration.
- Join/enrichment notebook.
- Versioned reference data tables.
- Data quality checks for unmapped values.
- Stewardship of reference data.

---

## B6. Slowly Changing Dimension Handling

### Purpose
Manage historical changes in dimensional data.

### Typical use cases
- Department, course, programme, staff or organisational changes over time.
- Gold dimensional modelling.
- Historical reporting and trend analysis.

### Expected low-level mapping
- SCD Type 1 / Type 2 logic.
- Effective date handling.
- Surrogate key management.
- Change detection.
- Audit and reconciliation.

---

# C. Data product patterns

Data product patterns define how trusted data is packaged and published for consumption.

## C1. Silver Curated Data Product

### Purpose
Publish reusable, curated Silver data for multiple downstream uses.

### Typical use cases
- Domain-level reusable dataset.
- Shared data product used by multiple reports or products.
- Data prepared for Gold modelling.

### Expected low-level mapping
- Curated Lakehouse table.
- Ownership/stewardship metadata.
- Purview registration.
- Access controls.
- Data quality score and freshness metadata.

---

## C2. Gold Dimensional Model

### Purpose
Create a dimensional model for trusted reporting and analytics.

### Typical use cases
- Star schema.
- Fact and dimension model.
- Conformed dimensions.
- Strategic or operational reporting layer.

### Expected low-level mapping
- Fabric Warehouse or Lakehouse tables.
- Fact/dimension model.
- SCD handling where required.
- Semantic model alignment.
- Reconciliation and performance checks.

---

## C3. Gold Flat Operational Reporting Model

### Purpose
Create a flattened reporting model for operational reporting where dimensional modelling is not appropriate or not yet required.

### Typical use cases
- Operational dashboards.
- Shorter-lived reporting use cases.
- Simple extracts for business teams.
- Transitional reporting migration.

### Expected low-level mapping
- Flattened Gold table/view.
- Controlled schema.
- Access controls.
- Data quality and freshness checks.
- Clear lifecycle classification: tactical, interim or strategic.

---

## C4. Semantic Model / Power BI Dataset

### Purpose
Publish a governed semantic model for Power BI reporting.

### Typical use cases
- Certified Power BI dataset.
- Reusable metrics and measures.
- Controlled report development.
- Shared business definitions.

### Expected low-level mapping
- Power BI semantic model.
- Direct Lake or import mode decision.
- Measures and calculation standards.
- Dataset ownership.
- Certification process.
- Access and endorsement model.

---

## C5. Direct Lake Reporting Pattern

### Purpose
Use Direct Lake to provide Power BI access to Fabric data with reduced duplication and improved performance.

### Typical use cases
- Fabric-native reporting.
- Large datasets where import is not ideal.
- Strategic Power BI over Lakehouse/Warehouse.

### Expected low-level mapping
- Direct Lake configuration.
- Lakehouse/Warehouse design constraints.
- Semantic model design.
- Performance testing.
- Capacity monitoring.
- Security and access model.

---

## C6. Data Extract Publication Pattern

### Purpose
Publish controlled extracts to downstream consumers.

### Typical use cases
- Regulatory/statutory extracts.
- Data sharing with another platform.
- Controlled operational extracts.
- Supplier/partner data sharing.

### Expected low-level mapping
- Export pipeline.
- Output file/table/API pattern.
- Approval and access control.
- Audit trail.
- Data sharing agreement linkage.
- Retention and deletion controls.

---

# D. Governance patterns

Governance patterns define how data is made trustworthy, understandable, owned and controlled.

## D1. Purview Registration

### Purpose
Register datasets, tables, pipelines, semantic models and data products in Purview.

### Expected low-level mapping
- Purview collection structure.
- Asset registration.
- Automated or semi-automated scan/configuration.
- Ownership metadata.
- Lineage capture.
- Classification metadata.

---

## D2. Business Glossary Linkage

### Purpose
Connect data assets to approved business terms and definitions.

### Expected low-level mapping
- Glossary term mapping.
- Domain/sub-domain assignment.
- Steward approval.
- Data product documentation.
- Review/version process.

---

## D3. Ownership / Stewardship Assignment

### Purpose
Ensure every data product has clear business and technical accountability.

### Expected low-level mapping
- Data Owner field.
- Data Steward field.
- Data Custodian field.
- RACI / accountability matrix linkage.
- Review and approval process.

---

## D4. Data Classification

### Purpose
Classify data according to sensitivity, protection needs and usage constraints.

### Expected low-level mapping
- Classification rules.
- Sensitivity labels.
- Data protection review trigger.
- Access policy mapping.
- Security review requirement.

---

## D5. Access Approval

### Purpose
Define and manage how users gain access to data products.

### Expected low-level mapping
- Access groups.
- Role-based access controls.
- Approval workflow.
- Least privilege model.
- Audit trail.
- Periodic access review.

---

## D6. Lineage Capture

### Purpose
Capture how data flows from source to data product and report.

### Expected low-level mapping
- Pipeline lineage.
- Notebook transformation lineage.
- Table-to-table lineage.
- Semantic model/report lineage.
- Purview lineage integration.

---

## D7. Data Quality Issue Logging

### Purpose
Record, prioritise and track data quality issues discovered through patterns.

### Expected low-level mapping
- DQ issue log integration.
- Severity and ownership assignment.
- Link to failed rule/output.
- Steward workflow.
- Reporting of trends and remediation status.

---

# E. Operational patterns

Operational patterns define how Data Lake solutions are run, monitored, recovered, deployed and supported.

## E1. Monitoring and Alerting

### Purpose
Monitor pipelines, notebooks, freshness, failures and data quality outputs.

### Expected low-level mapping
- Standard log tables.
- Pipeline run monitoring.
- Alert rules.
- Dashboard for operational status.
- Incident routing.

---

## E2. Reconciliation

### Purpose
Verify that data movement and transformation are complete and accurate.

### Expected low-level mapping
- Source-to-target counts.
- Checksum/aggregate comparisons where appropriate.
- Reconciliation tables.
- Exception reporting.
- Approval/sign-off for critical data.

---

## E3. Retry and Reprocessing

### Purpose
Provide controlled recovery when ingestion or transformation fails.

### Expected low-level mapping
- Retry rules.
- Idempotent processing.
- Reprocessing parameters.
- Backfill process.
- Failure isolation.
- Audit trail.

---

## E4. SLA / Freshness Tracking

### Purpose
Track whether data products are updated within expected timeframes.

### Expected low-level mapping
- Data freshness metadata.
- SLA thresholds.
- Alerting for late data.
- Consumer-facing status.
- Owner notification.

---

## E5. Deployment and Environment Promotion

### Purpose
Move patterns and implementations consistently across environments.

### Expected low-level mapping
- Dev/Test/Prod workspace strategy.
- Deployment pipelines.
- Terraform/IaC modules.
- Parameterised configuration.
- Version control.
- Release approval.

---

## E6. Cost / Capacity Monitoring

### Purpose
Monitor and manage Fabric capacity and cost impact.

### Expected low-level mapping
- Capacity metrics.
- Workload attribution.
- Cost/performance dashboard.
- Threshold alerts.
- Design guidance for efficient usage.
