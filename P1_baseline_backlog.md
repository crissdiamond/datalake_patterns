# P1 Baseline Backlog — Bronze → Operational Vertical Slice

This is the prioritised baseline backlog for the first engagement wave. It is **reuse-ranked**: order is priority. It exists to steer sprint capacity toward the most reusable patterns being **defined, completed and tested**, without committing the engagement to a fixed number of patterns.

Read alongside: `03_pattern_template.md` (the template), `09_supplier_engagement_brief.md` §3 (Definition of Done), `06_cross_cutting_concerns.md`, `02_pattern_catalogue.md` (full catalogue).

---

## 1. How this backlog is used

The engagement runs as a fixed-price, sprint-based model. This backlog does not change that. It sets **what gets worked, in what order, and to what bar** — not how many.

Three working rules apply:

1. **Definition of "progressed."** A pattern worked in a sprint is taken to **"Proposed" against the Definition of Done** (`09` §3): every template section complete, molecules listed by ID, Fabric/Purview mapping (Adopt-native / Wrap / Build), governance mapping (Enforced vs Expected), YAML configuration model, and a worked example — **and validated with a bounded prototype where the pattern is testable**. A pattern left at Draft has not been progressed.
2. **Depth over breadth.** Fewer patterns fully defined, completed and tested beats many partially started. Sprints finish-and-validate before opening new work. Sprint reviews are judged on patterns moved to *Proposed-and-tested*, not patterns touched.
3. **Top-down.** Work the backlog in order. If dependencies cost time, the least-reusable tail slips — not the core.

"Approved" remains dependent on UCL review and is **not** a sprint acceptance condition.

---

## 2. Scope boundary

- **In scope:** Bronze → operational vertical slice (standardise → quality → model → serve → govern → operate), using **synthetic / representative Bronze data** so prototyping is not gated on source-system access.
- **Optional tail:** Ingestion entry points (A1 / A3), last sprint only if capacity allows.
- **Deferred to a later wave:** the Ingestion Gateway / Schema Registry build-versus-buy and interface work, and the broader ingestion family.

---

## 3. Top 8 — core spine (define, complete, test first)

| # | ID | Pattern | Family | Why it ranks here (reuse) | Prototype |
|---|---|---|---|---|---|
| 1 | **B1** | Bronze → Silver Standardisation | Transformation | Every downstream path starts here — the most central pattern | Yes |
| 2 | **B3** | Data Quality Validation & Quarantine | Transformation | Quality gate reused by every ingestion/transformation flow | Yes |
| 3 | **B6** | Slowly Changing Dimension Handling | Transformation | Reused by every dimensional model; makes C2 credible | Yes |
| 4 | **C2** | Gold Star Schema Model | Data product | Flagship, canonical data product | Yes |
| 5 | **C6** | Semantic Model / Power BI Dataset | Data product | Lands the slice on real consumption | Yes |
| 6 | **D1** | Purview Registration | Governance | Every product gets governed — foundational | Config |
| 7 | **E1** | Monitoring & Alerting | Operational | Every pipeline is operated through it | Config |
| 8 | **E2** | Reconciliation | Operational | Integrity check on every flow | Yes |

This set alone is a complete, testable slice.

---

## 4. +6 — extend the baseline

| # | ID | Pattern | Family | Why included | Prototype |
|---|---|---|---|---|---|
| 9 | **C1** | Silver Curated Data Product | Data product | Reusable Silver feeding multiple Gold models; lowers cost of C2/C5 | Yes |
| 10 | **C5** | Gold Flat Operational Reporting Model | Data product | Serves the operational-reporting priority in the SOW; 2nd model shape | Yes |
| 11 | **B5** | Reference Data Enrichment | Transformation | Near-universal in Silver→Gold paths | Yes |
| 12 | **D3** | Ownership / Stewardship Assignment | Governance | Every asset needs an owner; prerequisite for access & remediation | Config |
| 13 | **E3** | Retry & Reprocessing | Operational | Every pipeline needs resilience logic | Yes |
| 14 | **E7** | Data Quality Remediation & Resubmission | Operational | Closes the B3 → D7 → E7 loop; resolves an open decision | Yes |

---

## 5. Optional tail — last sprint, if capacity

| ID | Pattern | Note |
|---|---|---|
| **A1** | File to Bronze | Ingestion entry point; synthetic file source |
| **A3** | Database Table to Bronze | Ingestion entry point; only if access available |

Gateway / Schema Registry advisory remains deferred to a later wave.

---

## 6. The slice as a composition chain

```
synthetic Bronze
  → B1  standardise to Silver
  → B3  quality-gate (quarantine failures)
  → B5  enrich with reference data
  → B6  apply dimension history
  → C1  publish curated Silver product
  → C2  build Gold star schema   (+ C5 flat operational model)
  → C6  expose as Power BI semantic model
  → D1  register in Purview   + D3 assign ownership/stewardship
  → E1  monitor + E2 reconcile + E3 retry/reprocess
  → E7  remediate & resubmit quarantined records
```

Every family (B, C, D, E) is exercised and each pattern composes into the next — which is the test of whether the framework holds.

---

## 7. Open judgment calls (swap candidates)

- **D3 vs D4 (Data Classification)** — ownership ranked higher for reuse; swap to D4 if the demo dataset carries PII / security drivers.
- **E3 vs E4 (SLA / Freshness Tracking)** — retry/reprocessing chosen for breadth; E4 if consumer SLAs matter more. (E5 Deployment excluded — CI/CD is out of scope.)
- **C1** — mild overlap with B1's Silver output; drop for another pattern if a leaner product layer is preferred.
- **B2 (Silver Conformance to EDM)** — deliberately excluded from baseline: depends on EDM maturity, a known risk; keep it out so it cannot block a sprint.
