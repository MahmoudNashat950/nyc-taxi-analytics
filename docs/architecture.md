# Architecture

## Overview

NYC Taxi Analytics is an Azure batch data engineering platform that transforms NYC TLC Yellow Taxi trip data into analytics-ready dimensional models.

## Data flow

1. **Source** — NYC TLC Yellow Taxi Parquet data for August 2026 and the Taxi Zone Lookup CSV.
2. **Bronze (ADLS Gen2)** — stores the source files in their raw form.
3. **Silver (Azure Databricks / PySpark)** — standardizes column names and types, derives trip attributes, validates timestamps, removes exact duplicates, and flags suspicious records. Invalid timestamp records are written to a quarantine location.
4. **Gold (Databricks)** — produces demand, revenue, and trip-performance aggregates.
5. **Azure Data Factory** — orchestrates the sequential loading of dimensions and fact tables.
6. **Azure SQL Database** — serves the star schema for downstream analytics.
7. **Power BI** — planned reporting layer; dashboard implementation is not yet complete.

## Azure services

- Azure Data Lake Storage Gen2: Bronze, Silver, and Gold storage.
- Azure Databricks Serverless with PySpark: data transformation and aggregation.
- Azure Data Factory: pipeline orchestration and copy activities.
- Azure SQL Database: dimensional serving layer.
- Managed identities: authentication between Azure services.

## ADF orchestration

The master pipeline `PL_Master_NYC_Taxi_Analytics` runs these child pipelines sequentially:

1. `PL_Load_DimDate`
2. `PL_Load_DimZone`
3. `PL_Load_DimHour`
4. `PL_Load_FactDailyZoneMetrics`
5. `PL_Load_FactHourlyDemand`

## Storage layers

- Bronze: original source data.
- Silver: cleaned and validated trip-level data, with invalid timestamp records quarantined.
- Gold: aggregated Parquet datasets and SQL staging outputs.

## Security

Databricks accesses the data lake through its configured managed-identity / access-connector setup. Azure Data Factory uses its system-assigned managed identity for Azure SQL access. No credentials, keys, or resource identifiers are stored in this documentation.
