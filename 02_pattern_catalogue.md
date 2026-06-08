# Proposed Data Lake Pattern Catalogue

## 1. Purpose

This is the main pattern catalogue defining the standard architectural patterns for data lake design and operations.

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

**Standardized Ingestion Gateway**: To enforce consistent security, auditability, metadata logging, and schema enforcement, all ingestion workloads enter the platform through a single unified entry point: a secure, platform-exposed RESTful Ingestion API. Whether data is pushed directly by the source system or pulled by an orchestrator/extraction agent, the final step in any ingestion pattern is a POST call to this Ingestion API, which then writes the validated payload to the Bronze layer.

---

## A1. File to Bronze

### Purpose
Load files from a source system, external partner, or controlled location into the Bronze/raw layer via the RESTful Ingestion API.

### Typical use cases
- Scheduled CSV, Excel, JSON, XML or Parquet extracts.
- External partner or third-party files.
- Legacy system exports.
- File-based reporting migration extracts.

### Key design questions
- Does the source system push the file directly to the RESTful Ingestion API, or does a file-watcher/extraction agent pull and forward it?
- How is the file payload chunked/streamed when posting to the API?
- Is the file full load, delta or incremental?
- What happens if the API call fails or is rate-limited?
- Does the file include sensitive data requiring payload-level encryption?

### Expected low-level mapping
- Source or extraction agent making HTTP POST requests to the platform's RESTful Ingestion API.
- API gateway authentication and authorization (e.g., OAuth2 / API keys).
- Ingestion API handler writing the file stream to Bronze Lakehouse storage.
- Metadata capture (file name, size, hash, load time) logged by the API.
- Schema capture and validation at the API gateway.
- API error response handling and client-side retry logic.
- Audit, lineage, and alerting for missing or failed file pushes.

---

## A2. API to Bronze

### Purpose
Ingest data from a source API into the Bronze/raw layer by routing it through the platform's RESTful Ingestion API.

### Typical use cases
- SaaS platform API extraction.
- Internal enterprise API consumption.
- External reference data API.
- Scheduled API pulls for reporting/analytics.

### Key design questions
- Does the source system call our RESTful Ingestion API directly (push model), or does an orchestration process fetch the source API and forward the payload to our Ingestion API (pull model)?
- Is pagination, rate-limiting, or filtering handled at the source fetch layer before sending to our API?
- How are authentication secrets managed for both the source API and our Ingestion API?
- How are failed source API calls or destination Ingestion API calls retried?

### Expected low-level mapping
- Ingestion orchestrator (Fabric Pipeline/Notebook) or direct source push calling the platform's RESTful Ingestion API.
- Secret management (Azure Key Vault / Fabric Credentials) for API tokens.
- Parameterised endpoint configuration.
- Client-side pagination and batching before posting to the Ingestion API.
- Gateway logging of API metadata, client IP, payload size, and timestamp.
- Raw payload storage in Bronze Lakehouse via the Ingestion API backend.

---

## A3. Database Table to Bronze

### Purpose
Ingest one or more database tables into Bronze by forwarding table extracts through the RESTful Ingestion API.

### Typical use cases
- Operational reporting source extraction.
- Data migration from legacy reporting stores.
- Controlled source system replication.
- Incremental database loads using watermark columns.

### Key design questions
- How is the database extractor configured to batch records and POST them to the RESTful Ingestion API?
- Is the load full, incremental, or CDC (Change Data Capture)?
- What is the extraction frequency and its query/load impact on the source database?
- How is schema drift in the source database detected and handled at the Ingestion API?

### Expected low-level mapping
- Database extraction agent or pipeline pulling records and posting JSON/Parquet batches to the RESTful Ingestion API.
- Watermark or replication state configuration.
- Source query templates.
- API-driven validation of table schemas.
- Ingestion API backend writing records as Delta files in Bronze landing tables.
- API-level logging of row counts and reconciliation metrics.

---

## A4. Event Stream to Bronze

### Purpose
Ingest streaming or event-based data into the lake via the RESTful Ingestion API.

### Typical use cases
- Near-real-time operational events.
- Event-driven data products.
- Change events from systems.
- High-frequency telemetry or activity streams.

### Key design questions
- Can the event producer post events directly to the RESTful Ingestion API, or is an event hub / broker used as an intermediary?
- How does the Ingestion API handle high-frequency streaming concurrency and rate limits?
- Are duplicate events handled at the API gateway or downstream in Bronze?
- What event schema (e.g., CloudEvents) is enforced by the Ingestion API?

### Expected low-level mapping
- Event producer or broker router publishing event payloads to the platform's RESTful Ingestion API endpoint.
- API gateway rate limiting, throttling, and request buffering/queuing.
- Real-time schema validation by the API.
- Ingestion API backend writing streaming payloads to Bronze append-only tables.
- Lag monitoring and webhook-based alerting.

---

## A5. Manual Upload to Governed Landing Area

### Purpose
Allow authorised users to upload files manually, routing the upload through the RESTful Ingestion API to ensure compliance and governance.

### Typical use cases
- Controlled business uploads.
- Small reference datasets.
- Interim data collection before automated integration exists.
- Research/admin data where automated source systems are unavailable.

### Key design questions
- Does the upload UI call the RESTful Ingestion API directly?
- Who is authorised to trigger uploads, and how is identity verified at the API?
- What payload size limits are enforced by the Ingestion API for manual uploads?
- What automated file validation (malware scans, schema verification) runs on the API?

### Expected low-level mapping
- A secure web portal or UI client that uploads files via HTTP POST to the platform's RESTful Ingestion API.
- User authentication linked to Active Directory / Identity Provider.
- API-integrated file virus/malware scanning.
- File schema and format validation by the API backend.
- Metadata capture (uploader ID, upload time, original file name).
- Target Bronze landing storage.

---

## A6. External Partner/Third-Party Extract to Bronze

### Purpose
Ingest data provided by an external partner or third-party service by exposing the RESTful Ingestion API as the secure entry point.

### Typical use cases
- Managed service data extracts.
- Third-party application data.
- External partner-hosted operational systems.
- External benchmarking or enrichment data.

### Key design questions
- How does the external partner authenticate with the RESTful Ingestion API (e.g., dedicated API keys, OAuth clients)?
- What is the agreed data contract and payload schema enforced by the API?
- What are the IP whitelist or security constraints on the Ingestion API gateway?
- Who owns the external relationship, and how are API contract changes managed?

### Expected low-level mapping
- External partner client pushing data payloads directly to the platform's RESTful Ingestion API.
- Gateway IP filtering and Web Application Firewall (WAF) protections.
- API key or Client Credentials flow authentication.
- Data contract/schema validation at the API gateway.
- Ingestion API backend writing payloads to Bronze partner folders.
- External partner change notification and API versioning process.
- Operational support logs and alerts.

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
- Customer, student, or other business entity duplicates.
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
- External partner data sharing.

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
