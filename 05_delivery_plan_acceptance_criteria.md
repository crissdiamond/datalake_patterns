# Delivery Plan and Acceptance Criteria

## 1. Purpose

This document proposes how the Data Lake pattern framework engagement should be structured and how outputs should be accepted.

The aim is to avoid buying generic consultancy time. The engagement should produce reusable architecture and implementation assets that UCL can maintain and use after the supplier engagement ends.

## 2. Proposed delivery phases

## Phase 0 - Mobilisation and alignment

### Objective

Confirm scope, principles, stakeholders, existing assets and target artefacts.

### Activities

- Review UCL Integration pattern/molecule approach.
- Review existing Data Architecture transformation goals.
- Review Fabric/Purview current state and existing standards.
- Confirm target audiences: Solution Architects, Data Solution Architects, platform engineers, federated users, governance roles.
- Confirm initial use cases to validate the framework.

### Outputs

- Confirmed scope and assumptions.
- Confirmed artefact list.
- Confirmed pattern taxonomy structure.
- Confirmed working cadence.

### Acceptance criteria

- Supplier demonstrates understanding of the Integration pattern/molecule model.
- Supplier confirms how that model will be adapted for Data Lake/Fabric.
- UCL confirms initial candidate patterns and use cases.

---

## Phase 1 - Pattern framework and taxonomy

### Objective

Create the high-level Data Lake pattern framework.

### Activities

- Define what a Data Lake pattern is.
- Define pattern categories.
- Define pattern naming and numbering.
- Define pattern documentation template.
- Define pattern decision tree.
- Define pattern lifecycle and ownership.

### Outputs

- Data Lake Pattern Framework.
- Pattern Taxonomy.
- Pattern Template.
- Pattern Decision Tree.
- Pattern Governance and Ownership Model.

### Acceptance criteria

- A Solution Architect can understand which pattern to use for a common use case.
- The framework clearly separates high-level design from low-level implementation.
- Each pattern category has clear purpose and boundaries.

---

## Phase 2 - Molecule catalogue

### Objective

Define the reusable Data Lake molecule catalogue.

### Activities

- Define what a Data Lake molecule is.
- Identify candidate molecules.
- Group molecules into lifecycle categories.
- Define molecule documentation template.
- Map each molecule to potential Fabric/Purview implementation components.
- Identify which molecules should become centrally managed code/templates.

### Outputs

- Data Lake Molecule Catalogue.
- Molecule Documentation Template.
- Molecule-to-Implementation Mapping.
- Initial configuration model.

### Acceptance criteria

- Molecules are understandable at SA level.
- Molecules are precise enough for technical implementation.
- Molecules are reusable across multiple patterns.
- Molecules include governance and operational considerations, not just technical steps.

---

## Phase 3 - Worked end-to-end patterns

### Objective

Create a small number of complete worked patterns to prove the model.

### Recommended initial patterns

1. Source File to Governed Gold Data Product.
2. API Source to Silver Curated Data Product.
3. Bronze to Silver Standardisation and Quality.
4. Silver to Gold Dimensional Reporting Model.
5. Governed Operational Reporting Product.

### Activities

- Document each pattern using the pattern template.
- Define molecule sequence.
- Define logical architecture.
- Define governance and ownership requirements.
- Define low-level Fabric/Purview mapping.
- Define reusable implementation assets.
- Define assurance checklist.

### Outputs

- 3 to 5 complete worked patterns.
- Pattern diagrams.
- Molecule composition maps.
- Fabric/Purview implementation mapping.
- Assurance checklist for each pattern.

### Acceptance criteria

- A Solution Architect can use each pattern to produce an HLD.
- A Data Platform engineer can understand the implementation route.
- Governance requirements are embedded by design.
- The pattern is specific enough to reduce bespoke design effort.

---

## Phase 4 - Implementation accelerator blueprint

### Objective

Define the central implementation kit equivalent to EASIKit for Data Lake/Fabric.

### Activities

- Identify reusable code libraries.
- Identify reusable notebooks.
- Identify reusable pipeline templates.
- Identify Terraform/IaC or deployment modules.
- Define configuration model.
- Define metadata structures.
- Define DQ and reconciliation framework.
- Define Purview registration helper approach.
- Define monitoring and operational runbook framework.

### Outputs

- DataLakeKit/FabricKit blueprint.
- Reusable asset inventory.
- Configuration model.
- Implementation roadmap.
- Ownership and maintenance model.

### Acceptance criteria

- The blueprint clearly identifies what should be centrally managed.
- The blueprint distinguishes between configurable federation and central engineering control.
- The blueprint can be used to plan development of reusable implementation assets.

---

## Phase 5 - Validation and knowledge transfer

### Objective

Validate the framework against real or representative UCL use cases and transfer knowledge to UCL teams.

### Activities

- Walk through selected use cases.
- Apply pattern selection decision tree.
- Compose molecule sequence.
- Map to low-level implementation.
- Review with Solution Architects, Data Architecture, Data Platform and Governance.
- Refine based on feedback.
- Deliver training/knowledge transfer.

### Outputs

- Validated pattern examples.
- Updated pattern/molecule catalogue.
- Training material.
- Handover pack.
- Recommendations for next phases.

### Acceptance criteria

- UCL stakeholders can explain and apply the model.
- Feedback has been incorporated.
- Ownership of ongoing maintenance is clear.
- Next steps for implementation are clear.

---

# 3. Definition of done

The engagement is complete when UCL has:

- a documented Data Lake pattern framework;
- a documented molecule catalogue;
- at least 3 to 5 worked end-to-end patterns;
- mapping from patterns/molecules to Fabric/Purview implementation;
- a proposed central implementation kit blueprint;
- templates for future patterns and molecules;
- assurance checklists;
- adoption and handover material;
- a prioritised roadmap for further pattern and implementation asset development.

# 4. Quality criteria

Each pattern should be assessed against the following quality criteria:

| Criterion | Description |
|---|---|
| Clear purpose | The pattern has a clearly defined use case and scope. |
| Safe for SAs | A Solution Architect can use it without deep Fabric engineering expertise. |
| Implementation-ready | It maps clearly to Fabric/Purview implementation components. |
| Governed by design | Ownership, stewardship, glossary, quality, lineage and access are included. |
| Reusable | The pattern can be applied across multiple portfolios/use cases. |
| Configurable | Variation is handled through configuration wherever possible. |
| Supportable | Monitoring, alerting, reprocessing and runbooks are included. |
| Assurable | Architecture and governance checkpoints are clear. |
| Maintainable | Ownership and update process are defined. |

# 5. Supplier deliverable checklist

Suppliers should explicitly confirm whether they will deliver:

- [ ] Data Lake pattern taxonomy.
- [ ] Data Lake pattern template.
- [ ] Data Lake molecule definition.
- [ ] Data Lake molecule catalogue.
- [ ] Molecule-to-Fabric mapping.
- [ ] Molecule-to-Purview mapping.
- [ ] Pattern decision tree.
- [ ] 3 to 5 worked patterns.
- [ ] Low-level implementation diagrams.
- [ ] Reusable asset blueprint.
- [ ] Configuration model.
- [ ] Governance model.
- [ ] Assurance checklist.
- [ ] Operational/support model.
- [ ] Knowledge transfer material.
- [ ] Maintenance roadmap.

# 6. Key risks to manage

## Risk 1 - Outputs remain too high-level

### Mitigation

Require every pattern to include low-level Fabric/Purview mapping and implementation asset recommendations.

## Risk 2 - Outputs become too technical for Solution Architects

### Mitigation

Maintain separate high-level pattern and low-level implementation views.

## Risk 3 - Patterns ignore governance

### Mitigation

Include Purview, ownership, glossary, lineage, quality and access controls in every pattern.

## Risk 4 - Federation creates inconsistency

### Mitigation

Use centrally managed building blocks with controlled configuration.

## Risk 5 - Supplier creates artefacts UCL cannot maintain

### Mitigation

Require templates, ownership model, knowledge transfer and a maintenance process.

## Risk 6 - Pattern work becomes generic Fabric delivery support

### Mitigation

Tie supplier activities to named framework deliverables and acceptance criteria.
