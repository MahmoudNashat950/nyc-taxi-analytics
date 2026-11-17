# NYC Taxi Analytics – Azure Data Engineering Platform

An end-to-end batch data engineering project built on Microsoft Azure to transform NYC Yellow Taxi trip data into analytics-ready datasets and business insights.

## Project Overview

The project processes NYC Yellow Taxi trip records to help analyze:

- Trip demand across pickup and drop-off zones.
- Revenue and fare distribution.
- Peak hours and trip patterns.
- Geographic and temporal transportation trends.

## Architecture

```text
NYC TLC Dataset (Parquet + CSV)
             |
             v
Azure Data Lake Storage Gen2
          (Bronze)
             |
             v
Azure Databricks – PySpark
             |
             v
       Silver Layer
   (Cleaning & Validation)
             |
             v
        Gold Layer
 (Business Transformations)
             |
             v
          Azure SQL
       (Star Schema)
             |
             v
          Power BI
       (Dashboards)
```

Azure Data Factory will be used for pipeline orchestration.

## Dataset

Source: NYC Taxi & Limousine Commission (TLC)

Selected dataset:
- Yellow Taxi Trip Records – August 2026
- Taxi Zone Lookup

The raw trip dataset contains:
- 3,336,716 records
- 21 columns

## Technology Stack

- Microsoft Azure
- Azure Data Lake Storage Gen2
- Azure Databricks Serverless
- Apache Spark / PySpark
- Azure Data Factory
- Azure SQL Database
- Power BI
- Git & GitHub

## Data Lake Organization

```text
ADLS Gen2
├── bronze/
├── silver/
└── gold/
```

## Repository Structure

```text
nyc-taxi-analytics/
├── notebooks/
│   ├── bronze/
│   ├── silver/
│   └── gold/
├── src/
│   ├── transformations/
│   └── utilities/
├── sql/
│   ├── ddl/
│   └── queries/
├── adf/
│   └── pipelines/
├── powerbi/
├── tests/
└── docs/
```

## Team Collaboration

The project is developed collaboratively using GitHub.

- Feature branches for individual tasks.
- Pull Requests for code reviews.
- Main branch reserved for integrated and reviewed work.

## Project Status

- [x] Azure infrastructure setup
- [x] ADLS Gen2 storage setup
- [x] Databricks Serverless setup
- [x] Managed Identity authentication
- [x] Initial source data profiling
- [ ] Silver transformations
- [ ] Gold aggregations
- [ ] Azure SQL dimensional model
- [ ] Power BI dashboard
- [ ] ADF orchestration
- [ ] End-to-end testing

## Project Goal

Build a reproducible, scalable batch data pipeline that transforms raw transportation data into structured analytical datasets for business intelligence.
