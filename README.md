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

1. `01_model_and_principles.md`  
   Defines the overall Data Lake pattern model and how it maps from Integration.

2. `02_pattern_catalogue.md`  
   Main document: the proposed pattern catalogue grouped into Ingestion, Transformation, Data Product, Governance and Operational patterns.

3. `03_pattern_template.md`  
   Standard template each pattern should follow.

4. `04_example_patterns.md`  
   Worked examples showing how selected patterns should be described.

5. `05_molecules_as_building_blocks.md`  
   Explains how molecules sit below patterns as reusable implementation units.
