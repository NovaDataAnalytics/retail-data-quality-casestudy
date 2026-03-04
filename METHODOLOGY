# Methodology — Retail Data Quality Case Study

This document explains every cleaning decision made in the analysis notebook,
the business logic behind each validation rule, and how issue counts are
reconciled across steps.

---

## Cleaning Principles

Data cleaning follows three core principles in this project:

**1. Sequence matters.** Structural issues are addressed before value issues,
and value issues before outliers. Cleaning out of order produces misleading
audit counts and can cause earlier steps to interact with later ones in
unexpected ways.

**2. Every decision is audited.** Each step records exactly how many records
were affected, what changed, and why. This allows any reviewer to reproduce
or challenge the methodology.

**3. Never invent data.** Where a value cannot be determined with confidence,
it is flagged (UNKNOWN, UNASSIGNED) rather than guessed. The exception is
Discount_Rate, where a missing value is conservatively assumed to be 0%
(no discount) — the safest assumption for revenue integrity.

---

## Pre-Cleaning Audit

Before any cleaning, the full 1,050-row dataset is audited to establish
baseline counts. These counts are captured on the raw data so they represent
the true scope of issues before any removal or correction occurs.

This is important because Step 1 (duplicate removal) changes the row count,
which would alter all subsequent counts if the audit were run after that step.

---

## Step 1 — Remove Duplicate Transactions

**What:** Rows where the Transaction_ID appears more than once.

**How:** `drop_duplicates(subset='Transaction_ID', keep='first')` — the first
occurrence is retained, all subsequent occurrences are removed.

**Why keep='first':** In the absence of a timestamp or version field, the
first-seen record is assumed to be the original entry. This is a conservative
assumption documented explicitly.

**Audit trail note:** Before removing duplicates, the notebook flags all rows
with date issues and missing values. This allows the audit log to show exactly
how many of each issue type were removed as a side effect of deduplication —
explaining why subsequent step counts are lower than the initial audit totals.

- Date issues in raw data: 60 rows
- Date issues removed with duplicates: 2 rows
- Date issues remaining for Step 4: 58 rows

- Missing cells in raw data: 80 cells
- Missing cells removed with duplicates: 6 cells
- Missing cells remaining for Step 2: 74 cells

---

## Step 2 — Handle Missing Values

**Scope:** Customer_ID, Region, Discount_Rate, Payment_Method

**Decisions by field:**

| Field | Action | Rationale |
|---|---|---|
| Customer_ID | Fill with UNKNOWN | Cannot infer — flag for CRM reconciliation |
| Region | Fill with UNASSIGNED | Cannot infer safely — flag for sales team review |
| Discount_Rate | Fill with 0 | Conservative assumption: no discount applied |
| Payment_Method | Fill with UNKNOWN | Flag for finance team review |

**Why not drop rows with missing values?**
Dropping rows removes real transactions from the revenue total. Flagging
preserves the transaction while making the gap visible for follow-up.

---

## Step 3 — Standardise Region Names

**What:** Region field contained 22 distinct variants of 5 canonical values.

Examples: North / NORTH / Nrth / N. / north all refer to the same region.

**How:** A lookup dictionary maps every known variant (case-insensitive,
stripped of whitespace) to its canonical form. Unknown values are left
unchanged so they remain visible rather than being silently misclassified.

**Why this matters:** Without standardisation, a GROUP BY Region query
produces 22 groups instead of 5. Revenue totals per region are fractured
and meaningless.

---

## Step 4 — Standardise Date Formats

**What:** 58 rows (post-deduplication) with dates in non-standard formats.

Formats found: `YYYY-MM-DD` (standard), `DD/MM/YYYY`, `MM-DD-YYYY`,
`DD-Mon-YYYY`, `YYYY/MM/DD`

**How:** A custom parse function attempts each format in sequence and
returns the date in ISO 8601 (YYYY-MM-DD) format. Dates that cannot be
parsed by any known format are set to NaN and logged.

**Format priority order:** ISO 8601 first (already correct), then formats
ordered by frequency in the dataset.

**Why ISO 8601:** It is the unambiguous international standard, sorts
correctly as a string, and is natively parsed by all analytics tools.

---

## Step 5 — Clean Unit Price Field

**What:** 38 rows where Unit_Price was stored as a string with a currency
symbol (e.g. "R1299.00" instead of 1299.00).

**How:** Strip the R character, strip whitespace, cast to float using
`pd.to_numeric(..., errors='coerce')`. Any value that fails conversion
after stripping is set to NaN.

**Why this matters:** A column with mixed numeric and string values will
be treated as object type by pandas and any aggregation or multiplication
will fail silently or raise an error in downstream tools.

---

## Step 6 — Fix Discount Rate Formatting

**What:** 9 rows where Discount_Rate was entered as a whole percentage
(e.g. 10, 15, 20) instead of a decimal (0.10, 0.15, 0.20).

**How:** Any Discount_Rate value greater than 1 is divided by 100.

**Business rule:** A valid discount rate must be between 0 and 1 inclusive.
The maximum discount in this dataset's product catalogue is 20% (0.20).
Any value above 1 is unambiguously a percentage-format error.

**Why this matters:** A discount rate of 10 applied to a R1,299 transaction
produces a revenue of R1,299 * (1 - 10) = negative R11,691. The error
directly destroys revenue calculations.

---

## Step 7 — Correct Revenue Outliers

Three sub-issues are addressed in this step:

### 7a — Negative Revenues
**What:** 15 rows with negative Revenue values.

**Interpretation:** Likely represent returns or refunds that were not
separately coded. They are not legitimate sales revenue.

**Action:** Replace with the recalculated expected revenue
(Unit_Price * Quantity * (1 - Discount_Rate)). In a production
environment, these rows should be reviewed to determine whether they
represent returns that belong in a separate returns dataset.

### 7b — Inflated Amounts
**What:** 7 rows where Revenue exceeded the maximum possible legitimate
transaction value.

**Business rule:**
```
MAX_QTY = 5                      # Maximum units per transaction
MAX_UNIT_PRICE = R4,499.00       # Most expensive SKU (Smart Watch)
BUFFER = 1.05                    # 5% tolerance for rounding/edge cases
MAX_LEGITIMATE_REVENUE = R23,619.75
```

Any Revenue value above R23,619.75 is mathematically impossible given
the product catalogue and transaction rules, and is therefore a data
entry error.

**Why a fixed ceiling rather than a multiplier?**
A multiplier (e.g. >5x expected) compares each row against its own
expected value — which is itself derived from potentially dirty inputs.
The fixed ceiling uses externally defined business rules and does not
move regardless of what other errors exist in the row.

**Action:** Replace with recalculated expected revenue.

### 7c — Zero Revenues
**What:** 10 rows where Revenue was 0 but Quantity was greater than 0.

**Interpretation:** A transaction was recorded but the revenue calculation
was not completed or was overwritten with 0.

**Action:** Replace with recalculated expected revenue.

---

## Audit Reconciliation

The notebook produces a reconciliation table that explains every count
discrepancy between the initial audit (run on 1,050 rows) and the
cleaning steps (run on 1,000 rows after deduplication).

| Issue | Raw Count (1,050) | Removed in Step 1 | Addressed in Step | Remaining |
|---|---|---|---|---|
| Duplicates | 50 | 50 | Step 1 | 0 |
| Missing values | 80 cells | 6 cells | Step 2 | 0 |
| Date issues | 60 rows | 2 rows | Step 4 | 0 |
| Region variants | 79 rows | 6 rows | Step 3 | 0 |
| Price strings | 40 rows | 2 rows | Step 5 | 0 |
| Discount errors | 9 rows | 0 rows | Step 6 | 0 |
| Revenue issues | 32 rows | 0 rows | Step 7 | 0 |

---

## What This Methodology Does Not Cover

This analysis addresses the most common structural and value-level data
quality issues. It does not address:

- **Referential integrity** — verifying that Customer_IDs and Product_SKUs
  exist in a master reference table
- **Business rule validation** — confirming that product-region combinations
  are valid for this retailer
- **Temporal consistency** — checking that transaction dates are within
  the expected trading period
- **Statistical outlier detection** — identifying anomalous but technically
  valid transactions using IQR or z-score methods

These would be appropriate next steps in a production data pipeline.

---

*For field definitions and expected formats, see DATA_DICTIONARY.md.*
