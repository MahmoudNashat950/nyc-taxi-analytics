# Azure Data Factory Runbook

## Master pipeline

Pipeline: `PL_Master_NYC_Taxi_Analytics`

The master pipeline executes these activities sequentially:

1. `PL_Load_DimDate`
2. `PL_Load_DimZone`
3. `PL_Load_DimHour`
4. `PL_Load_FactDailyZoneMetrics`
5. `PL_Load_FactHourlyDemand`

The latest recorded end-to-end run completed successfully.

## Full-refresh behavior

The pipelines use pre-copy SQL scripts to clear existing target data before loading refreshed data:

- DimDate load: deletes rows from both fact tables, then clears DimDate.
- DimZone load: clears DimZone.
- DimHour load: clears DimHour.
- FactDailyZoneMetrics load: truncates FactDailyZoneMetrics.
- FactHourlyDemand load: truncates FactHourlyDemand.

**Warning:** this is a destructive full-refresh process. If a pipeline fails after clearing a table but before completing its reload, the serving tables may be partially empty. Inspect the ADF Monitor run and validate target counts before treating the refresh as complete.

## Expected SQL row counts

| Table | Expected rows |
|---|---:|
| DimDate | 31 |
| DimZone | 265 |
| DimHour | 24 |
| FactDailyZoneMetrics | 7,430 |
| FactHourlyDemand | 99,566 |

## Validation queries

### Row counts

```sql
SELECT 'DimDate' AS table_name, COUNT_BIG(*) AS row_count FROM dbo.DimDate
UNION ALL
SELECT 'DimZone', COUNT_BIG(*) FROM dbo.DimZone
UNION ALL
SELECT 'DimHour', COUNT_BIG(*) FROM dbo.DimHour
UNION ALL
SELECT 'FactDailyZoneMetrics', COUNT_BIG(*) FROM dbo.FactDailyZoneMetrics
UNION ALL
SELECT 'FactHourlyDemand', COUNT_BIG(*) FROM dbo.FactHourlyDemand;
```

### Daily fact: unmatched dimensions

```sql
SELECT COUNT_BIG(*) AS unmatched_rows
FROM dbo.FactDailyZoneMetrics f
LEFT JOIN dbo.DimDate d ON d.date_key = f.date_key
LEFT JOIN dbo.DimZone z ON z.location_id = f.location_id
WHERE d.date_key IS NULL OR z.location_id IS NULL;
```

### Hourly fact: unmatched dimensions

```sql
SELECT COUNT_BIG(*) AS unmatched_rows
FROM dbo.FactHourlyDemand f
LEFT JOIN dbo.DimDate d ON d.date_key = f.date_key
LEFT JOIN dbo.DimHour h ON h.hour_key = f.hour_key
LEFT JOIN dbo.DimZone z ON z.location_id = f.location_id
WHERE d.date_key IS NULL OR h.hour_key IS NULL OR z.location_id IS NULL;
```

Both referential-integrity checks should return **0**.

## Operational checklist

1. Confirm the Gold staging Parquet outputs exist.
2. Confirm linked services and managed-identity permissions are valid.
3. Trigger `PL_Master_NYC_Taxi_Analytics`.
4. Inspect every child activity in ADF Monitor.
5. Run the SQL row-count and referential-integrity checks.
6. Treat the refresh as successful only when all activities and validations pass.
