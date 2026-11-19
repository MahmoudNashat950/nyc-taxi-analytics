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
