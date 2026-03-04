# Retail Data Quality Case Study
### How Dirty Data Cost a Retailer R2,985,788 in Phantom Revenue

![Python](https://img.shields.io/badge/Python-3.8+-blue?style=flat-square&logo=python)
![pandas](https://img.shields.io/badge/pandas-1.5+-green?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square)

---

## Overview

This project demonstrates how common data quality issues in retail transaction data
lead to catastrophically incorrect revenue reporting — and how systematic data cleaning
resolves them.

Using a realistic South African retail dataset of 1,050 transactions across 10 product
SKUs and 5 regions, we show that uncleaned data reported **R7,129,011** in revenue for
FY 2024. The true figure after cleaning was **R4,143,223** — a **R2,985,788 overstatement
(72.1% error)**.

This is a demonstration project built to illustrate real data quality problems encountered
across South African SMEs, NGOs, and public sector organisations.

---

## The Problem

Most businesses export transaction data and aggregate it directly — without validating
or cleaning it first. This analysis shows exactly what that costs.

| Metric | Dirty Data | Clean Data |
|---|---|---|
| Total Rows | 1,050 | 1,000 |
| Reported Revenue | R7,129,011 | R4,143,223 |
| Overstatement | R2,985,788 | — |
| Error Rate | 72.1% | 0% |

---

## Data Quality Issues Covered

| Issue | Rows Affected | Business Impact |
|---|---|---|
| Duplicate transactions | 50 rows | Direct revenue double-counting |
| Missing values | 80 cells | Breaks segmentation and reporting |
| Inconsistent region formatting | 79 rows / 22 variants | Breaks all regional aggregation |
| Inconsistent date formats | 60 rows / 5 formats | Breaks time-series analysis |
| Prices stored as strings with R symbol | 40 rows | Silent calculation failures |
| Discount rates entered as % not decimal | 9 rows | Inflates/deflates revenue |
| Negative revenues (unreconciled returns) | 15 rows | Distorts revenue totals |
| Inflated amounts (data entry errors) | 7 rows | Massive revenue overstatement |
| Zero revenues (missing calculations) | 10 rows | Understates true revenue |

---

## Repository Structure

```
retail-data-quality-casestudy/
│
├── README.md                        <- You are here
├── METHODOLOGY.md                   <- Full explanation of each cleaning step
├── DATA_DICTIONARY.md               <- Field definitions and business rules
├── CHANGELOG.md                     <- Version history
├── LICENSE                          <- MIT License
├── requirements.txt                 <- Python dependencies
│
├── data/
│   ├── dirty_data_zar.csv           <- Raw dataset with injected issues (1,050 rows)
│   └── retail_cleaned_data.csv      <- Cleaned output after all 7 steps (1,000 rows)
│
├── notebooks/
│   └── retail_data_quality_casestudy.ipynb   <- Main analysis notebook
│
└── charts/
    ├── chart1_revenue_comparison.png
    ├── chart2_issue_breakdown.png
    ├── chart3_region_comparison.png
    └── chart4_monthly_trend.png
```

---

## Getting Started

### Prerequisites

- Python 3.8 or higher
- pip

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/NovaDataAnalytics/retail-data-quality-casestudy.git
cd retail-data-quality-casestudy

# 2. Install dependencies
pip install -r requirements.txt

# 3. Launch the notebook
jupyter notebook notebooks/retail_data_quality_casestudy.ipynb
```

### Running the Analysis

Open the notebook and run all cells in order (Cell > Run All).

The notebook will:
1. Load `data/dirty_data_zar.csv`
2. Audit all data quality issues with exact counts per column
3. Apply 7 cleaning steps in deliberate sequence with a full audit trail
4. Reconcile issue counts across steps (e.g. duplicates carrying date/missing issues)
5. Calculate revenue impact dynamically per issue category
6. Generate and export 4 charts to the `charts/` folder
7. Save the cleaned dataset to `data/retail_cleaned_data.csv`

---

## Key Findings

**Inflated amounts** (data entry errors producing 10x or 100x values) were the single
biggest issue — 7 rows accounting for the majority of the R2.9M overstatement.

**Duplicate transactions** (50 rows) added R447,705 in phantom revenue through
straightforward double-counting.

**Inconsistent formatting** across regions, dates, and price fields made segmentation
and trend analysis completely unreliable even where revenue totals appeared correct.

**The audit trail** — tracking exactly which issues were resolved at each step and why
counts change between steps — is documented throughout the notebook and explained in
METHODOLOGY.md.

---

## Business Relevance

This scenario applies directly to:

- **SMEs** using Excel or basic POS systems to track sales
- **NGOs** aggregating field data from multiple collectors
- **Public sector** departments consolidating records across systems
- **Researchers** working with survey or administrative datasets

The same four issue categories appear across industries. Only the specific field names
and business rules change.

---

## Further Reading

- [METHODOLOGY.md](METHODOLOGY.md) — step-by-step explanation of every cleaning decision
- [DATA_DICTIONARY.md](DATA_DICTIONARY.md) — field definitions, formats, and validation rules
- [Full blog article](https://yourwebsite.co.za/blog/dirty-data-case-study) — non-technical
  narrative version for business audiences

---

## About

Produced by **Nova Data Analytics** — a South African data analytics company helping
SMEs, NGOs, researchers, and public sector organisations make better decisions through
cleaner, more reliable data.

- **Website:** [novadataanalytics.co.za/](https://www.novadataanalytics.co.za/)
- **Email:** info@novadataanalytics.co.za
- **LinkedIn:** [linkedin.com/company/novadataanalytics-coza](https://www.linkedin.com/company/novadataanalytics-coza)

---

## License

MIT License — see [LICENSE](LICENSE) file for details.
The dataset is entirely synthetic and does not represent any real business or individual.
