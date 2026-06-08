# Proposed Data Lake Pattern Catalogue

## 1. Purpose

This is the main pattern catalogue defining the standard architectural patterns for data lake design and operations.

The catalogue is structured into five pattern groups:

1. Ingestion patterns
2. Transformation patterns
3. Data product patterns
4. Governance patterns
5. Operational patterns

## 2. How to read this catalogue

This document is the **index and scope**. Each entry is a deliberately short, consistent summary so a Solution Architect can scan and select patterns quickly.

Every entry uses the same shape:

- **Intent** — what it is for.
- **When to use** / **When not to use** — selection conditions.
- **Typical use cases**.
- **Key design questions** — the decisions a designer must make.
- **Typical composition** — patterns usually combined with it.
- **Leverage** — *Reusable asset* (configure) or *Guided design* (guardrails; design still required).
- **Status** — Draft | Proposed | Approved | Deprecated.

A **completed** pattern expands this summary into the full 15-section template in `03_pattern_template.md`. Worked examples that follow the template are in `04_example_patterns.md`. For help choosing patterns, see `07_pattern_selection_guide.md`.

> All patterns below are currently **Draft**. Defining them to the full template (with external partners) is the work this framework is scoping.

---

# A. Ingestion patterns

Ingestion patterns define how source data enters the Data Lake / Fabric platform and becomes available in the Bronze/raw layer.

## Standardized Ingestion Gateway

To enforce consistent security, auditability, metadata logging and schema enforcement, **all ingestion enters the platform through a single tech-agnostic contract: a secure, platform-exposed RESTful Ingestion API.**

**Why a single contract (federation).** With 300+ source systems, the platform team cannot operate a pipeline per system, and source teams must not be required to learn Fabric or any platform-internal technology. The Ingestion API is the stable boundary: a source team authenticates, sends its data per the published contract, and is done. It owns its data, its extraction and its push. The platform owns everything behind the contract — and can change the underlying technology (Fabric or its successor) without any source team being affected. This mirrors the Integration approach, where systems integrate against enterprise endpoints, not against each other.

**Ingestion modes (one contract, multiple transports).** The contract is uniform; the physical transport behind it adapts to the payload so the standard scales from a 1 KB record to a multi-GB extract to a high-frequency stream — without forcing any team off the standard:

- **Direct** — small/medium payloads are sent in the request body. (Default.)
- **Signed-URL handoff** — for large payloads, the source sends *metadata* and receives a short-lived signed upload location; it uploads the bulk data there. The platform ingests it. The source team still only knows the Ingestion API.
- **Streaming** — for high-frequency/near-real-time data, the contract returns a streaming endpoint/topic the source publishes to.

In all three modes the source experience is the same — authenticate, follow the contract, done — and no Fabric knowledge is required. This is the data-lake equivalent of Integration offering both Enterprise APIs **and** enterprise distribution channels: several transports under one coherent contract.

**Schema Registry (the contract store).** Every ingestion is validated against a versioned data contract held in a central **Schema Registry**: the agreed schema, owner/steward, classification and DQ expectations for that source. The gateway rejects or quarantines payloads that violate the active contract, and contract changes are versioned and governed. The Schema Registry is specified as a cross-cutting component in `06_cross_cutting_concerns.md`.

> The Ingestion API is both a **control plane** (authn/authz, contract validation, classification, audit, lineage initiation) and a **data plane** (it moves or brokers the bytes). Keeping the control responsibilities first-class is what makes federation safe; the ingestion modes are what keep the data plane performant. See `06_cross_cutting_concerns.md`.

---

### A1. File to Bronze
- **Intent.** Load files from a source system, partner or controlled location into Bronze via the Ingestion API.
- **When to use.** Scheduled or ad-hoc file extracts where the source can push (or an agent can forward) to the API.
- **When not to use.** Continuous high-frequency events (use A4); direct interactive uploads by humans (use A5).
- **Typical use cases.** CSV/Excel/JSON/XML/Parquet extracts; partner files; legacy exports; reporting migration extracts.
- **Key design questions.** Push by source or pull-and-forward by agent? Which ingestion mode (direct vs signed-URL for large files)? Full, delta or incremental? Payload-level encryption for sensitive data?
- **Typical composition.** A1 → B1 → B3 → D1 → E1 → E2.
- **Leverage.** Reusable asset. **Status.** Draft.

### A2. API to Bronze
- **Intent.** Ingest data from a source API into Bronze by routing it through the Ingestion API.
- **When to use.** SaaS/enterprise/reference APIs feeding the lake.
- **When not to use.** Bulk relational extracts better served by A3; event streams better served by A4.
- **Typical use cases.** SaaS extraction; internal enterprise API consumption; external reference data; scheduled analytical pulls.
- **Key design questions.** Source push or orchestrated pull-and-forward? Where are pagination/rate-limiting/filtering handled? Secret management for both the source API and the Ingestion API? Retry strategy on either hop?
- **Typical composition.** A2 → B1 → B3 → C1 → D1.
- **Leverage.** Reusable asset. **Status.** Draft.

### A3. Database Table to Bronze
- **Intent.** Ingest one or more database tables into Bronze by forwarding extracts through the Ingestion API.
- **When to use.** Operational reporting sources; controlled replication; incremental loads via watermark/CDC.
- **When not to use.** Where Fabric-native mirroring is the agreed approach for a given source (see Fabric-native-first stance, `06_cross_cutting_concerns.md`) — confirm build-vs-buy first.
- **Typical use cases.** Operational reporting extraction; migration from legacy stores; controlled replication; incremental watermark loads.
- **Key design questions.** How is the extractor configured to batch and POST (mode: direct vs signed-URL)? Full, incremental or CDC? Extraction frequency and source-load impact? Schema-drift detection at the gateway?
- **Typical composition.** A3 → B1 → C2 → E2 → E1.
- **Leverage.** Reusable asset. **Status.** Draft.

### A4. Event Stream to Bronze
- **Intent.** Ingest streaming/event data into the lake via the Ingestion API streaming mode.
- **When to use.** Near-real-time operational events, change events, high-frequency telemetry.
- **When not to use.** Low-frequency or batch data (use A1/A3); where exactly-once batch reconciliation is the primary need.
- **Typical use cases.** Operational events; event-driven products; change events; activity/telemetry streams.
- **Key design questions.** Direct publish or via broker/event hub? Concurrency and rate-limit handling? Duplicate handling at gateway or in Bronze? Event schema standard (e.g. CloudEvents) enforced via the Schema Registry?
- **Typical composition.** A4 → B3 → C4 → E1.
- **Leverage.** Reusable asset. **Status.** Draft.

### A5. Manual Upload to Governed Landing Area
- **Intent.** Let authorised users upload files through a governed UI that calls the Ingestion API.
- **When to use.** Controlled business uploads, small reference datasets, interim collection before automation exists.
- **When not to use.** Anything that should be a system-to-system feed; large or frequent loads.
- **Typical use cases.** Controlled business uploads; small reference data; interim data collection; research/admin data.
- **Key design questions.** Who is authorised and how is identity verified at the API? Payload-size limits? Malware scan and schema validation on upload?
- **Typical composition.** A5 → B3 → C1 → D1.
- **Leverage.** Reusable asset. **Status.** Draft.

### A6. External Partner / Third-Party Extract to Bronze
- **Intent.** Ingest partner/third-party data by exposing the Ingestion API as the secure external entry point.
- **When to use.** Managed-service, third-party-app or partner-hosted data under a data contract.
- **When not to use.** Internal sources (use A1–A4); one-off manual transfers (use A5).
- **Typical use cases.** Managed-service extracts; third-party application data; partner operational systems; benchmarking/enrichment data.
- **Key design questions.** Partner authentication (dedicated keys, OAuth client credentials)? Agreed data contract/schema in the Registry? IP allow-listing / WAF constraints? Who owns the relationship and contract versioning?
- **Typical composition.** A6 → B3 → B5 → D4 → E1.
- **Leverage.** Reusable asset. **Status.** Draft.

### A7. Unstructured / Large Object to Bronze
- **Intent.** Ingest unstructured or binary large objects (documents, images, audio, archives) into Bronze for downstream AI/analytics, via the Ingestion API signed-URL mode.
- **When to use.** Non-tabular content needed for search, extraction or ML; payloads too large for direct body transfer.
- **When not to use.** Structured/tabular data (use A1–A3); content with no governance or downstream use defined.
- **Typical use cases.** Document stores feeding AI extraction; image/scan archives; large media; bulk archive files.
- **Key design questions.** What metadata/classification is captured when the content itself isn't schema-validated? Virus/malware scanning? Storage tier and retention (link to D8)? Downstream feature/index build (link to B7)?
- **Typical composition.** A7 → B7 → C4 → D4 → D8.
- **Leverage.** Reusable asset. **Status.** Draft.

---

# B. Transformation patterns

Transformation patterns define how Bronze data becomes standardised, conformed, curated and trusted.

### B1. Bronze to Silver Standardisation
- **Intent.** Convert raw Bronze data into standardised Silver structures.
- **When to use.** Raw structures must be made consistent before modelling/governance.
- **When not to use.** Data already conformed; pass-through extracts with no transformation need.
- **Typical use cases.** Type conversion; column-naming standardisation; date/time normalisation; removal of source formatting; basic cleansing.
- **Key design questions.** Metadata-driven mapping vs bespoke? Standard null/date handling? How is transformation logic versioned and audited?
- **Typical composition.** A* → B1 → B2/B3 → C*.
- **Leverage.** Reusable asset. **Status.** Draft.

### B2. Silver Conformance to EDM / Domain Model
- **Intent.** Translate standardised Silver data into enterprise/domain language.
- **When to use.** Cross-portfolio consistency is required and a domain/enterprise data model exists to conform to.
- **When not to use.** No mature EDM/domain model exists yet (resolve the dependency first — see `08_operating_model.md`); strictly local/tactical reporting.
- **Typical use cases.** Mapping source fields to EDM concepts; glossary alignment; domain-level reusable entities.
- **Key design questions.** Does the target domain model exist and is it owned? Mapping configuration design? Steward review/sign-off route?
- **Typical composition.** B1 → B2 → C1/C2 → D2.
- **Leverage.** Guided design. **Status.** Draft.

### B3. Data Quality Validation and Quarantine
- **Intent.** Apply quality rules and separate invalid records for review/remediation.
- **When to use.** Any dataset with correctness or completeness requirements.
- **When not to use.** Throwaway exploration where DQ adds no value.
- **Typical use cases.** Mandatory-field, referential-integrity, range/format checks; duplicate detection; business-rule validation.
- **Key design questions.** Reusable rule framework vs bespoke? Quarantine location and review workflow? DQ score output and thresholds? Link to issue logging (D7)?
- **Typical composition.** B1 → B3 → (D7) → C*.
- **Leverage.** Reusable asset. **Status.** Draft.

### B4. Deduplication and Survivorship
- **Intent.** Identify duplicates and select the surviving record.
- **When to use.** Entity consolidation across sources; master/reference data preparation.
- **When not to use.** Single authoritative source with no duplication risk.
- **Typical use cases.** Person/entity matching; customer/student duplicates; multi-source consolidation.
- **Key design questions.** Matching and survivorship rules? Exception review output? Audit trail and steward approval?
- **Typical composition.** B3 → B4 → B5 → C1.
- **Leverage.** Guided design. **Status.** Draft.

### B5. Reference Data Enrichment
- **Intent.** Enrich datasets using controlled reference data.
- **When to use.** Code-to-description, hierarchy or lookup enrichment from governed reference sources.
- **When not to use.** Reference data ungoverned or unversioned (register it first).
- **Typical use cases.** Code/description mapping; org hierarchy; academic year/term; location/department/cost-centre lookup.
- **Key design questions.** Reference-source registration and versioning? Handling unmapped values? Stewardship of the reference data?
- **Typical composition.** B1 → B5 → C*.
- **Leverage.** Reusable asset. **Status.** Draft.

### B6. Slowly Changing Dimension Handling
- **Intent.** Manage historical change in dimensional data.
- **When to use.** History/trend analysis requires preserving prior states.
- **When not to use.** Only current state is needed (Type 1 / overwrite).
- **Typical use cases.** Department/course/programme/staff change over time; Gold dimensional modelling; historical reporting.
- **Key design questions.** SCD Type 1 vs 2 per attribute? Effective-date and surrogate-key strategy? Change detection and reconciliation?
- **Typical composition.** B2 → B6 → C2/C3.
- **Leverage.** Guided design. **Status.** Draft.

### B7. Feature Engineering for ML
- **Intent.** Transform curated data (and unstructured content from A7) into governed features for machine learning and AI.
- **When to use.** Reusable features are needed across models or teams; ML must use governed, lineage-tracked inputs.
- **When not to use.** One-off exploratory modelling with no reuse or governance requirement.
- **Typical use cases.** Shared feature store tables; text/embedding extraction from documents; aggregated behavioural features.
- **Key design questions.** Point-in-time correctness / leakage avoidance? Feature versioning and reuse? Lineage from source to feature to model? Refresh cadence and serving (batch vs online)?
- **Typical composition.** B1/A7 → B7 → C4 → D1/D6.
- **Leverage.** Guided design. **Status.** Draft.

---

# C. Data product patterns

Data product patterns define how trusted data is packaged and published for consumption.

### C1. Silver Curated Data Product
- **Intent.** Publish reusable, curated Silver data for multiple downstream uses.
- **When to use.** A domain dataset will be reused by several reports/products.
- **When not to use.** Single-consumer, single-use extract.
- **Typical use cases.** Domain reusable dataset; shared input to Gold modelling.
- **Key design questions.** Ownership and freshness metadata? Access controls? Purview registration scope?
- **Typical composition.** B1/B2 → C1 → D1 → C2/C6.
- **Leverage.** Reusable asset. **Status.** Draft.

### C2. Gold Star Schema Model
- **Intent.** A classic dimensional model (central fact + flat denormalised dimensions) for high-performance BI.
- **When to use.** Repeatable, governed reporting with shared/conformed dimensions.
- **When not to use.** Deep hierarchies better normalised (C3); petabyte/streaming scale (C4); one-off extracts (C5).
- **Typical use cases.** Enterprise BI dashboards; financial/sales/student metrics; cross-domain analysis on conformed dimensions.
- **Key design questions.** Dimensions fully denormalised for performance? SCD requirements per dimension? Surrogate vs business keys for Direct Lake? Fact/dimension referential integrity?
- **Typical composition.** B2 → B6 → C2 → C6 → D1 → E4.
- **Leverage.** Guided design. **Status.** Draft.

### C3. Gold Snowflake Schema Model
- **Intent.** A normalised dimensional model where large/hierarchical dimensions are split into sub-dimensions.
- **When to use.** Deep branching hierarchies; very wide dimensions; sub-dimensions reused across models.
- **When not to use.** Performance-critical BI where joins hurt (prefer C2); simple models.
- **Typical use cases.** Product→Subcategory→Category; org charts; reusable standardised sub-dimensions.
- **Key design questions.** Does normalisation create a query bottleneck? Are views/cached models used to hide joins? How are parent-child updates coordinated?
- **Typical composition.** B2 → B6 → C3 → C6 → D1.
- **Leverage.** Guided design. **Status.** Draft.

### C4. Gold Big Data / Denormalised Model (One Big Table / OBT)
- **Intent.** A fully denormalised single-table (or distributed, e.g. Data Vault) model for petabyte-scale analytics, streaming and ML feature stores.
- **When to use.** High-velocity events/IoT/clickstream; ML feature scans; log analysis where star joins are too costly; high-concurrency archival reads.
- **When not to use.** Standard governed BI with shared dimensions (prefer C2/C3).
- **Typical use cases.** Event/IoT/clickstream stores; ML feature stores; log analytics; raw history archival.
- **Key design questions.** Partition-key strategy for pruning? Z-Order / V-Order / liquid clustering? Append-only vs Delta merge?
- **Typical composition.** A4/B7 → C4 → D4 → E6.
- **Leverage.** Guided design. **Status.** Draft.

### C5. Gold Flat Operational Reporting Model
- **Intent.** A flattened reporting model where dimensional modelling is not appropriate or not yet required.
- **When to use.** Operational dashboards; shorter-lived or transitional reporting.
- **When not to use.** Strategic, reusable analytics needing conformed dimensions (use C2).
- **Typical use cases.** Operational dashboards; simple business extracts; transitional migration reporting.
- **Key design questions.** Controlled schema and access? Lifecycle classification (tactical/interim/strategic)? DQ and freshness checks?
- **Typical composition.** B1 → C5 → C6 → E4.
- **Leverage.** Reusable asset. **Status.** Draft.

### C6. Semantic Model / Power BI Dataset
- **Intent.** Publish a governed semantic model for Power BI reporting.
- **When to use.** Certified, reusable metrics and shared business definitions are needed.
- **When not to use.** Throwaway personal reports.
- **Typical use cases.** Certified datasets; reusable measures; controlled report development.
- **Key design questions.** Direct Lake vs import? Measure/calculation standards? Ownership, certification and endorsement?
- **Typical composition.** C2/C3/C5 → C6 → D5.
- **Leverage.** Guided design. **Status.** Draft.

### C7. Direct Lake Reporting Pattern
- **Intent.** Use Direct Lake to give Power BI access to Fabric data with reduced duplication and improved performance.
- **When to use.** Fabric-native reporting over large Lakehouse/Warehouse datasets.
- **When not to use.** Where import mode better fits modelling/refresh constraints.
- **Typical use cases.** Strategic Power BI over Lakehouse/Warehouse; large datasets unsuited to import.
- **Key design questions.** Lakehouse/Warehouse design constraints for Direct Lake? Capacity monitoring? Security/access model?
- **Typical composition.** C2/C4 → C7 → E6.
- **Leverage.** Guided design. **Status.** Draft.

### C8. Data Extract Publication Pattern
- **Intent.** Publish controlled extracts to downstream consumers.
- **When to use.** Regulatory/statutory extracts; sharing to another platform; controlled operational extracts.
- **When not to use.** Reusable analytical consumption (use a data product + semantic model).
- **Typical use cases.** Statutory extracts; platform-to-platform sharing; external partner data sharing.
- **Key design questions.** Output as file/table/API? Approval and access control? Retention/deletion and data-sharing-agreement linkage?
- **Typical composition.** C1/C2 → C8 → D5 → D8.
- **Leverage.** Reusable asset. **Status.** Draft.

### C9. Cross-Domain / Data Sharing Product
- **Intent.** Publish a governed data product for consumption by *other domains/teams* with an explicit contract — the data-mesh / cross-domain sharing case.
- **When to use.** A domain's product becomes a dependency for other domains and needs a stable, owned interface and SLA.
- **When not to use.** Internal-only datasets with no cross-domain consumer; ad-hoc one-off shares (use C8).
- **Typical use cases.** Shared conformed dimensions; a domain's canonical entity reused enterprise-wide; published analytical products with consumer SLAs.
- **Key design questions.** What is the consumer-facing contract and version policy? SLA/freshness commitments (link E4)? Discoverability and access approval (D1/D5)? Who owns consumer support?
- **Typical composition.** C1/C2 → C9 → D1/D2/D5 → E4.
- **Leverage.** Guided design. **Status.** Draft.

---

# D. Governance patterns

Governance patterns define how data is made trustworthy, understandable, owned and controlled. Governance must be **embedded and, where possible, enforced** — see the enforcement model in `06_cross_cutting_concerns.md`.

### D1. Purview Registration
- **Intent.** Register datasets, tables, pipelines, semantic models and data products in Purview.
- **When to use.** Every reusable data asset.
- **When not to use.** Transient scratch data with no reuse (still log it).
- **Key design questions.** Collection structure? Automated vs manual scan? Ownership/lineage/classification metadata captured?
- **Typical composition.** Any pattern → D1 → D2 → D3 → D6.
- **Leverage.** Reusable asset. **Status.** Draft.

### D2. Business Glossary Linkage
- **Intent.** Connect assets to approved business terms.
- **When to use.** Any governed data product.
- **Key design questions.** Term mapping and domain assignment? Steward approval and version process?
- **Typical composition.** D1 → D2.
- **Leverage.** Reusable asset. **Status.** Draft.

### D3. Ownership / Stewardship Assignment
- **Intent.** Ensure every data product has clear business and technical accountability.
- **When to use.** Every data product (enforced before publication).
- **Key design questions.** Owner/steward/custodian named? RACI linkage and review cadence?
- **Typical composition.** D1 → D3.
- **Leverage.** Reusable asset. **Status.** Draft.

### D4. Data Classification
- **Intent.** Classify data by sensitivity, protection needs and usage constraints.
- **When to use.** All ingested and published data (set at the Schema Registry contract where possible).
- **Key design questions.** Classification rules and sensitivity labels? Protection-review triggers? Access-policy mapping?
- **Typical composition.** A*/D1 → D4 → D5.
- **Leverage.** Reusable asset. **Status.** Draft.

### D5. Access Approval
- **Intent.** Define and manage how users gain access to data products.
- **When to use.** Every access grant to governed data.
- **Key design questions.** Access groups and RBAC model? Approval workflow and least privilege? Periodic re-certification?
- **Typical composition.** D4 → D5.
- **Leverage.** Reusable asset. **Status.** Draft.

### D6. Lineage Capture
- **Intent.** Capture how data flows from source to product and report.
- **When to use.** All governed flows.
- **Key design questions.** Pipeline/notebook/table/report lineage captured automatically? Purview integration? Lineage starts at the Ingestion gateway?
- **Typical composition.** D1 → D6.
- **Leverage.** Reusable asset. **Status.** Draft.

### D7. Data Quality Issue Logging
- **Intent.** Record, prioritise and track DQ issues discovered through patterns.
- **When to use.** Wherever B3/B4 run.
- **Key design questions.** Issue-log integration and severity/ownership? Link to failed rule output? Trend reporting?
- **Typical composition.** B3/B4 → D7.
- **Leverage.** Reusable asset. **Status.** Draft.

### D8. Retention, Archival and Deletion
- **Intent.** Apply lifecycle policy to data — how long it is kept, when it is archived, and how it is securely deleted — across Bronze/Silver/Gold and extracts.
- **When to use.** All data, especially personal, regulated or contractually constrained data.
- **When not to use.** Never skipped; minimal policy still applies to scratch data.
- **Typical use cases.** GDPR/records-retention compliance; cost-driven archival of cold data; deletion on contract end (link C8/C9); right-to-erasure handling.
- **Key design questions.** Retention period and legal basis per dataset/classification? Archive tiering vs deletion? Erasure propagation across layers and lineage? Evidence/audit of deletion?
- **Typical composition.** D4 → D8 (applies across A*/C*).
- **Leverage.** Reusable asset. **Status.** Draft.

### D9. Schema Contract & Evolution Management
- **Intent.** Govern the data contracts in the Schema Registry — how a source's schema is agreed, versioned and evolved without breaking downstream consumers.
- **When to use.** Every ingestion source and every cross-domain product (C9).
- **When not to use.** Never skipped for governed sources.
- **Typical use cases.** Onboarding a new source contract; handling source schema drift; coordinating a breaking change with consumers.
- **Key design questions.** Backward/forward compatibility policy? Who approves a contract change and how are consumers notified? Versioning scheme and deprecation window? Drift detection at the gateway (link A1–A6)?
- **Typical composition.** A* (gateway) ↔ D9 ↔ C9.
- **Leverage.** Reusable asset. **Status.** Draft.

---

# E. Operational patterns

Operational patterns define how Data Lake solutions are run, monitored, recovered, deployed and supported.

### E1. Monitoring and Alerting
- **Intent.** Monitor pipelines, notebooks, freshness, failures and DQ outputs.
- **When to use.** Every production solution.
- **Key design questions.** Standard log tables and alert rules? Operational dashboard? Incident routing?
- **Typical composition.** All patterns → E1.
- **Leverage.** Reusable asset. **Status.** Draft.

### E2. Reconciliation
- **Intent.** Verify that data movement and transformation are complete and accurate.
- **When to use.** Important/critical datasets.
- **When not to use.** Low-value transient data.
- **Key design questions.** Source-to-target counts and aggregate checks? Reconciliation tables and exception reporting? Sign-off for critical data?
- **Typical composition.** A*/B*/C* → E2 → E1.
- **Leverage.** Reusable asset. **Status.** Draft.

### E3. Retry and Reprocessing
- **Intent.** Provide controlled recovery when ingestion/transformation fails.
- **When to use.** Any pipeline that can fail (i.e. all).
- **Key design questions.** Retry rules and idempotency? Reprocessing/backfill parameters? Failure isolation and audit?
- **Typical composition.** A*/B* → E3 → E1.
- **Leverage.** Reusable asset. **Status.** Draft.

### E4. SLA / Freshness Tracking
- **Intent.** Track whether data products update within expected timeframes.
- **When to use.** Any product with a consumer expectation or SLA (especially C9).
- **Key design questions.** Freshness metadata and SLA thresholds? Late-data alerting and owner notification? Consumer-facing status?
- **Typical composition.** C* → E4 → E1.
- **Leverage.** Reusable asset. **Status.** Draft.

### E5. Deployment and Environment Promotion
- **Intent.** Move patterns and implementations consistently across environments.
- **When to use.** All solutions (Dev/Test/Prod).
- **Key design questions.** Workspace strategy and deployment pipelines? Terraform/IaC and parameterised config? Release approval and version control? Governance gates enforced at promotion (link `06_cross_cutting_concerns.md`)?
- **Typical composition.** All patterns → E5.
- **Leverage.** Reusable asset. **Status.** Draft.

### E6. Cost / Capacity Monitoring
- **Intent.** Monitor and manage Fabric capacity and cost impact.
- **When to use.** All workloads, especially C4/C7.
- **Key design questions.** Capacity metrics and workload attribution? Cost/performance dashboard and threshold alerts? Efficient-design guidance?
- **Typical composition.** C4/C7 → E6.
- **Leverage.** Reusable asset. **Status.** Draft.
