# Standard Data Lake Pattern Template

This is the **canonical shape for a *complete* pattern**.

There is exactly one template. To avoid the confusion of multiple competing structures:

- `02_pattern_catalogue.md` holds a short, consistent **summary** of every pattern (the index and scope).
- A **completed** pattern — the deliverable expected from internal teams and external partners — expands that summary into the full structure below.
- The worked examples in `04_example_patterns.md` follow this template exactly and are the **quality bar**. If a supplier asks "what does good look like?", point them there.

---

## Header block

Every pattern starts with:

- **Pattern ID and name** — e.g. `A1 - File to Bronze`
- **Pattern group** — Ingestion | Transformation | Data Product | Governance | Operational
- **Status** — Draft | Proposed | Approved | Deprecated (see `08_operating_model.md` for the lifecycle)
- **Leverage type** — *Reusable asset* or *Guided design* (see note below)
- **Owner** — the central team accountable for this pattern definition
- **Version** — semantic version of the pattern definition

> **Leverage type — read this before writing a pattern.**
> Be honest about what the pattern actually gives a Solution Architect:
> - **Reusable asset** — the SA *configures* a centrally managed building block; little or no bespoke engineering. Most Ingestion, Governance and Operational patterns are this type.
> - **Guided design** — the framework supplies guardrails, decision questions and reference implementations, but genuine design judgement is still required. Most Data Product (modelling) patterns and some Transformation patterns are this type.
>
> The framework's promise is "SAs design safely without deep Fabric knowledge." That promise is *configuration* for asset patterns and *guardrails* for guided-design patterns. Do not imply a star-schema design is turnkey when it is not.

---

## 1. Intent

What the pattern is for, in business and architecture terms.

## 2. Pattern group

One of: Ingestion | Transformation | Data Product | Governance | Operational.

## 3. When to use

Clear conditions where this pattern is appropriate.

## 4. When not to use

Clear conditions where this pattern is unsuitable or risky. **Mandatory** — a pattern with no "when not to use" has not been thought through.

## 5. Typical use cases

Examples of business/data scenarios.

## 6. Solution Architect view

Understandable by an SA without deep Fabric knowledge. Include:

- logical flow;
- key architecture decisions;
- data ownership implications;
- dependencies (including any dependency on the Enterprise/Domain Data Model — see `08_operating_model.md`);
- assurance questions.

## 7. Pattern composition

Which other patterns usually combine with this one, upstream and downstream.

```text
A1 File to Bronze
→ B1 Bronze to Silver Standardisation
→ B3 Data Quality Validation and Quarantine
→ C2 Gold Star Schema Model
→ D1 Purview Registration
→ E1 Monitoring and Alerting
```

## 8. Reusable molecules / building blocks

List the lower-level molecules required, by their molecule ID and name (see the molecule ID scheme in `05_molecules_as_building_blocks.md`).

```text
ING-C-01 Read source file
ING-A-02 Validate payload schema
ING-A-04 Write payload stream to Bronze
OPS-01   Log run
GOV-01   Register asset in Purview
```

## 9. Fabric implementation mapping

The low-level Fabric implementation. Include where relevant: workspace; Lakehouse; Warehouse; pipeline; notebook; semantic model; deployment pipeline; monitoring/logging table; security group; capacity considerations.

State explicitly which parts adopt **Fabric-native** capability and which are **bespoke** (see the Fabric-native-first stance in `06_cross_cutting_concerns.md`).

## 10. Purview / governance mapping

Include: Purview collection; asset registration; lineage; business glossary linkage; ownership/stewardship; classification; access approval; quality issue logging.

State which governance controls are **enforced** (blocked if absent) versus **expected** (documented). See the enforcement model in `06_cross_cutting_concerns.md`.

## 11. Configuration model

What a federated team can configure without engineering. Example:

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

Security; availability; performance; scalability; auditability; retention; cost/capacity; supportability.

## 13. Assurance checklist

Questions Data Architecture or the relevant design forum should use to approve a solution built on this pattern.

## 14. Acceptance criteria

How we know the **pattern itself** is complete and reusable (not just one solution). Tie to the definition-of-done in `09_supplier_engagement_brief.md`.

## 15. Example implementation

A small worked example demonstrating the pattern in a realistic context.
