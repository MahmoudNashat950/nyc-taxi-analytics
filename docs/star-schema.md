# Azure SQL Star Schema

The Azure SQL serving layer contains three dimensions and two fact tables.

## Dimensions

| Table | Rows | Key | Description |
|---|---:|---|---|
| DimDate | 31 | date_key | Calendar attributes for August 2026 |
| DimZone | 265 | location_id | Taxi zone, borough, and service-zone attributes |
| DimHour | 24 | hour_key | Hour label and time-period attributes |

## Fact tables

### FactDailyZoneMetrics

- Rows: **7,430**
- Grain: one row per **date + pickup zone**.
- Measures: total trips, total revenue, average trip revenue, average trip duration, and average trip distance.
- Dimensions: DimDate and DimZone.

### FactHourlyDemand

- Rows: **99,566**
- Grain: one row per **date + pickup hour + pickup zone**.
- Measure: total trips.
- Dimensions: DimDate, DimHour, and DimZone.

## Data integrity

Referential-integrity validation returned zero unmatched dimension keys for both fact tables.

The fact-table row counts are limited to the August 2026 reporting period. The Gold aggregates were generated from the full Silver dataset, which includes 15 out-of-period records.

## Modeling notes

The declared grain is the basis for each fact table's composite key and prevents duplicate dimensional combinations. Dimension tables are loaded before facts so that fact records can reference existing dimension members.
