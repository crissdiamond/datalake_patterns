# Molecules as Building Blocks Under the Pattern Catalogue

## 1. Purpose

This document clarifies the role of molecules.

Molecules are not the top-level catalogue; they are the reusable lower-level building blocks that allow patterns to be assembled and implemented consistently under the pattern catalogue.

This mirrors the Integration approach:

```text
Integration Pattern
        ↓
Molecules
        ↓
AWS/Azure implementation
        ↓
EASIKit / Python / Terraform
```

The Data Lake equivalent should be:

```text
Data Lake Pattern
        ↓
Data Lake molecules
        ↓
Fabric/Purview implementation
        ↓
Centrally managed notebooks, pipelines, libraries, Terraform/IaC and configuration
```

## 2. Example relationship between patterns and molecules

Pattern:

```text
A1 File to Bronze (via Ingestion API)
```

Possible molecules:

```text
Client-Side/Source Actions:
- Read Local Source File
- Authenticate with Ingestion API
- POST File Payload to API

API-Side/Gateway Actions:
- Authorize Ingestion Request
- Validate Payload Schema against Registry
- Virus Scan Payload
- Stream Payload to Bronze
- Log Gateway Ingestion Event
- Quarantine Invalid Ingestion Payload
- Send Ingestion Failure Alert
```

Pattern:

```text
C2 Gold Star Schema Model
```

Possible molecules:

```text
Create Dimension
Create Fact
Apply Surrogate Key
Apply SCD Type 2
Calculate Measure
Reconcile Gold Output
Publish Semantic Model
Register Data Product
```

## 3. Molecule identification scheme

Patterns have IDs (`A1`, `C2`); molecules must too, or they become unmanageable as they multiply. Every molecule gets a stable ID and name, referenced from each pattern's section 8.

Format: `GROUP[-SUBGROUP]-NN`

| Prefix | Molecule family |
|---|---|
| `ING-C-NN` | Client-side ingestion (source systems & agents) |
| `ING-A-NN` | API-side ingestion (the Ingestion Gateway) |
| `TRN-NN`   | Transformation |
| `DP-NN`    | Data product |
| `GOV-NN`   | Governance |
| `OPS-NN`   | Operational |

Rules:
- An ID is permanent once approved; deprecate, never reassign.
- A molecule is defined once and reused across patterns — do not fork per pattern.
- Each molecule carries the same lifecycle status as patterns (Draft | Proposed | Approved | Deprecated).

## 4. Draft molecule categories

Molecules should be grouped underneath the pattern catalogue, for example:

### Client-Side Ingestion Molecules (`ING-C-NN` — source systems & agents)

- `ING-C-01` Read source file
- `ING-C-02` Extract source database table
- `ING-C-03` Pull from source API
- `ING-C-04` Authenticate with Ingestion API
- `ING-C-05` Batch source records and POST (or request signed URL)

### API-Side Ingestion Molecules (`ING-A-NN` — Ingestion Gateway)

- `ING-A-01` Authorize API client request
- `ING-A-02` Validate payload schema against Registry
- `ING-A-03` Virus/malware scan payload
- `ING-A-04` Write payload stream to Bronze folder
- `ING-A-05` Log gateway audit telemetry
- `ING-A-06` Quarantine rejected payload
- `ING-A-07` Send ingestion failure alert
- `ING-A-08` Route event payload to stream processor

### Transformation molecules (`TRN-NN`)

- `TRN-01` Standardise column names
- `TRN-02` Convert data types
- `TRN-03` Apply mapping rules
- `TRN-04` Apply data quality rule
- `TRN-05` Quarantine failed record
- `TRN-06` Deduplicate records
- `TRN-07` Enrich with reference data
- `TRN-08` Apply SCD logic

### Data product molecules (`DP-NN`)

- `DP-01` Create curated Silver table
- `DP-02` Create fact table
- `DP-03` Create dimension table
- `DP-04` Create flattened Gold table
- `DP-05` Create semantic model
- `DP-06` Publish extract

### Governance molecules (`GOV-NN`)

- `GOV-01` Register asset in Purview
- `GOV-02` Link glossary term
- `GOV-03` Assign owner/steward
- `GOV-04` Apply classification
- `GOV-05` Capture lineage
- `GOV-06` Log data quality issue

### Operational molecules (`OPS-NN`)

- `OPS-01` Log run
- `OPS-02` Reconcile row counts
- `OPS-03` Retry failed step
- `OPS-04` Reprocess batch
- `OPS-05` Track freshness / raise alert
- `OPS-06` Promote between environments
- `OPS-07` Monitor capacity

## 5. Implementation expectation

Designers and developers should not start by producing a large molecule catalogue in isolation.

They should start with the pattern catalogue and, for each priority pattern, identify the molecules required to implement it.

For each selected molecule, the framework should define:

- purpose;
- inputs and outputs;
- configuration parameters;
- Fabric/Purview implementation mapping;
- reusable code/template/module required;
- governance and operational controls;
- acceptance criteria.

## 6. Key distinction

- **Pattern** = reusable architecture solution for a common end-to-end need.
- **Molecule** = reusable implementation/design unit used inside one or more patterns.
- **Reusable asset** = actual centrally managed code/template/configuration that implements a molecule.

Any implementation or design using this framework should maintain this clear distinction.
