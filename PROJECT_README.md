# Order Cancellation Driver Analysis

**A Business Analyst case study identifying and validating the key drivers of order cancellations in an e-commerce marketplace.**

---

## Overview

This project investigates why orders get cancelled or go unfulfilled in a multi-seller e-commerce marketplace. Rather than treating the aggregate cancellation rate (1.24%) as negligible, the analysis segments the data to test whether the problem is concentrated in a specific, actionable part of the business — and finds that it is.

**Headline finding:** Seller tenure is a statistically validated driver of cancellation risk. Sellers active under 30 days cancel at **1.08%**, more than double the **0.40%** rate for sellers active 90+ days — a gap confirmed via chi-square testing (**p < 0.001**) to be real, not random variation. This pattern holds even within individual product categories, ruling out confounding.

**Dataset:** [Olist Brazilian E-Commerce](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (public, via Kaggle), used as a proxy for a multi-seller marketplace. It was chosen because its structure — orders, sellers, products, order items — closely mirrors a seller-marketplace business model.

**Tech stack:** SQLite (SQL analysis) · Python (`scipy` for statistical testing) · Excel (`openpyxl`, dashboarding) · PDF reporting (`reportlab`)

---

## Project Structure

```
.
├── data/                                  # Raw Olist CSVs (not included — download separately, see Setup)
├── olist.db                               # SQLite database built from the raw CSVs
├── load_data.py                           # Loads CSVs into SQLite
├── run_queries.py                         # Runs all 5 segmentation SQL queries, exports CSVs
├── cancellation_driver_analysis.sql       # Standalone copy of all 5 queries with comments
├── stats_validation.py                    # Chi-square test on seller tenure vs. cancellation
├── segment_category.csv                   # Query 2 output
├── segment_tenure.csv                     # Query 3 output
├── segment_delivery.csv                   # Query 4 output
├── priority_table.csv                     # Query 5 output
├── Order_Cancellation_Dashboard.xlsx       # Excel dashboard (KPIs + 3 charts across 5 tabs)
├── Case_Memo_Full.pdf                     # 2-page standalone memo (problem → findings → recommendation)
└── Order_Cancellation_Analysis_Report/    # Full multi-document report (see its own README below)
    ├── 00_Cover_and_Executive_Summary.pdf
    ├── 01_Problem_Statement.pdf
    ├── 02_Hypotheses_and_Examples.pdf
    ├── 03_SQL_Methodology_and_Queries.pdf
    ├── 04_Statistical_Validation.pdf
    ├── 05_Findings_and_Priority_Segments.pdf
    ├── 06_Recommendations.pdf
    └── Order_Cancellation_Dashboard.xlsx
```

---

## Setup / How to Reproduce

1. **Download the dataset:** Get the Olist Brazilian E-Commerce dataset from Kaggle and unzip the CSVs into a `data/` folder in the project root.

2. **Load the data into SQLite:**
   ```bash
   python3 load_data.py
   ```
   This creates `olist.db`, loading the `orders`, `order_items`, `order_reviews`, `sellers`, and `products` tables.

3. **Run the segmentation queries:**
   ```bash
   python3 run_queries.py
   ```
   This runs all 5 SQL queries against `olist.db` and exports each result to its own CSV (`segment_category.csv`, `segment_tenure.csv`, `segment_delivery.csv`, `priority_table.csv`).

4. **Run the statistical validation:**
   ```bash
   python3 stats_validation.py
   ```
   This runs a chi-square test of independence on the seller-tenure segmentation and prints the chi-square statistic and p-value.

5. **Review the outputs:**
   - Open `Order_Cancellation_Dashboard.xlsx` for the visual dashboard.
   - Open `Case_Memo_Full.pdf` for the 2-page summary memo.
   - Open the `Order_Cancellation_Analysis_Report/` folder for the full, section-by-section report.

**Requirements:** Python 3, `pandas`, `scipy`, `openpyxl` (`pip install pandas scipy openpyxl --break-system-packages` if needed).

---

## Methodology Summary

1. **Defined the problem:** an order is treated as a "problem order" if `order_status IN ('canceled', 'unavailable')`.
2. **Formed 3 hypotheses** for what might drive cancellations: product category, seller tenure, and delivery delay.
3. **Tested each hypothesis with SQL**, segmenting orders along each dimension and computing cancellation rate per segment.
4. **Validated the strongest finding statistically** using a chi-square test, to confirm the seller-tenure pattern wasn't due to chance.
5. **Prioritized action** using a combined category × seller-tenure view, ranked by an impact score (order volume × cancellation rate).
6. **Converted findings into a recommendation** with a specific intervention, a monitoring metric, and success criteria.

---

## Key Numbers

| Metric | Value |
|---|---|
| Total orders analyzed | 99,441 |
| Overall cancellation / unavailable rate | 1.24% |
| New-seller (0–30 days) cancellation rate | 1.08% |
| Established-seller (90+ days) cancellation rate | 0.40% |
| Chi-square statistic (tenure vs. cancellation) | 102.83 |
| Statistical significance | p < 0.001 |
| Highest-impact segment | Automotive category, new sellers (2.51% rate) |

---

## Documents Included

| Document | Purpose |
|---|---|
| `Case_Memo_Full.pdf` | Single 2-page memo covering the entire analysis — best for a quick read |
| `Order_Cancellation_Analysis_Report/` | Full 7-part report — best for a deep-dive or interview walkthrough of methodology |
| `Order_Cancellation_Dashboard.xlsx` | Interactive-style dashboard with charts, for a visual, stakeholder-facing view |
| `cancellation_driver_analysis.sql` | All SQL logic in one place, for technical review |

---

## Notes & Limitations

- Olist (Brazil) is a public dataset used as a structural proxy for a seller marketplace; findings and figures are specific to this dataset and are not claimed to represent any real company's actual metrics.
- The delivery-delay hypothesis (H3) was tested and ruled out due to a structural data limitation (cancelled orders rarely reach a delivery date), not disproven — this distinction is noted explicitly in the report rather than glossed over.
- Impact-sizing figures in the Recommendations section are illustrative estimates based on assumed proportions, not company-specific data.

---

## Author's Note

This project was built as a Business Analyst case study to demonstrate an end-to-end analytical workflow: framing a business problem, forming and testing hypotheses, validating findings statistically, and translating results into a prioritized, actionable recommendation — the same loop used in real analytics/BA roles.
