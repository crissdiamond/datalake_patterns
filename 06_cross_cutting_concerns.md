# Cross-Cutting Concerns

Some concerns recur in **every** pattern. Defining them once here keeps the patterns themselves focused and prevents each pattern (or each supplier) from reinventing them inconsistently.

---

## 1. The Ingestion Gateway: control plane vs data plane

All ingestion enters through a single tech-agnostic contract — the **RESTful Ingestion API** (see `02_pattern_catalogue.md`, Standardized Ingestion Gateway). The reason is federation: with 300+ source systems, source teams must own their data and their push without learning Fabric, and the platform must be free to change the underlying technology without affecting them.

The gateway plays two roles, and keeping them distinct is what makes federation safe *and* performant:

- **Control plane (always)** — authentication/authorisation, validation against the Schema Registry contract, classification tagging, audit logging, lineage initiation, quarantine of non-conforming payloads. Every ingestion passes through this regardless of size.
- **Data plane (mode-dependent)** — the physical movement of bytes. The contract supports multiple transports so the same standard scales from a single record to a multi-GB extract to a high-frequency stream.

### Ingestion modes

| Mode | Used for | How it works | Source experience |
|---|---|---|---|
| **Direct** | Small/medium payloads | Data sent in the request body | Authenticate, POST, done |
| **Signed-URL handoff** | Large payloads / bulk extracts / large objects | Source POSTs *metadata*; API returns a short-lived signed upload location; source uploads bulk data there; platform ingests | Authenticate, POST metadata, upload, done |
| **Streaming** | High-frequency / near-real-time | API returns a streaming endpoint/topic the source publishes to | Authenticate, publish, done |

In all modes the source team only ever interacts with the Ingestion API and never with Fabric. This mirrors Integration offering both Enterprise APIs and enterprise distribution channels: several transports, one coherent contract.

> Design rule: never force the entire payload through a synchronous request body for *all* sizes. That is the one way the single-contract approach would create a bottleneck — and the signed-URL/streaming modes exist precisely to avoid it.

---

## 2. Schema Registry

The Schema Registry is the central store of **data contracts**. It is referenced by every ingestion pattern and by D9 (Schema Contract & Evolution Management).

Each contract holds, per source/feed:

- the agreed schema and version;
- the ingestion mode(s) permitted;
- Data Owner, Data Steward, Data Custodian;
- classification / sensitivity;
- data-quality expectations;
- compatibility policy (backward/forward) and deprecation window.

The gateway validates every payload against the **active** contract version and rejects/quarantines violations. Contract changes are versioned and governed via D9. The registry is the single source of truth that lets ingestion be both federated and safe.

---

## 3. Security and identity

- **Source authentication** — OAuth2 / client credentials / scoped API keys per source; external partners isolated with dedicated credentials, IP allow-listing and WAF (A6).
- **Internal identity** — Active Directory / Entra ID; service principals for automated flows; least privilege.
- **Secrets** — Azure Key Vault / Fabric-managed credentials; never embedded in pipelines or notebooks.
- **Data protection** — encryption in transit and at rest; payload-level encryption for sensitive data; classification (D4) drives access policy (D5).

---

## 4. Environments and promotion

- Standard **Dev / Test / Prod** workspace strategy (see E5).
- Deployment pipelines + Terraform/IaC modules; parameterised configuration, no hard-coded environment values.
- Version control for patterns, notebooks, libraries and configuration.
- **Governance gates at promotion** (see §6).

---

## 5. Capacity and cost

- Fabric capacity is shared and finite; every pattern must consider its capacity profile (see E6).
- Workload attribution by domain/product for chargeback and accountability.
- Threshold alerts; efficient-design guidance baked into the modelling patterns (C4/C7 especially).

---

## 6. Governance enforcement (policy-as-code)

Principle 3.5 states governance is embedded *by design*. In a federated model, "documented but not enforced" drifts. Each governance control is therefore classified as **Enforced** or **Expected**:

- **Enforced** — blocked if absent. Examples: no ingestion without an active Schema Registry contract; no promotion to Prod without owner, steward, classification and Purview registration.
- **Expected** — strongly recommended and assured via review, but not hard-gated.

Mechanisms:

- contract validation at the Ingestion gateway (ingestion-time enforcement);
- policy-as-code checks in the deployment pipeline (promotion-time enforcement);
- automated Purview registration as a gate;
- assurance review for Expected controls.

Each pattern's section 10 must state which of its controls are Enforced vs Expected.

---

## 7. Fabric-native-first stance (build vs buy)

Much of what this framework patternises, Microsoft already ships (medallion templates, Purview integration, deployment pipelines, OneLake shortcuts, database mirroring, Eventstream). To avoid building bespoke assets that duplicate platform capability and rot, every pattern's Fabric mapping (section 9) must declare each component as:

- **Adopt native** — use the Fabric/Purview capability directly (default preference);
- **Wrap** — thin standardisation layer over a native capability (e.g. config conventions);
- **Build bespoke** — only where native capability is genuinely absent or insufficient, with justification.

The Ingestion gateway itself is a deliberate **Build/Wrap** choice: the tech-agnostic federated contract is the value, and it brokers to native Fabric ingestion behind the scenes. This stance is also a key question to put to external partners — they should justify bespoke build, not default to it.
