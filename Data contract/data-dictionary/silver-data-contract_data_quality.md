## Data Profiling Findings – August 2026

### Source Dataset
- Total Records: 3,336,716
- Total Columns: 21

### Missing Data Findings

| Column | Missing Records | Decision |
|---|---:|---|
| passenger_count | 921,181 | Preserve NULL |
| RatecodeID | 921,181 | Preserve NULL |
| store_and_fwd_flag | 921,181 | Preserve NULL |
| request_source | 2,415,867 | Preserve NULL |

The first three columns exhibit identical missingness patterns: all three are NULL together in 921,181 records.

Missingness varies by VendorID. Vendor 6 has all records missing the first three fields, while Vendor 7 has none of these fields missing.

### Duplicate Findings
- Potential duplicate groups based on selected business columns: 19,245.
- Exact duplicate groups across all 21 columns: 7.
- Extra rows belonging to exact duplicate groups: 7.

### Financial Data Findings
Negative financial values exist across multiple financial columns. They will be preserved pending business-rule validation and may be flagged for downstream analysis.

### Preliminary Silver Decisions
- Preserve original financial values.
- Preserve NULL values; do not replace unknown values with zero.
- Investigate invalid timestamps.
- Remove only verified exact duplicate rows.
- Keep potential duplicates for further investigation.
- Add data quality flags where appropriate.

*Note: These are preliminary profiling findings, not a finalized source-wide business validation policy.*

### Timestamp Quality Rules

- Records where dropoff_datetime < pickup_datetime
  are excluded from the Silver valid dataset.
- These records are preserved in a separate quarantine dataset.
- Records where dropoff_datetime == pickup_datetime
  are retained but flagged as zero-duration trips.
- No original Bronze records are deleted or modified.

### Current Profiling Results

- Reversed timestamps: 3 records.
- Zero-duration trips: 43,182 records.


### Trip Distance Quality Rules

- Negative trip distances are invalid and should be quarantined if encountered.
- Zero-distance trips are retained with a quality flag.
- Distances above 100 miles are flagged as suspicious.
- Distances above 1,000 miles are marked as quarantine candidates
  pending final validation.
- Original values are preserved in Bronze.

### Profiling Findings

- 135 records have trip_distance > 100 miles.
- 40 records have trip_distance > 1,000 miles.
- All 40 extreme-distance records belong to VendorID 2.
- 39 of the 40 records have payment_type = 0.


- 119,059 records have zero trip distance (approximately 3.57%).
- No NULL or negative trip distances were found.

### Financial Quality Findings

- 3,200 records have zero fare.
- 1,334 records have zero total amount.
- 14,049 records have negative fare.
- 14,493 records have negative total amount.
- congestion_surcharge and Airport_fee each contain
  921,181 NULL records.
- Negative financial values are concentrated in
  payment types 3 and 4.

### Financial Quality Rules

- Preserve original financial values.
- Retain zero and negative financial records.
- Add flags for zero and negative fare/total values.
- Preserve NULL optional surcharge values.
- Do not replace NULL values with zero.
- Do not quarantine records based on financial values alone.


### Duplicate Handling Rules

- Exact duplicate records across all source columns
  are deduplicated in the Silver layer.
- Only one copy of each exact record is retained.
- Potential duplicates based on partial column matching
  are preserved.
- Bronze source data remains unchanged.

### Duplicate Profiling Findings

- 7 exact duplicate groups were identified.
- Each group contains exactly 2 identical records.
- 7 extra rows will be removed during Silver processing.
- 19,245 potential duplicate groups were identified
  using a subset of columns and will be preserved.
