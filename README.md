# Data Lake Pattern Catalogue and Framework

## Purpose

This document defines the approach for the patternisation of data lake design and operations, aiming to establish a standardized framework for architecture, development, and governance.

This framework organizes the **Data Lake pattern catalogue** into five core groups:

1. Ingestion patterns
2. Transformation patterns
3. Data product patterns
4. Governance patterns
5. Operational patterns

The molecule concept is still useful, but it should sit underneath the pattern catalogue as the reusable building-block language, in the same way that Integration patterns were decomposed into lower-level molecules and then implemented through EASIKit, Python libraries and Terraform modules.

## Strategic intent

The objective is to reproduce the successful Integration approach in the Data Lake / Microsoft Fabric space:

- enable Solution Architects to safely design end-to-end data use cases without deep Fabric/data engineering expertise;
- enable federated users and delivery teams to use centrally managed building blocks safely;
- map high-level patterns to low-level Fabric/Purview implementation;
- create reusable code/configuration assets rather than bespoke delivery every time;
- embed governance, operations, ownership, lineage, quality and access into the patterns from the start.

## Documents

**Framework core**

1. `01_model_and_principles.md`  
   The overall Data Lake pattern model, how it maps from Integration, and the design principles (including honest leverage types and the EDM dependency).

2. `02_pattern_catalogue.md`  
   Main document: the pattern catalogue (index + scope) grouped into Ingestion, Transformation, Data Product, Governance and Operational patterns, with the Standardized Ingestion Gateway, ingestion modes and Schema Registry.

3. `03_pattern_template.md`  
   The single canonical template every completed pattern must follow.

4. `04_example_patterns.md`  
   Five fully worked examples (one per group) following the template — the quality bar.

5. `05_molecules_as_building_blocks.md`  
   How molecules sit below patterns as reusable implementation units, with the molecule ID scheme.

**Enabling documents**

6. `06_cross_cutting_concerns.md`  
   Concerns that recur in every pattern: the Ingestion gateway (control vs data plane) and modes, the Schema Registry, security/identity, environments, capacity, the Fabric-native-first stance, and governance enforcement.

7. `07_pattern_selection_guide.md`  
   A decision map helping Solution Architects choose and compose patterns.

8. `08_operating_model.md`  
   How the framework itself is run: RACI, pattern lifecycle, federation/contribution model, the EDM dependency, and success metrics.

9. `09_supplier_engagement_brief.md`  
   The brief for external partners: scope, priorities, deliverable definition, definition-of-done and engagement model.
