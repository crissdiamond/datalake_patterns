# Framework Operating Model

The Integration approach worked because a central team *ran* it — owning the standards, curating the catalogue and assuring federated delivery. The Data Lake framework needs the same operating model, or "safe federation" is just an aspiration. This document defines how the framework itself is governed.

---

## 1. Roles and RACI (the framework, not individual data products)

| Activity | Data Architecture | Data Platform | Data Governance | Federated team | Design forum (DWG) |
|---|---|---|---|---|---|
| Own the pattern catalogue & standards | **A/R** | C | C | I | C |
| Build & maintain reusable assets (notebooks, pipelines, IaC, gateway) | C | **A/R** | C | I | I |
| Own governance controls & enforcement | C | C | **A/R** | I | C |
| Approve a new/changed pattern | C | C | C | R (proposes) | **A/R** |
| Design a solution using patterns | **A/R** (assures) | C | C | **R** | C |
| Operate a deployed data product | I | C | C | **A/R** | I |

A = Accountable, R = Responsible, C = Consulted, I = Informed. The principle mirrors Integration: **Architecture provides standards and assurance; Platform provides reusable assets; federated teams configure and operate; a design forum approves.**

---

## 2. Pattern lifecycle

Every pattern (and molecule) carries a status:

```text
Draft  →  Proposed  →  Approved  →  Deprecated
```

- **Draft** — being written; not for production use.
- **Proposed** — complete to the template, in review by the design forum.
- **Approved** — assured and available for federated use; changes are versioned.
- **Deprecated** — superseded; existing solutions migrate; no new adoption.

Pattern IDs and molecule IDs are permanent once Approved — deprecate, never reassign.

---

## 3. Federation and contribution model

Federated teams **configure approved patterns**; they do not build off-framework. When a real need has no matching pattern:

1. The federated team raises a **pattern proposal** (the gap, the use case, a draft against the template).
2. Data Architecture and Data Platform assess fit, reuse and build-vs-buy (`06_cross_cutting_concerns.md` §7).
3. The design forum approves it to the catalogue, or routes the need to an existing pattern.
4. Data Platform builds/extends the reusable assets and molecules.

This keeps the catalogue growing from real demand while preventing fragmentation into bespoke solutions.

---

## 4. Assurance

- Each pattern has an **assurance checklist** (template section 13).
- Each *solution* built on patterns is assured by Data Architecture against those checklists at design time.
- Enforced governance controls are gated automatically (`06_cross_cutting_concerns.md` §6); Expected controls are checked at assurance review.

---

## 5. Known framework-level dependencies and risks

| Dependency / risk | Why it matters | Owner |
|---|---|---|
| **Enterprise/Domain Data Model maturity** | B2 conformance and several C patterns assume a model to conform to. Integration had a mature EDM; the data side may not. If immature, these patterns cannot be completed. | Data Architecture |
| **Schema Registry capability** | The whole ingestion contract depends on it; it must exist as a real component, not a concept. | Data Platform |
| **Fabric-native overlap** | Risk of building bespoke assets duplicating platform capability. | Data Platform |
| **Governance enforcement tooling** | Without policy-as-code gates, embedded governance drifts. | Data Governance |
| **Central team capacity** | Federation only stays safe if a central team curates and assures. | Data Architecture / Platform |

**Intended EDM approach.** Incremental and bottom-up: conform domains only as the wave use cases require, with B2 applied where a cross-domain consumer exists; the domain model accretes from real patterns under steward curation rather than being modelled top-down up front. This is the direction we expect the delivery partner to confirm and shape (see `09_supplier_engagement_brief.md`).

---

## 6. Success metrics

How we will know the framework is working (not just documented):

- **Reuse rate** — % of new solutions delivered by configuring approved patterns vs bespoke build.
- **Time-to-deliver** — lead time from use-case to governed data product, trend over time.
- **Governed coverage** — % of production data assets with owner, steward, classification and Purview registration (target: 100% enforced).
- **Federation reach** — number of federated teams delivering without central engineering.
- **Contract coverage** — % of ingestion sources with an active Schema Registry contract.
- **Capacity efficiency** — cost per workload/domain trend.
- **Catalogue health** — patterns at Approved status vs Draft; proposals turned around within SLA.

These metrics are also how the investment is justified to stakeholders and external partners.
