# Online Retail Sales Analysis: Discovering Customer Behavior, Product Trends, and Revenue Insights

A complete Exploratory Data Analysis (EDA) project built for the **CodeAlpha Data Analytics Internship**,
analyzing one year of real transaction data from a UK-based online gift retailer.

---

## Overview

This project takes a raw, messy transaction export (541,909 rows) and turns it into a documented,
reproducible analysis: every cleaning decision is explained and tracked with before/after row counts,
every chart answers a specific business question, and two hypotheses are formally tested with statistics
rather than assumed from a chart.

The full workflow — data quality checks, cleaning, univariate/bivariate/multivariate EDA, trend analysis,
anomaly detection, hypothesis testing, and business recommendations — lives in a single Jupyter Notebook:
[`Online_Retail_EDA.ipynb`](./Online_Retail_EDA.ipynb).

## Business Problem

The retailer wanted to understand recorded sales performance, product and customer behavior, and market
patterns to support decisions on inventory planning, market prioritization, and where to investigate
operational issues (e.g. cancellations). Since the dataset has **no product cost data**, every finding here
is about *recorded sales value*, never profit or margin.

## Dataset

- **Source:** [Online Retail Dataset — UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/352/online+retail)
  — see [`data/README.md`](./data/README.md) for dataset setup instructions.
- **Period:** 1 December 2010 – 9 December 2011 (373 days; first and last months are partial)
- **Size:** 541,909 transaction-line records, 8 columns
- **Columns:** `InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`,
  `CustomerID`, `Country`
- **Granularity note:** the raw file is at the *invoice-line* level (one row = one product on one
  invoice). The notebook is explicit throughout about whether a statistic is at the line, invoice, or
  customer level, since these give very different numbers.

## Tools & Technologies

Python · pandas · NumPy · Matplotlib · Seaborn · SciPy · Jupyter Notebook

## Key Business Questions

**Revenue:** total recorded sales & cancellation offset · top products/countries by revenue · monthly
sales trend · average order value
**Product:** top products by quantity vs. revenue · unusual pricing · cancellation rate by product
**Customer:** spend distribution & top 20 customers · countries by customer count vs. revenue
**Time:** monthly trend · day-of-week and hour-of-day invoice activity

*(12 questions total — see Section 3 of the notebook for the full list.)*

## Data Cleaning Summary

Every cleaning step is applied in order, with row counts tracked before and after each step. The complete audit trail is available in Section 8 of the notebook.

### 1. Exact Duplicate Rows
- **Count:** 5,268
- **Decision:** Removed duplicate rows while keeping the first occurrence.

### 2. Cancelled Invoices
- **Count:** 9,251 rows across 3,836 invoices
- **Decision:** Kept and flagged as `IsCancelled`.
- **Treatment:** Excluded only from sales totals because cancellations represent real business events.

### 3. Internal Stock Adjustments
- **Count:** 1,336
- **Decision:** Kept and flagged as `IsAdjustment`.
- **Treatment:** Excluded from sales analysis.

### 4. Accounting Entries
- **Count:** 2
- **Example:** "Adjust bad debt" entries
- **Decision:** Kept and flagged as `IsBadDebtAdj`.
- **Treatment:** Excluded from sales analysis.

### 5. Non-Product Stock Codes
- **Count:** 2,790
- **Examples:** `POST`, `DOT`, `M`, `D`, `S`, and `BANK CHARGES`
- **Decision:** Kept and flagged as `IsNonProduct`.
- **Treatment:** Excluded from product-level analysis.

### 6. Missing Customer IDs
- **Count:** Approximately 135,000 rows (24.9%)
- **Decision:** Kept for sales, product, country, and time-based analysis.
- **Treatment:** Excluded only from customer-level analysis.

### 7. Missing Product Descriptions
- **Count:** 1,454
- **Decision:** Replaced missing descriptions with a placeholder based on `StockCode`.
- **Treatment:** Rows were retained.

### Cleaned Datasets

Two working datasets are used throughout the notebook:

- **`df`** — Full cleaned dataset with all quality flags (536,641 rows). Used for invoice and return-rate analysis.
- **`analysis_df`** — Genuine completed merchandise sales only (522,685 rows). Used for revenue, product, customer, and time-based analysis.

## Main Findings

*(All figures below are computed live in the notebook — see the Key Findings section for the exact code.)*

- **Total recorded sales value:** £10,254,661.15 across 19,776 genuine invoices
- **Average invoice value:** £518.54 · **Median invoice value:** £302.33 — the gap confirms a right-skewed
  order-value distribution driven by a smaller number of large (likely wholesale) orders
- **Top country by revenue:** United Kingdom (£8,726,309.50, ~85% of recorded sales) — Netherlands, EIRE,
  Germany, and France follow
- **Top product by revenue:** REGENCY CAKESTAND 3 TIER (£174,156.54)
- **Invoice-level cancellation rate:** 14.81% (3,836 of 25,900 unique invoices)
- **Seasonality:** a genuine pre-holiday peak — November 2011 (£1,453,262.69) is the highest *full* month,
  well above every other month (December figures are partial-month and excluded from this comparison)

## Statistical Analysis Summary

### Hypothesis 1: UK vs. Non-UK Invoice Value

- **Test used:** Mann–Whitney U test
- **Reason:** Invoice values are right-skewed.
- **U statistic:** 12,982,792.5
- **p-value:** 1.06 × 10⁻⁵⁸
- **Result:** Reject H₀. There is a statistically significant difference between UK and non-UK invoice values.

### Hypothesis 2: Cancellation and Country Association

- **Test used:** Chi-square test of independence
- **Chi-square statistic:** 60.55
- **Degrees of freedom:** 5
- **p-value:** 9.33 × 10⁻¹²
- **Result:** Reject H₀. Cancellation status and country show a statistically significant association.

Both results are statistically significant at α = 0.05. However, statistical significance does not necessarily indicate business importance, and these tests do not establish causation.

## Visualizations

12 charts are generated and saved to [`visualizations/`](./visualizations/), each with a title, axis
labels, units, and a written interpretation directly below it in the notebook, including: univariate
distributions, top 10 products/countries by revenue, a quantity-vs-sales scatter plot, the monthly sales
trend, a correlation heatmap, day-of-week/hour-of-day activity, and the two hypothesis-test visualizations.

## Business Recommendations

1. **Inventory planning** — prioritize stock for the top-revenue products identified in the notebook.
2. **Market focus** — re-check whether marketing spend on top non-UK markets matches their actual (much
   smaller) share of recorded sales.
3. **Cancellation investigation** — since cancellation rate is significantly associated with country,
   investigate operational causes (shipping, fit, fraud) in the highest-cancellation markets before
   assuming customer dissatisfaction.
4. **Order-value strategy** — the mean/median gap suggests treating typical retail customers and large
   wholesale-style orders as separate segments.
5. **Data capture** — ~25% of transactions have no `CustomerID`; improving this would unlock proper
   customer-level analysis (loyalty, repeat-purchase rate).
6. **Seasonal planning** — use the confirmed November peak to plan inventory and staffing ahead of time.

## Limitations

- No product cost/margin data — all figures are *recorded sales value*, never profit.
- Single retailer, one year, ~85% UK-concentrated — findings may not generalize elsewhere.
- ~25% of rows have no `CustomerID`; customer-level findings reflect identified customers only.
- December 2010 and December 2011 are partial months and are excluded from month-over-month comparisons.
- Statistical significance does not imply causation anywhere in this notebook.
- `TotalSales` and `Quantity` are correlated partly by mathematical construction
  (`TotalSales = Quantity × UnitPrice`) — this is called out explicitly, not presented as an independent insight.

## How to Run This Project

1. Clone this repository.
2. Download the dataset (see [`data/README.md`](./data/README.md)) and place it at
   `data/Online_Retail.xlsx` (or `.csv`).
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Launch Jupyter and run all cells:
   ```bash
   jupyter notebook Online_Retail_EDA.ipynb
   ```
   The notebook is fully reproducible — every number and chart regenerates from the raw file, nothing
   is hardcoded.

## Project Folder Structure

```
CodeAlpha_EDA_Online_Retail/
├── Online_Retail_EDA.ipynb      # Main analysis notebook (all 19 sections)
├── README.md                    # This file
├── requirements.txt             # Python dependencies
├── data/
│   └── README.md                # Dataset download instructions (not redistributed here)
└── visualizations/               # Saved chart images (generated when the notebook runs)
```

## Author

**Nandini**

- GitHub: [Nandini04It](https://github.com/Nandini04It)
- LinkedIn: [Nandini .](https://www.linkedin.com/in/nandini-prajapati-it)
## License / Data Attribution

This project's code is shared for portfolio purposes. The underlying dataset belongs to its original
publisher (UCI Machine Learning Repository) — see `data/README.md` for the source link. The raw dataset
file is intentionally **not** included in this repository; download it separately using the instructions
provided.
