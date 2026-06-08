# Worked Example Data Lake Patterns

These five worked examples — one per pattern group — follow the canonical template in `03_pattern_template.md` exactly. They are the **quality bar**. When an internal team or external partner asks "what does a completed pattern look like?", this is the answer. Where a section is genuinely not yet decided, it says so explicitly rather than being omitted.

---

# A1 - File to Bronze

- **Group:** Ingestion · **Status:** Draft · **Leverage type:** Reusable asset · **Owner:** Data Platform · **Version:** 0.1

## 1. Intent
Load scheduled source files into the Bronze/raw layer in a controlled, auditable and repeatable way, with the source team owning its own extraction and push.

## 2. Pattern group
Ingestion.

## 3. When to use
The source can push a file (or a local agent can forward it) to the Ingestion API on a schedule or on demand, and the data is tabular/file-shaped.

## 4. When not to use
- Continuous high-frequency events → use A4 Event Stream to Bronze.
- Human interactive uploads → use A5 Manual Upload.
- Unstructured/large binary objects → use A7 (signed-URL mode).

## 5. Typical use cases
Scheduled CSV/Excel/JSON/XML/Parquet extracts; external partner files; legacy exports; reporting-migration extracts.

## 6. Solution Architect view
The source system (or a thin agent next to it) pushes the file to the platform's RESTful Ingestion API. The API authenticates the caller, validates the payload against the source's contract in the **Schema Registry**, scans it, and lands it in Bronze. For large files the API issues a short-lived **signed upload URL** (signed-URL ingestion mode) so the bulk bytes do not stream through the request body — the source team's experience is unchanged. No raw data lands without passing security and schema checks, and the source team never touches Fabric.

- **Key decisions:** push vs agent-forward; direct vs signed-URL mode; full/delta/incremental; encryption for sensitive payloads.
- **Ownership:** source team owns extraction and push; platform owns everything behind the API.
- **Dependency:** an active contract for this source must exist in the Schema Registry (see D9).

## 7. Pattern composition
```text
A1 File to Bronze (via Ingestion API)
→ B1 Bronze to Silver Standardisation
→ B3 Data Quality Validation and Quarantine
→ D1 Purview Registration
→ E1 Monitoring and Alerting
→ E2 Reconciliation
```

## 8. Reusable molecules / building blocks
```text
ING-C-01 Read source file
ING-C-04 Authenticate with Ingestion API
ING-C-05 POST payload (or request signed URL) to Ingestion API
ING-A-01 Authorize ingestion request
ING-A-02 Validate payload schema against Registry
ING-A-03 Virus/malware scan payload
ING-A-04 Write payload stream to Bronze
ING-A-05 Log gateway ingestion event
ING-A-06 Quarantine invalid payload
ING-A-07 Send ingestion failure alert
```

## 9. Fabric implementation mapping
- HTTP POST to the Ingestion API gateway (Fabric-native ingestion behind it; gateway is bespoke platform service).
- OAuth2 / secure API keys at the gateway.
- Schema validation against the central Schema Registry.
- Backend streams payloads to the Bronze Lakehouse; large files via signed-URL upload to staging then ingest.
- Gateway metadata logging (payload size, file hash, timestamp) to the standard ingestion log table.
- Quarantine storage for rejected payloads; telemetry dashboard.

## 10. Purview / governance mapping
- Data Owner/Steward set in the Schema Registry contract **before** ingestion (enforced).
- Source data contract versioned and registered (D9).
- Classification recorded (D4); Purview asset created for the Bronze dataset (D1).
- Lineage starts at the Ingestion API entry point (D6).

## 11. Configuration model
```yaml
pattern_id: A1
source_name: hr_extract
source_type: file
file_format: csv
ingestion_mode: direct        # direct | signed_url
load_frequency: daily
load_type: full
landing_zone: bronze/hr/extract
schema_contract: hr_extract@v3
schema_validation: true
data_owner: "[Named owner]"
data_steward: "[Named steward]"
classification: confidential
purview_registration: true
monitoring_profile: standard
```

## 12. Non-functional requirements
- **Security:** authenticated/authorised push; malware scan; encryption in transit; payload encryption for sensitive data.
- **Availability/scalability:** gateway scales across many sources; signed-URL mode handles large files without blocking.
- **Auditability:** every push logged with hash and metadata.
- **Retention:** per D8 policy for Bronze.
- **Cost/capacity:** monitored via E6.

## 13. Assurance checklist
- Is there an approved Schema Registry contract for this source?
- Are owner, steward and classification set before first ingestion?
- Is the correct ingestion mode chosen for the payload size?
- Is failure alerting and quarantine review owned by someone?

## 14. Acceptance criteria
A federated team can onboard a new file source by configuration only (no engineering), the gateway enforces contract + classification, and the asset appears in Purview with lineage from the gateway.

## 15. Example implementation
A daily HR CSV extract pushed by an agent beside the HR system; ~50 MB so `direct` mode; validated against `hr_extract@v3`; landed to `bronze/hr/extract`; reconciled by row count in E2.

---

# B1 - Bronze to Silver Standardisation

- **Group:** Transformation · **Status:** Draft · **Leverage type:** Reusable asset · **Owner:** Data Platform · **Version:** 0.1

## 1. Intent
Convert raw Bronze data into standardised Silver data with consistent naming, types, formats and basic cleansing.

## 2. Pattern group
Transformation.

## 3. When to use
Raw source structures need to be made consistent and reliable before domain conformance, modelling or governance.

## 4. When not to use
Data already standardised; pass-through extracts requiring no transformation.

## 5. Typical use cases
Type conversion; column-name standardisation; date/time normalisation; removal of source formatting; basic cleansing.

## 6. Solution Architect view
This makes raw source data usable for downstream modelling and governance. It does **not** create a Gold reporting model — it produces a standardised, more reliable representation of the source. *Leverage is configuration*: the SA points the reusable standardisation notebook at a mapping file rather than writing transformation code.

## 7. Pattern composition
```text
A1 File to Bronze
→ B1 Bronze to Silver Standardisation
→ B2 Silver Conformance to EDM / Domain Model
→ C1 Silver Curated Data Product
```

## 8. Reusable molecules / building blocks
```text
TRN-01 Standardise column names
TRN-02 Convert data types
TRN-03 Apply mapping rules
OPS-01 Log run
```

## 9. Fabric implementation mapping
- Reusable notebook using a shared transformation library (Fabric-native Spark; library is centrally managed).
- Mapping configuration from source fields to standardised fields.
- Standard type and date/time handling; standard null handling.
- Silver Lakehouse output table; transformation audit log table.

## 10. Purview / governance mapping
- Mapping rules reviewed by Data Steward or delegated SME.
- Glossary candidate terms identified (feeds D2).
- DQ rules identified for follow-on B3.
- Lineage captured Bronze → Silver (D6).

## 11. Configuration model
```yaml
pattern_id: B1
source_table: bronze.hr_extract
silver_table: silver.hr_standardised
mapping_file: mappings/hr_to_silver.yaml
standard_date_format: ISO-8601
standard_null_handling: true
audit_logging: true
```

## 12. Non-functional requirements
- **Auditability:** every run logged; transformation logic versioned.
- **Performance/cost:** Spark sizing per data volume (E6).
- **Supportability:** failures recoverable via E3.

## 13. Assurance checklist
- Is the mapping file reviewed and version-controlled?
- Are standard date/null conventions applied?
- Is lineage captured to Silver?

## 14. Acceptance criteria
A new source can be standardised by supplying a mapping file only; output conforms to platform Silver conventions; run is audited and lineage captured.

## 15. Example implementation
`bronze.hr_extract` standardised to `silver.hr_standardised` via `mappings/hr_to_silver.yaml`, ISO-8601 dates, audited.

---

# C2 - Gold Star Schema Model

- **Group:** Data Product · **Status:** Draft · **Leverage type:** Guided design · **Owner:** Data Architecture · **Version:** 0.1

## 1. Intent
Create a classic conformed dimensional model (Star Schema) of flat dimensions and facts to support strategic, governed reporting.

## 2. Pattern group
Data Product.

## 3. When to use
Data must support repeatable, reusable, governed reporting with shared/conformed dimensions.

## 4. When not to use
- Deep hierarchies better normalised → C3 Snowflake.
- Petabyte/streaming/ML scans → C4 OBT.
- One-off operational extract → C5 Flat Operational.

## 5. Typical use cases
Enterprise BI dashboards; financial/sales/student metrics; cross-domain analysis on conformed dimensions.

## 6. Solution Architect view
This defines facts, dimensions, measures, keys and historical behaviour for reusable, governed analytics. **Leverage type is guided design**: the framework supplies the modelling guardrails, decision questions, SCD building blocks and a reference implementation, but the dimensional design itself requires modelling judgement — it is not turnkey. Use it where output must be reusable and governed, not for one-off reports.

- **Dependency:** a conformed Silver/domain model (B2) and agreed grain.

## 7. Pattern composition
```text
B2 Silver Conformance to EDM / Domain Model
→ B6 Slowly Changing Dimension Handling
→ C2 Gold Star Schema Model
→ C6 Semantic Model / Power BI Dataset
→ D1 Purview Registration
→ E4 SLA / Freshness Tracking
```

## 8. Reusable molecules / building blocks
```text
DP-02 Create fact table
DP-03 Create dimension table
TRN-08 Apply SCD logic
DP-09 Apply surrogate key
DP-10 Calculate measure
OPS-02 Reconcile row counts
DP-05 Create semantic model
GOV-01 Register asset in Purview
```

## 9. Fabric implementation mapping
- Fabric Warehouse or Lakehouse Gold tables (fact + dimension) — Fabric-native.
- Surrogate-key handling; SCD Type 1/2 where required (B6).
- Power BI semantic model or Direct Lake model (C6/C7).
- Reconciliation Silver → Gold (E2).

## 10. Purview / governance mapping
- Data product owner defined (D3).
- Metrics/dimensions linked to glossary terms (D2).
- Dataset classified and access-controlled (D4/D5).
- Lineage to semantic model/report (D6); certification/endorsement defined.

## 11. Configuration model
```yaml
pattern_id: C2
model_name: recruitment_gold
model_type: star_schema
grain: one row per application stage event
fact_tables: [fact_application, fact_recruitment_stage]
dimensions: [dim_date, dim_department, dim_vacancy]
scd_type_2_dimensions: [dim_department]
semantic_model_required: true
purview_registration: true
certification_required: true
```

## 12. Non-functional requirements
- **Performance:** denormalised dimensions for Direct Lake; integrity checks fact↔dimension.
- **Scalability/cost:** capacity monitored (E6).
- **Auditability:** reconciliation counts retained.

## 13. Assurance checklist
- Is the grain explicitly agreed and documented?
- Are dimensions conformed/shared where they should be?
- Are SCD requirements defined per dimension?
- Is referential integrity assured fact↔dimension?

## 14. Acceptance criteria
The model is reusable and governed (not a one-off), reconciles to Silver, is registered with lineage to the semantic model, and follows the certification process.

## 15. Example implementation
A recruitment Gold star model: `fact_application` + `fact_recruitment_stage` against `dim_date/department/vacancy`, with `dim_department` as SCD Type 2, published via a certified semantic model.

---

# D1 - Purview Registration

- **Group:** Governance · **Status:** Draft · **Leverage type:** Reusable asset · **Owner:** Data Governance · **Version:** 0.1

## 1. Intent
Register and govern data assets in Purview so they are discoverable, owned, classified and connected to lineage.

## 2. Pattern group
Governance.

## 3. When to use
Every reusable data asset created by any ingestion, transformation or data-product pattern.

## 4. When not to use
Never skipped for governed assets; transient scratch data still gets minimal logging.

## 5. Typical use cases
Registering Bronze datasets, Silver products, Gold models, pipelines and semantic models.

## 6. Solution Architect view
Every reusable asset must be visible in the governance layer — not just technically created, but understandable, owned and governed. This is a *reusable asset* pattern: registration is automated/semi-automated and, where possible, **enforced** as a deployment gate (an unregistered asset should not reach Prod). See enforcement in `06_cross_cutting_concerns.md`.

## 7. Pattern composition
```text
Any ingestion/transformation/data product pattern
→ D1 Purview Registration
→ D2 Business Glossary Linkage
→ D3 Ownership / Stewardship Assignment
→ D6 Lineage Capture
```

## 8. Reusable molecules / building blocks
```text
GOV-01 Register asset in Purview
GOV-02 Link glossary term
GOV-03 Assign owner/steward
GOV-04 Apply classification
GOV-05 Capture lineage
```

## 9. Fabric implementation mapping
- Purview collection selected/created; asset scan/registration configured (Fabric–Purview native integration).
- Owner/steward metadata populated; classification applied; glossary terms linked; lineage connected where available.

## 10. Purview / governance mapping
This pattern *is* the governance mapping; it enforces that D2/D3/D4/D6 are satisfied.

## 11. Configuration model
```yaml
pattern_id: D1
asset_name: recruitment_gold
asset_type: data_product
purview_collection: student_lifecycle
business_domain: student
owner: "[Named owner]"
steward: "[Named steward]"
classification: confidential
glossary_terms: [application, vacancy, recruitment stage]
lineage_enabled: true
```

## 12. Non-functional requirements
- **Auditability:** registration evidence retained.
- **Supportability:** registration failures alerted (E1).

## 13. Assurance checklist
- Are owner, steward and classification present (and enforced)?
- Is the asset in the correct collection/domain?
- Is lineage connected?

## 14. Acceptance criteria
No governed asset reaches production without owner, steward, classification and Purview registration; lineage is visible.

## 15. Example implementation
`recruitment_gold` registered in the `student_lifecycle` collection with owner/steward, `confidential` classification and glossary links, enforced as a promotion gate.

---

# E2 - Reconciliation

- **Group:** Operational · **Status:** Draft · **Leverage type:** Reusable asset · **Owner:** Data Platform · **Version:** 0.1

## 1. Intent
Verify that data movement and transformation have completed correctly.

## 2. Pattern group
Operational.

## 3. When to use
Important/critical datasets where technical pipeline success is not sufficient proof of correctness.

## 4. When not to use
Low-value transient data where reconciliation cost outweighs benefit.

## 5. Typical use cases
Source-to-Bronze, Bronze-to-Silver and Silver-to-Gold completeness checks for critical reporting.

## 6. Solution Architect view
For important datasets the design must *prove* the expected data arrived, was transformed and was published correctly — not merely that pipelines ran. This is a reusable asset: the SA selects checks and thresholds via configuration.

## 7. Pattern composition
```text
A3 Database Table to Bronze
→ B1 Bronze to Silver Standardisation
→ C2 Gold Star Schema Model
→ E2 Reconciliation
→ E1 Monitoring and Alerting
```

## 8. Reusable molecules / building blocks
```text
OPS-02 Reconcile row counts
OPS-03 Compare aggregates
OPS-04 Write reconciliation status
ING-A-07 / OPS-05 Raise alert on threshold breach
```

## 9. Fabric implementation mapping
- Source→Bronze and Bronze→Silver row-count checks; Silver→Gold aggregate checks.
- Reconciliation status table; alerts on threshold breach; manual sign-off for critical datasets where required.

## 10. Purview / governance mapping
- Reconciliation status linked to the data product for consumer trust; failures feed DQ issue logging (D7).

## 11. Configuration model
```yaml
pattern_id: E2
reconciliation_level: critical
checks:
  - source_to_bronze_row_count
  - bronze_to_silver_row_count
  - silver_to_gold_aggregate_total
threshold_percentage: 0.5
alert_on_failure: true
manual_approval_required: false
```

## 12. Non-functional requirements
- **Auditability:** reconciliation results retained and queryable.
- **Supportability:** breaches alert and route to an owner.

## 13. Assurance checklist
- Are the right checks selected for the dataset's criticality?
- Is the threshold agreed with the data owner?
- Do breaches alert someone accountable?

## 14. Acceptance criteria
For a critical dataset, reconciliation runs automatically end-to-end, records status, and alerts on breach within threshold.

## 15. Example implementation
A recruitment load reconciled source→Bronze→Silver→Gold at 0.5% tolerance, status written to the reconciliation table, alerting on breach.
