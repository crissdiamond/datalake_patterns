# Standard Data Lake Pattern Template

Each pattern should be documented using this structure.

# Pattern ID and name

Example: `A1 - File to Bronze`

## 1. Intent

What the pattern is for, in business and architecture terms.

## 2. Pattern group

One of:

- Ingestion
- Transformation
- Data Product
- Governance
- Operational

## 3. When to use

Clear conditions where this pattern is appropriate.

## 4. When not to use

Clear conditions where this pattern is unsuitable or risky.

## 5. Typical use cases

Examples of business/data scenarios.

## 6. Solution Architect view

The description should be understandable by an SA without deep Fabric knowledge.

Include:

- logical flow;
- key architecture decisions;
- data ownership implications;
- dependencies;
- assurance questions.

## 7. Pattern composition

Which other patterns usually combine with this one.

Example:

```text
A1 File to Bronze
→ B1 Bronze to Silver Standardisation
→ B3 Data Quality Validation and Quarantine
→ C2 Gold Dimensional Model
→ D1 Purview Registration
→ E1 Monitoring and Alerting
```

## 8. Reusable molecules / building blocks

List the reusable lower-level capabilities required.

Examples:

- receive file;
- land raw data;
- capture metadata;
- validate schema;
- log run;
- quarantine record;
- register asset in Purview.

## 9. Fabric implementation mapping

Describe the low-level Fabric implementation.

Include where relevant:

- workspace;
- Lakehouse;
- Warehouse;
- pipeline;
- notebook;
- semantic model;
- deployment pipeline;
- monitoring/logging table;
- security group;
- capacity considerations.

## 10. Purview / governance mapping

Include:

- Purview collection;
- asset registration;
- lineage;
- business glossary linkage;
- ownership/stewardship;
- classification;
- access approval;
- quality issue logging.

## 11. Configuration model

Define what should be configurable by federated teams.

Examples:

```yaml
source_name: ""
source_type: ""
load_frequency: ""
load_type: "full | incremental | delta"
watermark_column: ""
target_domain: ""
data_owner: ""
data_steward: ""
classification: ""
dq_ruleset: ""
purview_registration: true
monitoring_profile: "standard | critical"
```

## 12. Non-functional requirements

Include:

- security;
- availability;
- performance;
- scalability;
- auditability;
- retention;
- cost/capacity;
- supportability.

## 13. Assurance checklist

Questions Data Architecture or the relevant design forum should use to approve the pattern.

## 14. Acceptance criteria

How UCL knows the pattern is complete and reusable.

## 15. Example implementation

A small worked example demonstrating the pattern in a realistic UCL context.
