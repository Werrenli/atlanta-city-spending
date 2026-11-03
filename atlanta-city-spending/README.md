# Where does Atlanta's money go?

An analysis of **219,012 payment records** from the City of Atlanta's FY2025 open checkbook.

> **TODO — your headline finding goes here.** One sentence. The thing a city council
> member would want to know if they read nothing else. Write this *last*, but put it *first*.

---

## The question

Atlanta publishes every payment it makes. Almost nobody reads it.

This project asks four things a council member, journalist, or resident might reasonably want answered:

1. Which **departments** absorb the most spending?
2. Which **vendors** receive the most, and what share goes to the largest few?
3. Does spending **arrive evenly through the year**, or does it spike?
4. What does the **payment method mix** say about how the city transacts?

This is a **descriptive** analysis. It does not claim to identify waste, fraud, or
impropriety, and large payments to utilities and infrastructure contractors are what a
functioning city looks like.

## The data

| | |
|---|---|
| **Source** | [City of Atlanta Open Checkbook Ledger](https://atlanta.data.socrata.com/d/jmke-icfi) |
| **Scope** | Fiscal Year 2025 |
| **Records** | 219,012 |
| **Columns** | 18 |
| **Retrieved** | TODO — date you downloaded it |
| **Tool** | Microsoft Excel |

### Why FY2025 only

The full ledger holds roughly **1.8 million records** across FY2017–2027 — more than
Excel's 1,048,576-row limit. Three years (FY2023–25) would fit at 632,013 rows but make
PivotTables slow enough to discourage exploration.

One closed fiscal year is enough to answer "where does the money go" and small enough to
work with interactively. It is **not** enough to answer "is concentration increasing" —
that needs a time series, and is out of scope here.

Data was filtered server-side rather than downloaded in full:

```
https://atlanta.data.socrata.com/resource/jmke-icfi.csv?$where=fiscal_year=2025&$limit=250000
```

## Data quality

Public data is not clean data. What I checked before analysing anything:

| Check | Finding |
|---|---|
| Row count matches source | TODO |
| `check_date` imported as dates, not text | TODO |
| `amount` is numeric throughout | TODO |
| Negative amounts (refunds / reversals) | TODO — count, and whether included |
| Zero-value rows | TODO |
| Distinct `vendor_id` vs distinct `vendor_name` | TODO — a gap means name variants |
| Distinct departments | TODO |
| `invoice_description` populated | TODO — appeared empty in sampling |
| `project` placeholder values (`DEFAULT`) | TODO |

### Rows are line items, not payments

A single payment can span multiple rows — same `payment_id`, several `amount` values
against different accounts. **Counting rows counts line items, not cheques.**

Payment counts in this analysis use `DISTINCTCOUNT` of `payment_id`.

### Vendor names vs vendor IDs

TODO — if `vendor_name` yields more distinct values than `vendor_id`, the same vendor
appears under multiple spellings and any ranking grouped by name is wrong. Grouped by
`vendor_id`, labelled with `vendor_name`.

## Findings

TODO — one subsection per finding. Lead with the number, then explain it.

### 1. TODO

### 2. TODO

### 3. TODO

## Recommendation

TODO — what should someone *do* about this? This section is the point of the project.
A description without a recommendation is a report; with one, it's analysis.

## Limitations

- Single fiscal year — no year-over-year comparison, so no trend claims
- Descriptive only — identifies patterns, not causes
- Vendor consolidation not attempted beyond ID-level grouping; parent/subsidiary
  relationships are not visible in this data
- TODO — anything else you hit

## What I'd do with more time

TODO

## Repository contents

```
README.md              this case study
data/                  source documentation (raw CSV not committed - see below)
workbook/              the Excel analysis
images/                exported charts
case-study/            one-page PDF version
```

The source CSV is not committed — it is ~40MB of public data available at the URL above,
and version control is a poor place for it. `data/README.md` documents exactly how to
reproduce the extract.

---

**Werren Li** · [github.com/Werrenli](https://github.com/Werrenli)
