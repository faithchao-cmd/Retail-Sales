# Sales Tracker in Google Sheets

A lightweight sales tracker that auto-calculates **daily, weekly, and monthly totals** from raw sales entries — built to practice structuring a spreadsheet for ongoing data entry and automatic aggregation.

## Overview

Retail and operations teams need a simple place to log transactions that rolls up into reporting numbers without manual recalculation. This project separates **data entry** from **reporting logic** so the two never interfere with each other.

## Structure

| Tab | Purpose |
|---|---|
| `Raw Entries` | One row per transaction. The only tab anyone types into. Locked down with data validation. |
| `Summary` | Daily, weekly, and monthly totals. Every cell is a formula — nothing is hardcoded. |
| `SUMIFS Practice` | Worked examples building up from a single condition to a full date-range rollup. |

**Raw Entries columns:** Transaction ID, Date, Category, Product, Quantity, Unit Price, Total Sale (formula), Store Location, Payment Method, Salesperson.

## Why separate raw data from formulas?

- Staff entering sales only ever touch `Raw Entries` — a simple, validation-locked grid. They can't see or accidentally break a formula.
- `Summary` formulas reference whole columns (`'Raw Entries'!G:G`) safely, since new rows of raw data never land on top of a formula cell.

## Aggregation logic

Every total uses `SUMIFS(sum_range, criteria_range1, criteria1, ...)`. A row counts only if **every** criteria pair matches.

```
Daily:   =SUMIFS('Raw Entries'!$G:$G, 'Raw Entries'!$B:$B, A6)
Weekly:  =SUMIFS('Raw Entries'!$G:$G, 'Raw Entries'!$B:$B, ">="&A84, 'Raw Entries'!$B:$B, "<="&B84)
Monthly: =SUMIFS('Raw Entries'!$G:$G, 'Raw Entries'!$B:$B, ">="&B120, 'Raw Entries'!$B:$B, "<="&D120)
```

Each `Summary` row points at its own date cell(s) rather than hardcoding a date into the formula — one formula shape, copied down every row.

## Data validation

| Column | Rule | Prevents |
|---|---|---|
| Category | Dropdown (9 fixed categories) | Typos that fragment category rollups |
| Date | Date, between `2026-01-01` and `2026-12-31` | Wrong-year typos, stray future dates |
| Quantity | Whole number, 1–20 | Text, decimals, negative/unrealistic quantities |
| Unit Price | Decimal, > 0 | Zero or negative prices |
| Store / Payment | Dropdown list | Free-typed values that break reporting groupings |


## Verification

Five `SUMIFS` results were independently recomputed from the raw data and matched exactly:

| Check | Result |
|---|---|
| Single day (Jun 1, 2026) | $271.53 |
| Category total (Electronics) | $4,048.39 |
| Weekly total (Jun 1–7) | $2,363.49 |
| Weekly + store filter (Downtown) | $501.68 |
| Monthly total (June) | $11,276.42 |

## Sample dataset

`retail_sales_dataset.xlsx` — 590 unique transactions across 75 days (Jun 1–Aug 14, 2026), 24 products across 9 categories, 5 store locations.
