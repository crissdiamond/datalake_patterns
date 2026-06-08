# Supplier / Partner Engagement Brief

This brief is for external delivery partners (and internal teams) asked to help **define Data Lake patterns** to the framework standard. It exists so that several partners working in parallel produce *consistent, mergeable* output rather than four divergent styles.

Read first: `01_model_and_principles.md`, `03_pattern_template.md` (the template you must follow), `04_example_patterns.md` (the quality bar), `06_cross_cutting_concerns.md`.

---

## 1. What we are asking you to do

Take patterns from the catalogue (`02_pattern_catalogue.md`) — currently short summaries — and **complete them to the canonical template** (`03_pattern_template.md`), to the standard shown in the worked examples (`04_example_patterns.md`), including the molecules each pattern requires.

We are **not** asking you to:

- invent a new framework or restructure the five groups;
- produce a large molecule catalogue in isolation (derive molecules per pattern — see `05_molecules_as_building_blocks.md`);
- default to bespoke build where Fabric-native capability exists (justify build vs buy — `06_cross_cutting_concerns.md` §7).

---

## 2. Scope and priorities

Patterns will be completed in priority waves, not all at once.

| Wave | Patterns | Rationale |
|---|---|---|
| **P1** | A1, A3, B1, B3, C2, D1, E1, E2 + the Ingestion Gateway / Schema Registry detail | The most common end-to-end path; proves the framework. |
| **P2** | A2, A4, A6, B2, B5, B6, C1, C5, C6, D3/D4/D5, E3, E5 | Broadens ingestion, modelling and governance. |
| **P3** | A5, A7, B4, B7, C3, C4, C7, C8, C9, D2/D6/D7/D8/D9, E4, E6 | Specialist and lifecycle patterns. |

(Confirm the wave assignment with Data Architecture before starting — priorities may shift with demand.)

---

## 3. Definition of a "complete" pattern (definition of done)

A pattern is **Proposed** (ready for review) only when **all** of the following hold:

1. Every section of the template (`03`) is filled — including **When not to use**, **Leverage type**, **Pattern composition**, **NFRs**, **Assurance checklist** and **Acceptance criteria**.
2. Required **molecules are listed by ID** (`05` scheme); any new molecule is proposed with purpose, inputs/outputs, config and acceptance criteria.
3. The **Fabric mapping** declares each component as Adopt-native / Wrap / Build-bespoke, with justification for bespoke.
4. The **governance mapping** states which controls are Enforced vs Expected.
5. A **configuration model** (YAML) shows exactly what a federated team configures.
6. A **worked example** in a realistic context is included.
7. It is consistent with the cross-cutting concerns (`06`) — gateway/modes, Schema Registry, security, environments, capacity, enforcement.

A pattern that fails any of these is **Draft**, not a deliverable.

---

## 4. Context you will be given

To avoid you spending the first weeks on discovery, we will provide:

- current Fabric estate and capacity model;
- existing reusable assets (gateway, libraries, IaC) and their maturity;
- the Enterprise/Domain Data Model state (note: this is a known dependency — see `08_operating_model.md` §5);
- Purview rollout and classification scheme;
- security/identity standards;
- the priority use cases driving each wave.

If any of this is missing when you start, flag it — do not assume.

---

## 5. Engagement model and ways of working

- **One template, one quality bar.** All partners use `03` and match `04`. Output is reviewed for consistency before acceptance.
- **Assurance gate.** Completed patterns are reviewed by the design forum (`08_operating_model.md`) and moved Draft → Proposed → Approved.
- **No off-framework build.** If a pattern doesn't fit, raise a pattern proposal (`08` §3); do not deliver a bespoke solution.
- **Challenge welcome, early.** If you believe an architectural assumption is wrong (e.g. the single Ingestion contract, the leverage type of a pattern), raise it at the start with evidence — not after delivery.

---

## 6. What "good" looks like

- A Solution Architect can select your completed pattern and design a solution **by configuration and guided decisions**, without deep Fabric knowledge.
- A federated team can implement it **without bespoke engineering** for asset-type patterns.
- Governance and operations are **built in and, where stated, enforced** — not bolted on.
- It composes cleanly with the other patterns via the composition section.

This — not volume of documentation — is the measure of success.
