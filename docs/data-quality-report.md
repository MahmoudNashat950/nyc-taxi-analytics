# Data Quality Report

## Source profile

- Dataset: NYC TLC Yellow Taxi, August 2026.
- Input volume: **3,336,716 rows**.
- Source schema: **21 columns**.

## Silver processing

The Silver transformation includes:

- Standardizing column names to snake_case.
- Deriving trip duration, pickup date, and pickup hour.
- Applying timestamp validation.
- Removing exact duplicate records across the source columns.
- Creating quality flags for suspicious duration, distance, and financial values.
- Preserving missing values rather than silently imputing them.
- Quarantining records where the drop-off timestamp precedes the pickup timestamp.

## Results

| Check | Result | Treatment |
|---|---:|---|
| Final Silver rows | 3,336,706 | Retained |
| Final Silver columns | 32 | Enriched schema |
| Reversed timestamp records | 3 | Quarantined |
| Exact duplicate rows removed | 7 | Removed |
| Zero-duration trips | 43,175 | Flagged |
| Zero-distance trips | 119,050 | Flagged |
| Trips over 100 miles | 135 | Flagged as suspicious |
| Trips over 1,000 miles | 40 | Flagged as extreme |
| Negative fare amounts | 14,049 | Retained and flagged |
| Negative total amounts | 14,493 | Retained and flagged |

Suspicious distance and financial values were not automatically deleted. They were retained with quality flags to avoid making unsupported assumptions about the business meaning of those records.

## Gold aggregates

| Dataset | Rows | Grain |
|---|---:|---|
| gold_demand | 99,579 | Pickup date + pickup hour + pickup zone |
| gold_revenue | 7,442 | Pickup date + pickup zone |
| gold_trip_performance | 7,442 | Pickup date + pickup zone |

## Reporting-period scope

The Silver dataset contains 15 records outside August 2026 (one from 2009-01-01 and fourteen from 2026-07-31). They remain in Silver. The SQL fact tables are scoped to August 2026, which explains why their row counts are lower than the corresponding full-Silver Gold aggregates.

## Data quality principles

- Preserve source information wherever possible.
- Quarantine clearly invalid timestamp sequences.
- Remove exact duplicates, not merely similar-looking trips.
- Flag suspicious values instead of assuming they are invalid.
- Keep NULLs visible for downstream analysis.
