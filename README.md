# Data Lake Patterns

## Purpose

This repository contains documentation defining a reusable Data Lake / Microsoft Fabric pattern framework.

The intent is to replicate the successful architectural approach used for UCL's Integration Strategy, where:

- Solution Architects could safely design integration flows without needing deep integration engineering knowledge.
- Developers could implement those flows using centrally governed reusable building blocks.
- EASIKit standardised implementation through centrally managed Python libraries, Terraform modules and repeatable deployment assets.

The Data Lake equivalent should allow:

- Solution Architects to safely design end-to-end data use cases.
- Federated users and delivery teams to build safely using centrally managed Fabric/Purview building blocks.
- Data Architecture to provide guardrails, design language, reusable patterns and assurance rather than becoming a bottleneck for every delivery.

## Files in this repository

1. `01_data_lake_patterns_model.md`  
   Describes the proposed Data Lake pattern model, based on the Integration pattern/molecule approach.

2. `03_data_lake_molecule_catalogue_draft.md`  
   Draft catalogue of Data Lake molecules equivalent to the Integration molecule catalogue.

3. `04_example_end_to_end_patterns.md`  
   Example end-to-end Data Lake patterns showing how molecules combine into repeatable designs.

4. `05_delivery_plan_acceptance_criteria.md`  
   Proposed delivery structure, outputs and acceptance criteria.

## Core Goal

The goal is to create a reusable Data Lake pattern framework that turns complex Microsoft Fabric, Purview, data engineering, governance and operational practices into a safe design language for Solution Architects and a governed implementation framework for federated delivery teams.
