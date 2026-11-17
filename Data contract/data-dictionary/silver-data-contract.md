# Silver Layer – Data Contract

## Output
`silver.yellow_taxi_trips`

## Source
`bronze/yellow_tripdata_2026-08.parquet`

## Responsibilities

### Member 3 – Silver Owner
- Read source Parquet.
- Validate and clean records.
- Handle missing and invalid values according to agreed rules.
- Apply consistent data types.
- Write the final Silver dataset.

### Member 4 – Gold Owner
- Consume the approved Silver dataset.
- Join Taxi Zone Lookup.
- Build business aggregations.
- Create Gold analytical datasets.

## Silver Output Columns

Preserve the original 21 source columns, using consistent naming:

| Original | Silver |
|---|---|
| VendorID | vendor_id |
| tpep_pickup_datetime | pickup_datetime |
| tpep_dropoff_datetime | dropoff_datetime |
| passenger_count | passenger_count |
| trip_distance | trip_distance |
| RatecodeID | ratecode_id |
| store_and_fwd_flag | store_and_fwd_flag |
| PULocationID | pickup_location_id |
| DOLocationID | dropoff_location_id |
| payment_type | payment_type |
| fare_amount | fare_amount |
| extra | extra |
| mta_tax | mta_tax |
| tip_amount | tip_amount |
| tolls_amount | tolls_amount |
| improvement_surcharge | improvement_surcharge |
| total_amount | total_amount |
| congestion_surcharge | congestion_surcharge |
| Airport_fee | airport_fee |
| cbd_congestion_fee | cbd_congestion_fee |
| request_source | request_source |

## Derived Columns
- trip_duration_minutes
- pickup_date
- pickup_hour

## Output Format
Parquet

## Output Path
`abfss://silver@stnyctaxide2026.dfs.core.windows.net/yellow_taxi/`

## Important Rules
- Do not silently drop records.
- Document all cleaning rules.
- Preserve the original Bronze data.
- Agree on null-handling rules before implementation.
- Member 4 must use the approved Silver schema.
