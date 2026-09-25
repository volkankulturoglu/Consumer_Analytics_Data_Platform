# Consumer Analytics Data Platform

## Overview

This project is an end-to-end consumer analytics data platform built in Databricks using retail transaction, product, household demographic, campaign, and coupon data.

The project follows a **Medallion Architecture**, transforming raw CSV files through Bronze, Silver, and Gold layers. Raw data is ingested and stored as Delta tables, validated and standardized using Python with PySpark, and modeled into reusable analytical datasets using SQL.

The final Gold layer supports customer behavior analysis, campaign performance analysis, and comparisons between purchasing behavior during campaign periods and equally sized pre-campaign periods.

A Power BI reporting layer is planned as the final consumption layer of the project.

---

## Business Questions

The project is designed to explore questions such as:

- How do customers differ in spending, shopping frequency, basket value, and activity?
- How widely are marketing campaigns distributed across households?
- What proportion of campaign-assigned households redeem coupons?
- How does purchasing behavior differ between campaign periods and equally sized pre-campaign periods?
- Are changes in spending accompanied by changes in shopping frequency or average basket value?
- How can customer and campaign metrics be transformed into reusable datasets for downstream analytics and BI reporting?

The campaign-period analysis is descriptive. It identifies observed behavioral differences but does not assume that the campaigns caused those changes.

---

## Architecture

The project follows a layered data architecture:

```text
Raw CSV Files
     │
     ▼
┌─────────────────────┐
│    Bronze Layer     │
│ Raw Delta Tables    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│    Silver Layer     │
│ Cleaned & Validated │
│      Data           │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│     Gold Layer      │
│ Analytical Models   │
└──────────┬──────────┘
           │
           ▼
     Power BI
   (planned)
```

### Bronze Layer

The Bronze layer preserves the source datasets while converting the raw CSV files into managed Delta tables.

Additional ingestion metadata is captured to improve traceability:

- source file
- ingestion timestamp

The raw structure is intentionally preserved so that data-quality issues can be investigated without losing the original source representation.

### Silver Layer

The Silver layer contains cleaned and standardized datasets used by downstream analytical models.

Transformations include:

- removal of exact duplicate coupon records
- parsing transaction time into hour and minute fields
- standardization of selected text attributes
- preservation of category-specific transaction quantities rather than applying unsupported outlier removal
- processing metadata for traceability

Data-quality checks performed before and during transformation include:

- primary and composite key validation
- duplicate detection
- null profiling
- referential integrity checks
- transaction range analysis
- campaign date validation
- demographic coverage analysis

### Gold Layer

The Gold layer contains reusable business-oriented analytical datasets at clearly defined grains.

#### `customer_360`

**Grain:** One row per household.

Combines customer purchasing behavior, campaign participation, coupon redemption activity, and available demographic attributes.

Example metrics include:

- total spend
- total baskets
- average basket value
- distinct products
- active shopping days
- first and last purchase day
- campaigns assigned
- coupon redemption activity

#### `campaign_performance`

**Grain:** One row per campaign.

Provides campaign-level response and customer profile metrics, including:

- households assigned
- households redeemed
- redemption events
- distinct coupons redeemed
- redemption rate
- campaign duration
- purchasing profile of assigned households

#### `campaign_behavior_signals`

**Grain:** One row per campaign.

Compares purchasing behavior of campaign-assigned households during each campaign with an equally sized period immediately preceding the campaign.

Metrics include:

- spend before and during the campaign
- spend change %
- basket count before and during the campaign
- basket count change %
- average basket value before and during the campaign
- average basket value change %

These metrics represent observed behavioral changes and should not be interpreted as causal campaign effects.

#### `campaign_analytical_summary`

A Gold analytical view combining campaign response metrics with purchasing behavior signals for downstream reporting and exploratory analysis.

---

## Dataset

The project uses seven source datasets:

| Dataset | Purpose |
|---|---|
| Transactions | Household-level retail transaction activity |
| Products | Product hierarchy and product attributes |
| Demographics | Available household demographic attributes |
| Campaign Descriptions | Campaign definitions and active periods |
| Campaign Households | Household-to-campaign assignments |
| Coupons | Campaign coupon and product relationships |
| Coupon Redemptions | Household coupon redemption activity |

The transaction dataset contains approximately **2.6 million transaction lines** across **2,500 households**.

Demographic information is available for **801 households**, representing approximately **32% of households with transaction activity**. Demographic analyses therefore use a smaller population than behavioral analyses.

---

## Data Quality Findings

Several data-quality and analytical issues were identified during source profiling.

### Coupon Duplicates

The coupon dataset contained:

- 124,548 source rows
- 119,384 unique coupon-product-campaign combinations
- 5,164 redundant duplicate rows

The Bronze layer preserves the source data, while the duplicates are removed in the Silver layer.

### Referential Integrity

Key relationships between transaction, product, campaign, coupon, and redemption datasets were validated before analytical modeling.

No missing references were identified in the tested relationships.

### Transaction Quantity

Very large quantity values were investigated rather than automatically treated as outliers.

The largest quantities were associated with categories such as gasoline and miscellaneous transaction types, indicating that quantity does not represent the same physical unit across all product categories.

For this reason, customer analytics primarily uses measures such as spend, basket count, transaction lines, and distinct products instead of relying on raw quantity totals.

### Campaign Observation Coverage

Transaction history is available through **Day 711**, while one campaign extends through **Day 719**.

As a result:

- 29 campaigns have complete transaction coverage
- 1 campaign has an incomplete observation window

The final analytical view includes an `observation_status` field so that incomplete campaigns can be identified and excluded from direct pre-campaign vs. campaign-period comparisons when necessary.

---

## Analytical Approach

Campaign behavior is evaluated using equal observation windows.

For each campaign:

```text
BEFORE
Equal-length period immediately before the campaign

              ↓

CAMPAIGN START ───────────── CAMPAIGN END

              DURING
```

For example, if a campaign lasts 38 days, purchasing activity during the campaign is compared with the 38 days immediately preceding it.

This improves comparability between the two periods, but it does **not** establish causal campaign impact.

Factors such as seasonality, pricing, product availability, concurrent promotions, targeting strategy, and other external influences are not controlled for in the available data.

A control-group or causal-inference approach would be required to estimate incremental campaign impact more reliably.

---

## Technologies

- **Databricks**
- **Python / PySpark**
- **Apache Spark**
- **SQL**
- **Delta Lake**
- **Unity Catalog**
- **Git / GitHub**
- **Power BI** — planned reporting layer

---

## Repository Structure

```text
Consumer_Analytics_Data_Platform/
│
├── notebooks/
│   ├── 01_Source_Profiling_and_Data_Quality
│   ├── 02_Bronze_Layer_Ingestion
│   ├── 03_Silver_Transformations
│   ├── 04_Customer_360
│   ├── 05_Campaign_Analysis
│   └── 06_Growth_Opportunities
│
├── powerbi/
│   └── planned
│
└── README.md
```

---

## Current Status

The Databricks data pipeline and Gold analytical layer are complete.

Current development is maintained on:

`feature/consumer-analytics-pipeline`

Next steps:

- build the Power BI reporting layer
- add dashboard screenshots
- finalize project documentation
- perform final validation
- merge the completed project into `main`