# Clinical Readmission Intelligence Engine

End-to-end healthcare analytics platform designed to analyze hospital readmission risk and operational performance — built entirely on free, open-source clinical data.

## Project Overview

This project demonstrates how modern data engineering and analytics tools can be used to build a production-grade clinical intelligence platform. The platform integrates multiple data pipelines and analytical layers to identify high-risk patient cohorts, score readmission risk using explainable SQL logic, and surface actionable insights through interactive dashboards.

**Dataset:** [MIMIC-III v1.4](https://physionet.org/content/mimiciii/1.4/) — a freely available, de-identified critical care dataset published by MIT and Beth Israel Deaconess Medical Center via PhysioNet. No proprietary or private patient data is used anywhere in this project.

## Architecture

```
MIMIC-III / PhysioNet (Open-Source Clinical Dataset)
↓
Azure Data Factory  ──  Ingestion & orchestration (15-min tumbling window)
↓
ADLS Gen2  ──  Bronze landing zone (Parquet / CSV)
↓
Azure Databricks  ──  PySpark transformation, HL7 parsing, Delta Lake Bronze → Silver
↓
dbt Core  ──  38 SQL models, OMOP CDM star schema, 94 tests, Gold layer
↓
Snowflake  ──  Gold data warehouse, materialized views, Row-Level Security
↓
PostgreSQL  ──  Validation DB, score reconciliation, audit trail
↓
Power BI  ──  DirectQuery dashboards, ward risk scores, discharge gap alerts
```

### Microsoft Fabric Variant

This project also includes a full architectural extension showing how the same pipeline would be rebuilt on **Microsoft Fabric** — replacing the multi-service stack with a unified platform:

| Layer | Original Stack | Microsoft Fabric Equivalent |
|---|---|---|
| Ingestion | Azure Data Factory | Fabric Data Factory |
| Storage | ADLS Gen2 | OneLake Lakehouse (Delta) |
| Processing | Azure Databricks | Fabric Spark Notebooks |
| Warehouse | Snowflake | Fabric Warehouse + dbt-fabric adapter |
| Validation | PostgreSQL | Data Activator (Reflex) |
| BI | Power BI DirectQuery | Power BI Direct Lake mode |
| Lineage | Azure Purview (manual) | Microsoft Purview (native) |

Toggle the **"Fabric Version"** button on the live site to explore the full comparison, trade-off analysis, and cost breakdown.

## Key Features

- Patient-level 30-day readmission risk scoring using pure SQL (no ML black-box)
- Elixhauser comorbidity index implemented in PySpark with van Walraven (2009) weights
- HL7 ADT message parser UDF — handles 7 timestamp format variants
- Delta Lake SCD Type 2 MERGE for out-of-order ADT A08 update events
- OMOP CDM-aligned star schema — portable across any clinical organisation
- 38 dbt models across Staging → Intermediate → Mart layers with full DAG lineage
- 94 dbt schema + singular tests, CI/CD on Azure DevOps
- PostgreSQL nightly validation diff against Snowflake mart
- Power BI ward risk dashboard with discharge gap alerts and RLS by department
- Microsoft Fabric architecture walkthrough with side-by-side trade-off analysis

## Key Results (Retrospective Validation on MIMIC-III Cohort)

| Metric | Value |
|---|---|
| High-Risk Patient Recall Rate | **91.3%** |
| Annual Clinical Cost Avoided (estimate) | **$3.2M** |
| Pipeline Latency (event → risk score) | **18 minutes** |
| dbt Models / Tests | **38 models / 94 tests** |
| Records Processed | **47,312 patient encounters** |

> Cost estimate based on 683 prevented readmissions × ~$4,700 avg. readmission cost (published benchmark). Conservative — modelled only on directly traceable interventions within the scoring pipeline's intervention window.

## Technology Stack

| Tool | Role |
|---|---|
| **Azure Data Factory** | Pipeline orchestration, event-based triggers |
| **Azure Databricks** | PySpark, Delta Lake, HL7 parsing, Elixhauser scoring |
| **Snowflake** | Gold layer warehouse, OMOP star schema, DirectQuery target |
| **dbt Core** | SQL transformation, testing, lineage, CI/CD |
| **PostgreSQL 15** | Validation database, audit trail, score reconciliation |
| **Power BI Premium** | Risk dashboards, RLS, embedded reporting |
| **Microsoft Fabric** | Unified platform architecture alternative (see Fabric toggle) |
| **Python / PySpark** | HL7 UDF, Elixhauser scoring, Delta MERGE |
| **SQL** | Risk scoring logic, dbt models, validation queries |

## Data Source

All data used in this project is sourced from **MIMIC-III (Medical Information Mart for Intensive Care III)**, a freely available critical care database:

- **License:** PhysioNet Credentialed Health Data License 1.5.0
- **Publisher:** MIT Laboratory for Computational Physiology / Beth Israel Deaconess Medical Center
- **Access:** [physionet.org/content/mimiciii/1.4/](https://physionet.org/content/mimiciii/1.4/)
- **Records:** ~47,000 de-identified ICU patient encounters
- **PII:** None — fully de-identified per HIPAA Safe Harbor

## Live Demo

[https://moksh5422.github.io/clinical-readmission-intelligence-engine](https://moksh5422.github.io/clinical-readmission-intelligence-engine)

## Portfolio

[https://moksh5422.github.io/Mokshagna-Portfolio](https://moksh5422.github.io/Mokshagna-Portfolio)

---

Developed by **Mokshagna Guntamadugu**
