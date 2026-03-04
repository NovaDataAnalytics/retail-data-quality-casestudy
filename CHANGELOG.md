# Changelog

All notable changes to this project are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [1.0.0] - 2024

### Added
- Initial release of the retail data quality case study
- Synthetic dataset of 1,050 South African retail transactions (dirty_data_zar.csv)
- 7-step data cleaning notebook with full audit trail
- Dynamic revenue impact calculations (no hardcoded values)
- Maximum legitimate transaction threshold based on ZAR product catalogue
- Audit trail reconciliation tracking issues removed by deduplication
- 4 analysis charts: revenue comparison, issue breakdown, regional comparison, monthly trend
- README, METHODOLOGY, DATA_DICTIONARY, and CHANGELOG documentation
- MIT License

### Data Quality Issues Covered
- Duplicate transactions (50 rows)
- Missing values across 4 fields (80 cells)
- Inconsistent region name formatting (79 rows, 22 variants)
- Inconsistent date formats (60 rows, 5 formats)
- Prices stored as strings with R symbol (40 rows)
- Discount rates entered as percentages instead of decimals (9 rows)
- Negative revenue values (15 rows)
- Inflated amounts from data entry errors (7 rows)
- Zero revenues with positive quantity (10 rows)

---

## Planned

- [ ] Version with English and Afrikaans field labels
- [ ] Extended version covering NGO beneficiary data quality issues
- [ ] Extended version covering survey/research data quality issues
- [ ] Automated data validation pipeline using Great Expectations
- [ ] Power BI dashboard version of the analysis
