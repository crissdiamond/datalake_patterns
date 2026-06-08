# Worked Example Data Lake Patterns

## Example 1: A1 - File to Bronze

### Intent

Load scheduled source files into the Bronze/raw layer in a controlled, auditable and repeatable way.

#### Solution Architect view

The source system or external client pushes the file payload directly to the platform's RESTful Ingestion API. The API validates credentials, verifies the payload against the central Schema Registry, scans the contents, and streams the data directly to the Bronze layer. This ensures that no raw data lands in the lake without passing security and schema checks.

### Typical composition

```text
A1 File to Bronze (via Ingestion API)
→ B1 Bronze to Silver Standardisation
→ B3 Data Quality Validation and Quarantine
→ D1 Purview Registration
→ E1 Monitoring and Alerting
→ E2 Reconciliation
```

### Low-level Fabric mapping

- HTTP POST request targeting the platform's RESTful Ingestion API gateway.
- API authentication using OAuth2 or secure API keys.
- Gateway schema validation utilizing the central Schema Registry.
- Ingestion API backend streaming payloads directly to Bronze Lakehouse storage.
- API-level metadata logging (payload size, file hash, upload timestamp).
- Quarantine storage for payloads rejected by the gateway.
- Gateway logging and telemetry dashboard.

### Governance mapping

- Data Owner and Data Steward identified and configured in the Schema Registry before ingestion.
- Source data contract versioned and registered.
- Data classification recorded.
- Purview asset created for Bronze destination dataset.
- Lineage starts at the Ingestion API gateway entry point.

### Example configuration

```yaml
pattern_id: A1
source_name: hr_extract
source_type: file
file_format: csv
load_frequency: daily
load_type: full
landing_zone: bronze/hr/extract
schema_validation: true
data_owner: "[Named owner]"
data_steward: "[Named steward]"
classification: confidential
purview_registration: true
monitoring_profile: standard
```

---

## Example 2: B1 - Bronze to Silver Standardisation

### Intent

Convert raw Bronze data into standardised Silver data with consistent naming, types, formats and basic cleansing.

### Solution Architect view

This pattern is used when raw source structures need to be made usable for downstream modelling and governance. It does not yet create a Gold reporting model, but it creates a standardised and more reliable representation of the source data.

### Typical composition

```text
A1 File to Bronze
→ B1 Bronze to Silver Standardisation
→ B2 Silver Conformance to EDM / Domain Model
→ C1 Silver Curated Data Product
```

### Low-level Fabric mapping

- Reusable notebook using a shared transformation library.
- Mapping configuration from source fields to standardised fields.
- Standard data type conversion.
- Standard date/time handling.
- Silver Lakehouse output table.
- Transformation audit log.

### Governance mapping

- Mapping rules reviewed by Data Steward or delegated SME.
- Glossary candidate terms identified.
- Data quality rules identified for follow-on validation.
- Lineage captured from Bronze to Silver.

### Example configuration

```yaml
pattern_id: B1
source_table: bronze.hr_extract
silver_table: silver.hr_standardised
mapping_file: mappings/hr_to_silver.yaml
standard_date_format: ISO-8601
standard_null_handling: true
audit_logging: true
```

---

## Example 3: C2 - Gold Star Schema Model

### Intent

Create a classic conformed dimensional model (Star Schema) consisting of flat dimensions and facts to support strategic reporting.

### Solution Architect view

This pattern is used when data needs to support repeatable reporting and analytical consumption. It defines facts, dimensions, measures, keys and historical behaviour. It should be used where the output needs to be reusable and governed, not just a one-off report extract.

### Typical composition

```text
B2 Silver Conformance to EDM / Domain Model
→ B6 Slowly Changing Dimension Handling
→ C2 Gold Star Schema Model
→ C6 Semantic Model / Power BI Dataset
→ D1 Purview Registration
→ E4 SLA / Freshness Tracking
```

### Low-level Fabric mapping

- Fabric Warehouse or Lakehouse Gold tables.
- Fact and dimension tables.
- Surrogate key handling.
- SCD Type 1/2 implementation where required.
- Power BI semantic model or Direct Lake model.
- Reconciliation from Silver to Gold.

### Governance mapping

- Data product owner defined.
- Metrics and dimensions linked to glossary terms.
- Dataset classified and access-controlled.
- Lineage captured to semantic model/report.
- Certification/endorsement process defined.

### Example configuration

```yaml
pattern_id: C2
model_name: recruitment_gold
model_type: star_schema
fact_tables:
  - fact_application
  - fact_recruitment_stage
dimensions:
  - dim_date
  - dim_department
  - dim_vacancy
scd_type_2_dimensions:
  - dim_department
semantic_model_required: true
purview_registration: true
certification_required: true
```

---

## Example 4: D1 - Purview Registration

### Intent

Register and govern data assets in Purview so that they are discoverable, owned, classified and connected to lineage.

### Solution Architect view

Every reusable data asset should be visible in the governance layer. This pattern ensures that the asset is not just technically created, but also understandable, owned and governed.

### Typical composition

```text
Any ingestion/transformation/data product pattern
→ D1 Purview Registration
→ D2 Business Glossary Linkage
→ D3 Ownership / Stewardship Assignment
→ D6 Lineage Capture
```

### Low-level implementation mapping

- Purview collection selected or created.
- Asset scan/registration configured.
- Owner/steward metadata populated.
- Classification applied.
- Glossary terms linked.
- Lineage connected where technically available.

### Example configuration

```yaml
pattern_id: D1
asset_name: recruitment_gold
asset_type: data_product
purview_collection: student_lifecycle
business_domain: student
owner: "[Named owner]"
steward: "[Named steward]"
classification: confidential
glossary_terms:
  - application
  - vacancy
  - recruitment stage
lineage_enabled: true
```

---

## Example 5: E2 - Reconciliation

### Intent

Verify that data movement and transformation have completed correctly.

### Solution Architect view

For important datasets, it is not enough that the pipeline succeeds technically. The design must prove that the expected data arrived, was transformed and was published correctly.

### Typical composition

```text
A3 Database Table to Bronze
→ B1 Bronze to Silver Standardisation
→ C2 Gold Star Schema Model
→ E2 Reconciliation
→ E1 Monitoring and Alerting
```

### Low-level Fabric mapping

- Source-to-Bronze row count checks.
- Bronze-to-Silver row count checks.
- Silver-to-Gold aggregate checks.
- Reconciliation status table.
- Alerts on threshold breach.
- Manual sign-off for critical datasets where required.

### Example configuration

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
