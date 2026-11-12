# Where does Atlanta's money go?

An analysis of **219,012 payment records** from the City of Atlanta's FY2025 open checkbook.

> **Only 20 out of 5,334 vendors received 52% of Atlanta's total spending, adding up to
> $1.26 billion.**

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
| **Retrieved** | 3 November 2026 |
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
| Row count matches source | 219,012 — matches; the extract was not truncated |
| Date range | 2024-07-01 to 2025-06-30 — exactly FY2025, no bleed from adjacent years |
| `check_date` imported as dates | **No** — it arrives as text. Added a `Date` column: `=DATEVALUE(LEFT(C2,10))`, 219,012 values, 0 errors |
| `amount` is numeric throughout | Yes — `COUNT` and `COUNTA` both return 219,012, so no text-stored amounts |
| Negative amounts (refunds / reversals) | 3,053 rows, −$14,124,856 — 1.4% of rows, 0.6% of dollars. **Included** in all totals |
| Zero-value rows | 1,208 (0.55%). No effect on sums; would distort any average |
| Distinct `vendor_id` vs `vendor_name` | 5,334 names against **5 usable IDs** — `vendor_id` is corrupted in the source. See below |
| Distinct departments | 28 — no whitespace or spelling variants |
| `invoice_description` populated | Only 26.9% — **73.1% of rows are blank** |
| `project` values | No blanks, but 95.7% read `NON-PROJECT`, so the field carries little information |
| `payment_id` corruption | 3 values stored in scientific notation (`3.00E+14` and similar) across 60 rows, all `payment_method = CHECK` |

### Rows are line items, not payments

A single payment can span multiple rows — same `payment_id`, several `amount` values
against different accounts. **Counting rows counts line items, not cheques.**

219,012 rows resolve to **42,561 distinct payments** — 5.15 line items per payment. Excel for Mac has no Data Model, so PivotTable Distinct Count is unavailable; distinct counts here were produced with Remove Duplicates on a copy of the column.

### Vendor names vs vendor IDs

`vendor_id` is unusable. 5,334 distinct vendor names map to just **5 distinct IDs**,
because the IDs were exported as 15-digit numbers in scientific notation (`3E+14`,
`3.00001E+14`) and collapsed into each other. This happened before publication — the
strings are in the CSV as downloaded, not introduced on import.

All vendor analysis is therefore grouped by **`vendor_name`**, which carries its own risk:
the same business can appear under more than one name. See Limitations.

## Findings

### 1. Two departments are 71.6% of all spending

The Aviation and Watershed departments make up 71.6% of total spending — Aviation alone
is 55.8%. Both are enterprise funds, meaning they are paid for by landing fees and water
bills rather than taxes. The dataset is mostly an airport and a water utility.

Police Services ($75.4M) and Fire Services ($21.3M) sit far below what those departments
cost to operate, which suggests payroll is excluded from this ledger.

### 2. Spending is flat, with no fiscal year-end surge

Monthly spending ranges only 1.49× between the highest month (January, $233.4M) and the
lowest (February, $156.6M), with no trend across the year.

June — the last month of the fiscal year — runs **7.7% below average**. Government budgeting
is known for a year-end spending surge, and this data shows the opposite. One plausible
reason sits in Finding 1: when most spending is airport construction, payments follow build
schedules rather than the budget calendar.

### 3. The city is 97.6% electronic; the paper that remains is legal settlements

Electronic payments carry $2,359.1M across 208,819 lines. Paper checks move under 1% of the
money — but average **$13,401 per line**, above electronic's $11,298.

Filtering to `payment_method = CHECK` explains the gap. 43% of check value carries the
invoice description `SETTLEMENT`, the largest recipients are law firms, and 83.7% sits in
`NON-DEPARTMENTAL` rather than any operating department. A further $3.55M went to the US
Treasury as a bond arbitrage rebate.

Checks are what the city uses for one-off payments to parties who were never set up as
regular vendors.

## Recommendation

- TODO — who should act, and on what?
- TODO — the `vendor_id` field is broken in a published dataset. Is that worth reporting to the city?
- TODO — does 52% concentration in 20 vendors warrant procurement review, or is it normal for a city running a major airport?
- TODO — pick one and argue it. A description without a recommendation is a report; with one, it's analysis.

## Limitations

- Single fiscal year — no year-over-year comparison, so no trend claims
- Descriptive only — identifies patterns, not causes
- Vendor consolidation not attempted beyond ID-level grouping; parent/subsidiary
  relationships are not visible in this data
- **Vendor identity is approximate.** The top two vendors, `HOLDER-AUSTIN-MOODY-BRYSON, A JV`
  ($177.9M) and `HOLDER-MOODY-BRYSON-SOVEREIGN A JOINT VENTURE` ($149.3M), share three named
  partners and together hold 13.5% of city spending. `NSMS`, `NSMS II` and `NSMS III` repeat
  the pattern. These are distinct legal entities, so no cleaning step merges them — real
  concentration is therefore **higher** than the headline figure reports
- **73.1% of rows have no invoice description**, so spending cannot be categorised by purpose
  at scale
- **60 rows have unrecoverable `payment_id` values**, making the 42,561 payment count a lower bound
- **190 rows carry no vendor name**
- TODO — anything else you hit

## What I'd do with more time

- TODO — what would you check next?
- TODO — what did you have to leave unanswered?
- TODO — what would a second fiscal year let you say that one cannot?

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
