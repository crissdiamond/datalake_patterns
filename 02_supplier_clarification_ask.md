# Supplier Clarification Ask: Reusable Data Lake Patterns

## 1. Purpose of this clarification

UCL is asking for support to create a reusable Data Lake / Microsoft Fabric pattern framework.

This is not a request for generic Fabric consultancy, isolated technical recommendations, or general delivery capacity.

The request is to help UCL define a pattern-based architecture and implementation model equivalent in intent to the approach previously used for Integration.

## 2. Problem to solve

UCL needs to enable Solution Architects and federated delivery teams to design and deliver Data Lake use cases safely and consistently.

At present, without a mature pattern framework, Data Lake work risks becoming:

- dependent on a small number of central experts;
- inconsistent across portfolios;
- overly bespoke;
- difficult to govern;
- difficult to assure;
- difficult to support operationally;
- disconnected from Purview, data ownership, glossary, lineage and quality controls.

## 3. Desired operating principle

The operating principle is:

> Centralise standards, reusable implementation assets and assurance; federate safe configuration and delivery.

This means:

- Solution Architects should design using high-level patterns and molecules.
- Federated teams should implement using centrally approved building blocks.
- Data Architecture should provide guardrails and assurance rather than designing every detail.
- Data Platform should provide reusable implementation assets rather than bespoke builds for every use case.
- Governance should be embedded into patterns rather than added afterwards.

## 4. Required supplier contribution

The supplier should help UCL define, validate and document:

1. A Data Lake pattern taxonomy.
2. A Data Lake molecule catalogue.
3. A mapping from high-level patterns to low-level Fabric/Purview implementation.
4. A set of worked end-to-end patterns.
5. A reusable implementation approach based on central libraries, templates and configuration.
6. Pattern documentation templates for Solution Architects and implementation teams.
7. Governance, security and operational guardrails embedded into each pattern.
8. Recommendations for how these patterns should be maintained and assured.

## 5. Expected deliverables

The engagement should produce tangible, reusable artefacts. At minimum, UCL expects:

### 5.1 Pattern framework

- Pattern taxonomy.
- Pattern naming convention.
- Pattern documentation template.
- Pattern decision tree.
- Pattern lifecycle and ownership model.

### 5.2 Molecule catalogue

- Definition of a Data Lake molecule.
- Initial molecule catalogue.
- Molecule documentation template.
- Mapping of molecules to Fabric/Purview implementation components.
- Configuration model for each molecule where applicable.

### 5.3 End-to-end worked patterns

At least 3 to 5 worked patterns should be produced, for example:

- File/API/database source to Bronze/Silver/Gold governed data product.
- Bronze to Silver standardisation and data quality pattern.
- Silver to Gold dimensional reporting pattern.
- Gold semantic model / Power BI pattern.
- Purview registration, ownership, lineage and access pattern.

### 5.4 Low-level implementation mapping

Each worked pattern should show how the high-level design translates into:

- Fabric workspaces;
- Lakehouses;
- Warehouses;
- pipelines;
- notebooks;
- semantic models;
- deployment pipelines;
- access controls;
- Purview assets;
- lineage;
- monitoring;
- support runbooks;
- reusable code/templates/modules.

### 5.5 Implementation accelerator view

The supplier should propose the structure of a central implementation kit equivalent to EASIKit for Data Lake/Fabric.

This should include:

- reusable Python/PySpark libraries;
- reusable Fabric notebooks;
- reusable pipeline templates;
- Terraform/IaC or deployment modules;
- YAML/JSON configuration model;
- data quality rule configuration;
- metadata tables;
- audit and reconciliation framework;
- Purview registration helpers;
- monitoring and alerting framework.

### 5.6 Adoption and handover material

The supplier should produce materials that can be used by:

- Solution Architects;
- Data Solution Architects;
- Data Platform engineers;
- federated portfolio teams;
- Data Owners and Data Stewards where relevant.

This should include:

- SA playbook;
- engineering playbook;
- worked examples;
- training material;
- assurance checklist;
- decision log template;
- pattern exception process.

## 6. Required response from suppliers

Suppliers should explain:

1. How they would structure the work.
2. Which artefacts they would produce.
3. Which patterns they recommend delivering first.
4. How they would define Data Lake molecules.
5. How they would map molecules to Fabric/Purview implementation.
6. What reusable assets they would create or recommend.
7. How they would support knowledge transfer.
8. How they would help UCL maintain the pattern library after the engagement.
9. What assumptions, dependencies and exclusions apply.
10. What UCL resources they require.

## 7. What UCL does not want

UCL does not want the engagement to result only in:

- high-level slides;
- generic Fabric advice;
- unmanaged sprint capacity;
- isolated prototypes with no reusable pattern documentation;
- implementation assets that UCL cannot maintain;
- patterns that do not map to real Fabric/Purview components;
- technical implementation without governance, ownership, lineage and operational controls;
- governance recommendations that cannot be implemented in the platform.

## 8. Success criteria

The engagement will be successful if UCL can use the outputs to:

- let Solution Architects design Data Lake use cases safely using patterns;
- let federated teams implement through approved building blocks;
- reduce bespoke design effort;
- improve consistency across portfolios;
- embed governance and Purview by design;
- create a maintainable pattern/molecule catalogue;
- define a practical path towards a centrally managed Data Lake implementation kit.
