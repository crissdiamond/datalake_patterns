# Pattern Selection Guide

This guide helps a Solution Architect navigate the catalogue and compose patterns into an end-to-end solution without deep Fabric knowledge. It is the entry point to `02_pattern_catalogue.md`.

---

## 1. The end-to-end shape

Almost every solution is a path through the five groups:

```text
Ingestion (A)  →  Transformation (B)  →  Data Product (C)
        with  Governance (D)  and  Operational (E)  applied throughout
```

Governance (D) and Operational (E) patterns are **not optional add-ons** — they attach to every solution. Choose one ingestion path, one or more transformation steps, and the right data-product shape; then apply the standard D and E patterns.

---

## 2. Step 1 — choose the ingestion pattern (A)

| If the source is… | Use |
|---|---|
| A file pushed/forwarded on a schedule | **A1** File to Bronze |
| A source API to pull from | **A2** API to Bronze |
| One or more database tables | **A3** Database Table to Bronze |
| A stream of events / telemetry | **A4** Event Stream to Bronze |
| A human uploading via a UI | **A5** Manual Upload |
| An external partner / third party | **A6** External Partner Extract |
| Unstructured / large binary objects | **A7** Unstructured / Large Object |

All A-patterns use the same Ingestion API contract; only the **ingestion mode** (direct / signed-URL / streaming) and authentication differ. See `06_cross_cutting_concerns.md`.

---

## 3. Step 2 — choose the transformation steps (B)

Pick the ones that apply, in order:

1. **B1** Standardisation — almost always.
2. **B2** Conformance to EDM/Domain Model — if cross-portfolio consistency is needed *and* a domain model exists.
3. **B3** Data Quality & Quarantine — wherever correctness matters.
4. **B4** Deduplication & Survivorship — if consolidating entities across sources.
5. **B5** Reference Data Enrichment — if lookups/hierarchies are needed.
6. **B6** SCD Handling — if history must be preserved for dimensions.
7. **B7** Feature Engineering for ML — if the output feeds models/AI.

---

## 4. Step 3 — choose the data-product shape (C)

```text
Need governed, reusable BI with shared dimensions?      → C2 Star Schema
  ...with deep hierarchies / very wide dimensions?       → C3 Snowflake
Petabyte / streaming / ML feature scans?                → C4 OBT / Big Data
Operational / short-lived / transitional report?        → C5 Flat Operational
Just a reusable curated dataset (pre-modelling)?        → C1 Silver Curated Product
Publishing to Power BI?                                  → C6 Semantic Model (+ C7 Direct Lake)
Sending a controlled extract out?                        → C8 Extract Publication
Other domains will consume it as a dependency?           → C9 Cross-Domain / Data Sharing Product
```

> Remember the **leverage type**: C2/C3/C4 are *guided design* — the framework keeps the modelling safe, but the design still needs judgement. C1/C5/C8 are closer to *reusable asset*.

---

## 5. Step 4 — apply governance (D) — always

- **D1** Purview Registration, **D2** Glossary, **D3** Ownership, **D4** Classification, **D5** Access, **D6** Lineage — the standard governance bundle for any data product.
- **D7** DQ Issue Logging — wherever B3/B4 run.
- **D8** Retention/Archival/Deletion — always, especially personal/regulated data.
- **D9** Schema Contract & Evolution — for every ingestion source and cross-domain product.

Several D controls are **enforced as gates** (see `06_cross_cutting_concerns.md`).

---

## 6. Step 5 — apply operational patterns (E) — always

- **E1** Monitoring & Alerting — every solution.
- **E2** Reconciliation — important/critical datasets.
- **E3** Retry & Reprocessing — any pipeline.
- **E4** SLA / Freshness — anything with a consumer expectation (especially C9).
- **E5** Deployment & Promotion — all solutions.
- **E6** Cost / Capacity — all workloads, especially C4/C7.
- **E7** DQ Remediation & Resubmission — wherever records are quarantined (B3/B4/D7).

---

## 7. Worked composition examples

**Operational reporting from a database**
```text
A3 → B1 → B3 → C5 → C6 → D1/D3/D4 → E1/E2/E5
```

**Strategic governed BI**
```text
A1 → B1 → B2 → B6 → C2 → C6/C7 → D1/D2/D3/D4/D5/D6 → E1/E2/E4/E5/E6
```

**Streaming + ML**
```text
A4 → B3 → B7 → C4 → D1/D4/D8 → E1/E3/E6
```

**External partner data shared cross-domain**
```text
A6 → B3 → B5 → C1 → C9 → D1/D2/D5/D8/D9 → E1/E4
```

---

## 8. When you can't find a pattern

If no pattern fits, that is a signal to **propose a new one**, not to build bespoke off-framework. Follow the contribution route in `08_operating_model.md`.
