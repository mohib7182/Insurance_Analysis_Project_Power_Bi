# Insurance Policy & Claims Analysis

Data cleaning and dashboard build on a raw insurance operations export, using
Power Query and Power BI Desktop.

**Note:** the dataset appears to be synthetic/sample data rather than a real
insurer's book of business — the loss ratio in the key result below is far
higher than would be plausible in practice, which is itself a useful thing to
flag when reviewing any dataset before trusting its conclusions.

---

## Project Overview

The project takes a raw insurance operations export (policies, customers, and
claims combined in a single file) and turns it into a clean, analysis-ready
model in Power BI. The work covers two phases:

1. Importing and cleaning the data in Power Query
2. Building summary KPIs and visuals in Power BI Desktop to understand how
   claims activity compares to premium income

## Data Source

**Source file:** `InsuranceData(1).csv` — a single flat file containing one
row per policy, with an associated claim (where one exists). As loaded, the
file contains 10,004 rows across 13 columns.

| Column | Description |
|---|---|
| `PolicyNumber` | Unique identifier for the policy (e.g. P1, P2…) |
| `CustomerID` | Unique identifier for the customer (e.g. C1, C2…) |
| `Gender` | Policyholder gender |
| `Age` | Policyholder age |
| `PolicyType` | Auto, Travel, Health, Home, or Life |
| `PolicyStartDate` / `PolicyEndDate` | Policy coverage window |
| `PremiumAmount` | Premium charged for the policy |
| `CoverageAmount` | Insured coverage value |
| `ClaimNumber` | Identifier for a claim linked to the policy |
| `ClaimDate` | Date the claim was filed (blank if no claim) |
| `ClaimAmount` | Amount claimed |
| `ClaimStatus` | Settled, Pending, or Rejected |

## Data Cleaning in Power Query

All cleaning steps were performed in the Power Query Editor before the data
was loaded into the report model.

1. **Connect to the source file** — `Get Data → Text/CSV`, then Transform
   Data to clean before loading.
2. **Set column profiling to the full dataset** — the default profiles only
   the first 1,000 rows; switching to the entire dataset is what first
   revealed a jump from ~999 to ~10,000 distinct values.
3. **Promote headers and set data types** — text/ID columns to Text, `Age`
   to whole number, monetary columns to decimal number.
4. **Remove duplicate rows** — `Remove Duplicates` on `PolicyNumber`;
   `PolicyNumber` and `CustomerID` both confirmed at 10,000 distinct /
   10,000 unique afterward.
5. **Fix date columns with Change Type Using Locale** — a plain Date type
   produced conversion errors on ~60% of rows in `PolicyEndDate`. Cause: the
   source dates are DD-MM-YYYY, which Power Query's default locale read as
   month-first. Fixed via `Transform → Data Type → Using Locale` → Date,
   English (United Kingdom), applied to `PolicyStartDate`, `PolicyEndDate`,
   and `ClaimDate`.
6. **Verify the cleaned table** — 0% errors across all key columns, 13
   columns, ~10,000 rows.

### Summary of cleaning actions
- Loaded via `Get Data → Text/CSV → Transform Data`
- Set column profiling to the entire dataset
- Promoted headers, set data types per column
- Removed duplicate policy records on `PolicyNumber`
- Corrected date parsing with Change Type + Locale (English – UK)
- Confirmed a clean, error-free table before loading into the report

## Power BI Report

**KPI cards:**
- Sum of Premium Amount — 5.97M
- Sum of Claim Amount — 16.90M
- Claims to Premium Ratio — 2.83 (quick measure: Sum of ClaimAmount ÷ Sum of
  PremiumAmount)
- Total Number of Policy Number — 10K
- Rejection Rate — 43.54% (claims with `ClaimStatus = Rejected` as a share
  of total claims)

**Supporting visuals:**
- Horizontal bar chart, premium vs. claim amount by `PolicyType` — claims
  exceed premiums in every policy line
- Average of `PremiumAmount` by `PolicyType`
- Count of claims by `PolicyType` / claim status
- Donut chart of claim status mix (Rejected / Settled / Pending) with policy
  count in the center
- Count of `Gender` — even 50/50 split

**Formatting pass:** titled/iconed KPI cards in a single row, purple page
background with white visual containers, per-category colors with data
labels on the bar charts, and the finished donut breakdowns described above.

## Key Result

Claims outpace premiums across every policy line — total claims (≈16.9M)
run well above total premium collected (≈5.97M), a ratio of roughly 2.8x,
with a rejection rate of about 43.5% on filed claims.

## Files & Artifacts

- `InsuranceData(1).csv` — raw source data (10,004 rows, 13 columns)
- Power Query cleaning steps — Promoted Headers → Changed Type → Removed
  Duplicates → Changed Type with Locale (dates)
- Power BI report — KPI cards, premium-vs-claims bar chart, average premium
  by policy type, claim count by claim status, claim status mix, and gender
  split

## Tools

Power Query · Power BI Desktop
