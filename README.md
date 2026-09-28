# Consumer Analytics Data Platform

End-to-end consumer analytics platform built with **Databricks, PySpark, SQL, Delta Lake, Unity Catalog, and Power BI**.

The project transforms **2.6M+ retail transaction records** through a **Bronze → Silver → Gold** architecture into Customer 360 and campaign analytics models used in an interactive Power BI dashboard.

---

## 📊 Dashboard

![Consumer Analytics Dashboard Demo](images/consumer_analytics_dashboard_demo.gif)

The Power BI report includes:

- **Campaign Analytics** — campaign response, spend, shopping frequency, and basket value patterns
- **Customer Insights** — customer purchasing behavior and demographic insights

📊 [Download Power BI Dashboard](dashboard/Consumer_Analytics_Dashboard.pbix)

---

## 🏗️ Architecture

```text
Raw CSV Data
      │
      ▼
   Bronze
 Raw Delta
      │
      ▼
   Silver
Cleaned Data
      │
      ▼
    Gold
 ┌───────────────┐
 │ Customer 360  │
 │ Campaign      │
 │ Analytics     │
 └───────┬───────┘
         │
         ▼
      Power BI
```

---

## 🔄 Data Pipeline

### 1. Data Profiling & Quality

📓 [View Notebook](notebooks/01_Source_Profiling_and_Data_Quality.ipynb)

Profiled source datasets for grain, keys, duplicates, nulls, referential integrity, distributions, and observation coverage.

Key findings included **5,164 redundant coupon records** and demographic coverage of **801 / 2,500 households (32.04%)**.

### 2. Bronze — Raw Ingestion

📓 [View Notebook](notebooks/02_Bronze_Layer_Ingestion.ipynb)

Ingested transaction, product, demographic, campaign, and coupon CSV files with **PySpark** and stored them as managed **Delta tables** with ingestion metadata.

### 3. Silver — Cleaning & Standardization

📓 [View Notebook](notebooks/03_Silver_Transformations.ipynb)

Cleaned and standardized Bronze datasets, removed redundant coupon records, derived transaction time features, and validated transformation row counts.

### 4. Gold — Analytical Models

**Customer 360**  
📓 [View Notebook](notebooks/04_Customer_360.ipynb)

Built a customer-level model combining purchasing behavior, campaign engagement, coupon activity, and available demographics.

**Campaign Performance**  
📓 [View Notebook](notebooks/05_Campaign_Analysis.ipynb)

Built campaign-level metrics covering household participation, coupon redemption, spend, baskets, and basket value.

**Campaign Behavioral Signals**  
📓 [View Notebook](notebooks/06_Growth_Opportunities.ipynb)

Compared campaign-period purchasing behavior with equal-length pre-campaign periods across **spend, basket frequency, and average basket value**.

---

## 📈 Power BI Analytics

### Campaign Analytics

![Campaign Analytics Dashboard](images/campaign_analytics_dashboard.png)

Explores campaign response alongside changes in spend, shopping frequency, and average basket value.

### Customer Insights

![Customer Insights Dashboard](images/customer_insights_dashboard.png)

Explores customer spend, shopping frequency, basket value, activity, and available demographic characteristics.

---

## ⚠️ Analytical Notes

- Campaign comparisons are **descriptive, not causal**, because no comparable control group is available.
- Demographic attributes cover **32.04% of purchasing households**, so demographic analysis represents a subset of the customer population.
- Campaign 24 is flagged for an **incomplete observation window** and excluded from relevant comparative visuals.
- One-to-many datasets are aggregated to the required grain before joining to prevent metric inflation.

---

## 🛠️ Tech Stack

**Databricks · PySpark · Apache Spark · SQL · Delta Lake · Unity Catalog · Power BI · Git/GitHub**

---

## 📁 Repository

```text
├── notebooks/
│   ├── 01_Source_Profiling_and_Data_Quality.ipynb
│   ├── 02_Bronze_Layer_Ingestion.ipynb
│   ├── 03_Silver_Transformations.ipynb
│   ├── 04_Customer_360.ipynb
│   ├── 05_Campaign_Analysis.ipynb
│   └── 06_Growth_Opportunities.ipynb
│
├── dashboard/
│   └── Consumer_Analytics_Dashboard.pbix
│
├── images/
└── README.md
```

---

## 📚 Data Source

**dunnhumby — The Complete Journey**

[Kaggle Dataset](https://www.kaggle.com/datasets/frtgnn/dunnhumby-the-complete-journey)

Used for educational and portfolio purposes.
