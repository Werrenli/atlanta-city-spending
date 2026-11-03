# Data

The raw CSV is **not committed**. It is public data, roughly 40MB, and reproducible
exactly from the URL below.

## Source

**City of Atlanta Open Checkbook Ledger**
Portal: https://atlanta.data.socrata.com/d/jmke-icfi
Dataset ID: `jmke-icfi`
Platform: Socrata

## Reproducing this extract

```
https://atlanta.data.socrata.com/resource/jmke-icfi.csv?$where=fiscal_year=2025&$limit=250000
```

| Parameter | Purpose |
|---|---|
| `/resource/jmke-icfi.csv` | Dataset ID, returned as CSV |
| `$where=fiscal_year=2025` | Server-side filter — only FY2025 rows are sent |
| `$limit=250000` | Row cap. Socrata defaults to 1,000 without it |

Expected: **219,012 rows**. Fewer means the limit truncated the pull.

## Columns

| Column | Type | Notes |
|---|---|---|
| `fiscal_year` | integer | |
| `fiscal_period` | integer | City accounting period |
| `check_date` | date | |
| `fund_type` | text | |
| `fund` | text | |
| `department` | text | Primary breakdown |
| `division` | text | |
| `account` | text | |
| `expense_category` | text | |
| `project` | text | Often the placeholder `DEFAULT` |
| `invoice_description` | text | Appeared empty in sampling |
| `amount` | numeric | The measure |
| `vendor_name` | text | May contain spelling variants |
| `vendor_id` | text | Stable identifier — group on this |
| `payment_id` | text | **One payment may span multiple rows** |
| `payment_method` | text | |
| `payment_status` | text | Appeared empty in sampling |
| `payment_check_num` | text | |

## Record counts by fiscal year

| FY | Records | FY | Records |
|---|---|---|---|
| 2017 | 146,424 | 2023 | 194,585 |
| 2018 | 159,445 | 2024 | 218,416 |
| 2019 | 159,157 | **2025** | **219,012** |
| 2020 | 154,132 | 2026 | 272,019 |
| 2021 | 120,605 | 2027 | 41,068 (partial) |
| 2022 | 163,256 | | |

Total: ~1.8 million records.

## Known defect: `vendor_id` is unusable

Measured on the FY2025 extract (219,012 rows):

| | |
|---|---|
| Rows with `vendor_id` in scientific notation | 210,326 (96%) |
| Distinct `vendor_id` values | 5 |
| Distinct `vendor_name` values | 5,335 |
| Vendors sharing the single id `3E+14` | 4,212 |

Observed values: `3E+14`, `3.00E+14`, `3.00001E+14`, `3.00002E+14`. A 15-digit
identifier rounded to one or six significant figures. The digits are not recoverable
from this file.

This appears to have happened before publication, via a spreadsheet export that
converted long numeric ids to scientific notation.

**Consequence:** do not group or join on `vendor_id`. Grouping by it returns five rows
and no error. Group on `vendor_name` and treat spelling variants as a cleaning step.
