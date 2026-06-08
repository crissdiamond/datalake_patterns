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
A1 File to Bronze
```

Possible molecules:

```text
Receive File
Validate File Presence
Capture Source Metadata
Land Raw Data
Validate Schema
Log Pipeline Run
Quarantine Failed File
Register Bronze Asset
Send Failure Alert
```

Pattern:

```text
C2 Gold Dimensional Model
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

## 3. Draft molecule categories

Molecules should be grouped underneath the pattern catalogue, for example:

### Source and ingestion molecules

- Receive file
- Call API
- Extract database table
- Read event stream
- Capture source metadata
- Validate source contract
- Land raw data

### Transformation molecules

- Standardise column names
- Convert data types
- Apply mapping rules
- Apply data quality rule
- Quarantine failed record
- Deduplicate records
- Enrich with reference data
- Apply SCD logic

### Data product molecules

- Create curated Silver table
- Create fact table
- Create dimension table
- Create flattened Gold table
- Create semantic model
- Publish extract

### Governance molecules

- Register asset in Purview
- Link glossary term
- Assign owner/steward
- Apply classification
- Capture lineage
- Log data quality issue

### Operational molecules

- Log run
- Reconcile row counts
- Retry failed step
- Reprocess batch
- Track freshness
- Promote between environments
- Monitor capacity

## 4. Implementation expectation

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

## 5. Key distinction

- **Pattern** = reusable architecture solution for a common end-to-end need.
- **Molecule** = reusable implementation/design unit used inside one or more patterns.
- **Reusable asset** = actual centrally managed code/template/configuration that implements a molecule.

Any implementation or design using this framework should maintain this clear distinction.
