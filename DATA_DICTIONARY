# Data Dictionary

## Dataset Overview

| Property | Value |
|---|---|
| Source | Synthetic - generated to reflect real SA retail data quality issues |
| Period | FY 2024 (1 January 2024 to 31 December 2024) |
| Raw rows | 1,050 (including 50 duplicates) |
| Clean rows | 1,000 |
| Currency | South African Rand (ZAR) |
| Encoding | UTF-8 |
| Delimiter | Comma (CSV) |

---

## Field Definitions

### Transaction_ID
- **Type:** String
- **Format:** TXN-XXXXX (e.g. TXN-00001)
- **Description:** Unique identifier for each transaction
- **Issues injected:** 50 duplicate Transaction_IDs
- **Cleaning action:** Retain first occurrence, remove all duplicates

---

### Date
- **Type:** Date
- **Expected format:** YYYY-MM-DD (ISO 8601)
- **Valid range:** 2024-01-01 to 2024-12-31
- **Issues injected:** 60 rows in non-standard formats including DD/MM/YYYY, MM-DD-YYYY, DD-Mon-YYYY, YYYY/MM/DD
- **Cleaning action:** Parse using multi-format parser, convert to ISO 8601

---

### Customer_ID
- **Type:** String
- **Format:** CUST-XXXX (e.g. CUST-1234)
- **Issues injected:** 20 blank/null values
- **Cleaning action:** Fill with UNKNOWN, flag for CRM reconciliation

---

### Product_SKU
- **Type:** String
- **Valid values:** SKU-001 through SKU-010

**SKU Reference Table:**

| SKU | Product Name | Category | Unit Price (ZAR) |
|---|---|---|---|
| SKU-001 | Wireless Earbuds | Electronics | R1,299.00 |
| SKU-002 | Running Shoes | Footwear | R1,799.00 |
| SKU-003 | Yoga Mat | Sports | R649.00 |
| SKU-004 | Coffee Maker | Kitchen | R2,999.00 |
| SKU-005 | Backpack | Accessories | R1,099.00 |
| SKU-006 | Sunglasses | Accessories | R799.00 |
| SKU-007 | Smart Watch | Electronics | R4,499.00 |
| SKU-008 | Protein Powder | Health | R599.00 |
| SKU-009 | Water Bottle | Sports | R349.00 |
| SKU-010 | Laptop Stand | Electronics | R749.00 |

---

### Region
- **Type:** String
- **Valid values:** North, South, East, West, Online
- **Issues injected:** 79 rows with 22 variant names (e.g. north, NORTH, Nrth, N.)
- **Also:** 20 blank/null values
- **Cleaning action:** Map variants to canonical form, fill blanks with UNASSIGNED

---

### Channel
- **Type:** String
- **Valid values:** Website, Mobile App, In-Store, Marketplace
- **Issues injected:** None

---

### Payment_Method
- **Type:** String
- **Valid values:** Credit Card, PayPal, Debit Card, Bank Transfer
- **Issues injected:** 20 blank/null values
- **Cleaning action:** Fill with UNKNOWN, flag for finance team review

---

### Quantity
- **Type:** Integer
- **Valid range:** 1 to 5
- **Issues injected:** None

---

### Unit_Price
- **Type:** Float (ZAR)
- **Valid range:** R349.00 to R4,499.00
- **Issues injected:** 40 rows stored as strings with R symbol (e.g. R1299.0)
- **Cleaning action:** Strip R symbol, cast to float

---

### Discount_Rate
- **Type:** Float
- **Valid range:** 0.0 to 0.20 (0% to 20%)
- **Issues injected:** 9 rows entered as whole percentage (e.g. 10 instead of 0.10), 20 blank values
- **Cleaning action:** Fill blanks with 0.0, divide values greater than 1 by 100

---

### Revenue
- **Type:** Float (ZAR)
- **Expected formula:** Unit_Price x Quantity x (1 - Discount_Rate)
- **Valid range:** R0.00 to R23,619.75
- **Maximum legitimate transaction:** 5 units x R4,499.00 x 1.05 buffer = R23,619.75
- **Issues injected:** 15 negative values, 7 inflated values (10x or 100x), 10 zero values with Quantity greater than 0
- **Cleaning action:** Replace all anomalous values with recalculated expected revenue

---

## Null Value Handling Summary

| Field | Value Used | Rationale |
|---|---|---|
| Customer_ID | UNKNOWN | Cannot infer, flag for follow-up |
| Region | UNASSIGNED | Cannot infer safely |
| Discount_Rate | 0.0 | Conservative, assume no discount |
| Payment_Method | UNKNOWN | Flag for finance review |
| Date | NaN | Unparseable dates cannot be assumed |
| Unit_Price | NaN | Cannot calculate revenue without price |

---

## Business Rules Summary

| Rule | Value |
|---|---|
| Maximum units per transaction | 5 |
| Maximum unit price | R4,499.00 (Smart Watch) |
| Maximum legitimate transaction | R23,619.75 |
| Maximum discount rate | 20% (0.20) |
| Standard date format | YYYY-MM-DD (ISO 8601) |
| Currency | ZAR (South African Rand) |

---

See METHODOLOGY.md for the step-by-step cleaning decisions.
