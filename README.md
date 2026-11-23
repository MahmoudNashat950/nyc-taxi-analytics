# NYC Taxi Analytics – Azure Data Engineering Platform

An end-to-end Azure batch data engineering project that transforms NYC TLC Yellow Taxi trip records into analytics-ready datasets and a dimensional model in Azure SQL.

## Business objectives

- Analyze taxi demand by pickup zone, date, and hour.
- Understand revenue patterns across zones and dates.
- Compare average trip revenue, duration, and distance.
- Identify temporal and geographic demand patterns.

## Architecture

```text
NYC TLC Yellow Taxi Parquet + Taxi Zone Lookup CSV
                         |
                         v
             Azure Data Lake Storage Gen2
                       Bronze
                         |
                         v
              Azure Databricks / PySpark
                  Cleaning & validation
                         |
                         v
                      Silver
                         |
                         v
                 Gold aggregations
              Demand / Revenue / Performance
                         |
             +-----------+-----------+
             |                       |
             v                       v
       ADLS Gold              Azure Data Factory
                                     |
                                     v
                              Azure SQL Database
                                Star Schema
                                     |
                                     v
                              Power BI (planned)
```

## Dataset

Source: NYC Taxi & Limousine Commission (TLC).

- Yellow Taxi Trip Records – August 2026.
- Taxi Zone Lookup.
- Raw trip dataset: **3,336,716 rows**, **21 columns**.

## Technology stack

- Azure Data Lake Storage Gen2
- Azure Databricks Serverless
- Apache Spark / PySpark
- Azure Data Factory
- Azure SQL Database
- Power BI (next milestone)
- Git and GitHub

## Processing summary

### Bronze

Stores the source Parquet and Taxi Zone Lookup files in their original form.

### Silver

- Standardizes names and types.
- Derives trip duration, pickup date, and pickup hour.
- Validates timestamp sequences.
- Quarantines 3 records with drop-off earlier than pickup.
- Removes 7 exact duplicate records.
- Flags suspicious distance and financial values while retaining them for analysis.
- Final Silver output: **3,336,706 rows**, **32 columns**.

### Gold

| Dataset | Rows | Grain |
|---|---:|---|
| Demand | 99,579 | Pickup date + hour + pickup zone |
| Revenue | 7,442 | Pickup date + pickup zone |
| Trip performance | 7,442 | Pickup date + pickup zone |

## Azure SQL star schema

| Table | Rows | Grain / purpose |
|---|---:|---|
| DimDate | 31 | Calendar dimension |
| DimZone | 265 | Taxi zone dimension |
| DimHour | 24 | Hour dimension |
| FactDailyZoneMetrics | 7,430 | Date + pickup zone |
| FactHourlyDemand | 99,566 | Date + hour + pickup zone |

Both fact tables passed referential-integrity validation with **zero unmatched dimension keys**.

The Silver and Gold outputs include 15 records outside August 2026; SQL fact loads are scoped to August 2026. This explains the difference between Gold aggregate counts and SQL fact counts.

## ADF orchestration

The master pipeline `PL_Master_NYC_Taxi_Analytics` executes these pipelines sequentially:

1. `PL_Load_DimDate`
2. `PL_Load_DimZone`
3. `PL_Load_DimHour`
4. `PL_Load_FactDailyZoneMetrics`
5. `PL_Load_FactHourlyDemand`

The latest recorded master run completed successfully. The pipelines currently use a destructive full-refresh strategy; see the runbook before triggering a refresh.

## Documentation

- [Architecture](docs/architecture.md)
- [Data Quality Report](docs/data-quality-report.md)
- [Star Schema](docs/star-schema.md)
- [ADF Pipeline Runbook](docs/pipeline-runbook.md)

## Project status

- [x] Azure infrastructure and ADLS Gen2
- [x] Databricks Serverless and managed-identity access
- [x] Bronze data profiling
- [x] Silver transformations, validation, deduplication, and quarantine
- [x] Gold demand, revenue, and trip-performance aggregates
- [x] Azure SQL dimensions and fact tables
- [x] ADF child pipelines and master orchestration
- [x] End-to-end pipeline run and SQL data-integrity validation
- [ ] Power BI semantic model and dashboard

## Security

Credentials, access keys, and sensitive Azure resource identifiers are intentionally excluded from this repository documentation.
