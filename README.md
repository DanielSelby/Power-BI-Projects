# Aurora Bank Insights — Data Analysis & Power BI Dashboard

**Project:** Aurora Bank Insights — Customer, Transaction & Risk Analysis

**Author:** (Analyst Name)

**Date:** 2025-11-24

---

## Table of Contents

1. [Introduction](#introduction)
2. [Project Aim](#project-aim)
3. [Project Description](#project-description)
4. [Data Acquisition & Preparation](#data-acquisition--preparation)
5. [Data Cleaning & Transformation](#data-cleaning--transformation)
6. [Data Model & Relationships](#data-model--relationships)
7. [Analysis & Dashboards](#analysis--dashboards)

   * [Customer Demographics Dashboard](#customer-demographics-dashboard)
   * [Credit Score Analysis Dashboard](#credit-score-analysis-dashboard)
   * [Financial Health & Transactions Dashboard](#financial-health--transactions-dashboard)
   * [Card Ownership & Usage Dashboard](#card-ownership--usage-dashboard)
8. [Key Findings & Interpretation](#key-findings--interpretation)
9. [Recommendations](#recommendations)
10. [Deliverables](#deliverables)
11. [Power BI Implementation Notes](#power-bi-implementation-notes)
12. [DAX Snippets & Useful Measures](#dax-snippets--useful-measures)
13. [How to Reproduce](#how-to-reproduce)
14. [Limitations & Next Steps](#limitations--next-steps)

---

## Introduction

This repository documents the Aurora Bank Insights project — an end-to-end analysis of Aurora Bank's customers, transactions, cards, and merchant data. The goal is to provide actionable insights for improved risk management, customer targeting, and product optimization through an interactive Power BI dashboard suite and an analysis report.

## Project Aim

Deliver a data-driven view into customer profiling, credit health, transaction behavior and card usage to:

* Improve risk identification and underwriting decisions
* Identify high-value customer segments for marketing and upsell
* Reduce operational friction caused by transaction errors
* Support product and channel optimizations using transaction and merchant insights

## Project Description

The project focuses on customer profiling & segmentation across multiple dimensions:

* Demographics: age, gender, income, geography
* Credit health: credit score distribution & relationship to transaction behaviour
* Financial risk: Debt-to-Income (DTI) calculations and high-risk cohorts
* Transactions: spending patterns, merchant categories, and error analysis
* Cards: card penetration, brand mix, and usage trends

Analysis is driven by a normalized model combining `users_data`, `transactions_data`, `cards_data`, `mcc_codes` and a `calendar` dimension.

## Data Acquisition & Preparation

**Source:** Aurora Bank-supplied dataset (Excel). Tables:

* `users_data` — customer demographics and credit attributes
* `transactions_data` — transaction records and merchant info
* `cards_data` — card issuance and brand information
* `mcc_codes` — merchant category mapping
* `calendar` — date dimension supporting time analysis

**Ingest:** Data was imported into Power BI using Power Query (`Get Data` → Excel → relevant sheets). No external SQL data-cleaning was used.

## Data Cleaning & Transformation

Performed entirely in Power Query (Power BI):

* Promoted first rows to headers and set/detected data types (Dates, Text, Decimal)
* Created a dedicated `calendar` dimension for time intelligence
* Engineered columns:

  * Age brackets (Youth, Young Adult, Adult, Senior)
  * Credit score categories (e.g., Poor / Fair / Good / Excellent)
  * DTI ratio and DTI risk categories (Low / High / Severe)
  * Yearly income brackets
  * Risk score composite metric
* Standardized geographical values (e.g., convert US states in `merchant_state` to `United States of America` for clarity)
* Removed redundant columns and handled missing values with appropriate imputation or exclusion

**Example geographical standardization logic (Power BI approach):**

```DAX
merchant_country =
VAR StateName = transactions_data[merchant_state 2]
VAR USStates =
{
    "Alaska","Alabama","Arkansas","Arizona","California","Colorado","Connecticut",
    "Delaware","District of Columbia","Florida","Georgia","Hawaii","Iowa","Idaho",
    "Illinois","Indiana","Kansas","Kentucky","Louisiana","Massachusetts","Maryland",
    "Maine","Michigan","Minnesota","Missouri","Mississippi","Montana",
    "North Carolina","North Dakota","Nebraska","New Hampshire","New Jersey",
    "New Mexico","Nevada","New York","Ohio","Oklahoma","Oregon","Pennsylvania",
    "Rhode Island","South Carolina","South Dakota","Tennessee","Texas","Utah",
    "Vermont","Virginia","Washington","Wisconsin","West Virginia","Wyoming"
}
RETURN
IF (
    StateName IN USStates,
    "United States of America",
    StateName
)
```

## Data Model & Relationships

A star schema was implemented with `calendar`, `users_data`, `cards_data` and `mcc_codes` as dimension tables and `transactions_data` as the central fact table. Relationships used primary identifier keys (Customer ID, Card ID, Transaction ID) and date keys to support time-based calculations.

## Analysis & Dashboards

Four Power BI dashboard pages were created, each with KPIs and visuals designed for executive and operational audiences.

### Customer Demographics Dashboard

**KPIs:** Total Clients (2,000), Average Age (45), Average Income ($45.72K), Average Debt ($63.71K)

**Insights:** Seniors (56–70) have the highest transaction volume ($2.4M). Geographical concentration: California ($0.70M) and other top states.

**Visuals:** KPI cards, age distribution histogram, map of transaction values by state, income band bar chart.

### Credit Score Analysis Dashboard

**KPI:** Average Credit Score = 710

**Insights:** Largest cohort lies between 700–800 credit score. Biggest transaction volumes originate from Fair ($2.5M) and Excellent ($1.8M) cohorts.

**Visuals:** Score distribution histogram, transaction amount by score category, cohort timeline.

### Financial Health & Transactions Dashboard

**KPI:** Average DTI Ratio = 139.36

**Insights:** Youth and Young Adult groups show high DTI (1.61, 1.59 respectively), signaling higher lending risk. Error analysis shows `Insufficient BAD PIN Balance` as dominant error.

**Visuals:** DTI trend line, DTI by age bracket, merchant category treemap, error type frequency bar chart.

### Card Ownership & Usage Dashboard

**KPIs:** Total Cards Issued = 6,146, Average Cards per Customer = 3, Total Transactions = 157K

**Insights:** Mastercard comprises 57.13% of cards and leads in transaction volume ($3.5M). Exponential growth in card issuance since 2015.

**Visuals:** Card brand donut chart, issuance trend area chart, transactions by brand bar chart.

## Key Findings & Interpretation

* The customer base is largely mature and credit healthy (avg score 710).
* Seniors contribute the highest transactional value and are high-value targets for retention and premium product offers.
* Younger cohorts (Youth, Young Adult) show elevated DTI ratios — important for credit policy adjustments and financial education targeting.
* Operational friction exists with transaction errors (balance/PIN issues); addressing this will improve customer experience and reduce service costs.
* Automotive and Retail merchant categories drive the most transaction value — marketing and partnership opportunities exist here.

## Recommendations

1. **Reduce Transaction Errors:** Implement real-time balance checks, proactively alert customers when balances fall below common thresholds, and review PIN retry flows for better UX.
2. **Targeted Lending Products:** Offer smaller, secured lending and credit-building products to Youth/Young Adult cohorts; bundle with financial literacy programs.
3. **Marketing Focus:** Concentrate spend on high-transaction merchant categories (Automotive & Retail) and high-performing states (e.g., California, Texas).
4. **Credit Upsell:** Cross-sell higher-limit products to customers in the 700–800 credit band who show high transaction activity.
5. **Product Analytics:** Continuously monitor card issuance trends to identify saturation and product fatigue.

## Deliverables

* Comprehensive analysis report (this repository)
* Power BI dashboard package (.pbix)
* Executive summary slide deck
* Data dictionary & model diagram

## Power BI Implementation Notes

* Data cleaning and feature engineering performed in Power Query (Power BI Desktop).
* Use the `calendar` table with continuous date ranges (mark as date table) to enable time intelligence functions like `SAMEPERIODLASTYEAR()`.
* Use slicers for Age, Credit Score Category, Card Brand and Geography to enable interactive filtering.
* Use `Bookmarks` and `Buttons` to create an executive-friendly story flow across dashboard pages.

## DAX Snippets & Useful Measures

**Total Clients**

```DAX
Total Clients = DISTINCTCOUNT(users_data[customer_id])
```

**Average DTI**

```DAX
Average DTI = AVERAGE(users_data[DTI])
```

**Total Transaction Amount**

```DAX
Total Transaction Amount = SUM(transactions_data[transaction_amount])
```

**Error Category (example)**

```DAX
Error Category =
SWITCH(
    TRUE(),
    CONTAINSSTRING(transactions_data[error], "Bad Card Number"), "Bad Card Number",
    CONTAINSSTRING(transactions_data[error], "Bad CVV"), "Bad CVV",
    CONTAINSSTRING(transactions_data[error], "Bad Expiration"), "Bad Expiration",
    CONTAINSSTRING(transactions_data[error], "Bad PIN"), "Bad PIN",
    CONTAINSSTRING(transactions_data[error], "Bad Zipcode"), "Bad Zipcode",
    CONTAINSSTRING(transactions_data[error], "Insufficient Balance"), "Insufficient Balance",
    CONTAINSSTRING(transactions_data[error], "Technical Glitch"), "Technical Glitch",
    "Unknown"
)
```

**Average Credit Score**

```DAX
Average Credit Score = AVERAGE(users_data[credit_score])
```

## How to Reproduce

1. Clone or download this repository.
2. Open Power BI Desktop and import the provided Excel file(s).
3. Implement the data model as described (link the date key to calendar table and connect customer/card keys).
4. Recreate the calculated columns and DAX measures provided.
5. Rebuild visuals using the visual guidance above and apply consistent color palette and theme.

## Limitations & Next Steps

**Limitations**

* Source dataset is a snapshot and may not reflect current dynamic customer behavior.
* DTI and risk measures use available fields; additional data (credit bureau history, repayments) would improve risk accuracy.

**Next Steps**

* Integrate additional datasets (e.g., credit bureau, product usage logs) for richer modelling.
* Build automated data pipelines (Azure Data Factory / Synapse or SQL store) for scheduled refreshes.
* Develop predictive models (credit default probability) and integrate them into the Power BI model for near-real-time risk scoring.

---

## Contact

For questions or to request the Power BI `.pbix`, data dictionary, or executive slide deck, contact: **(Daniel Addy Selby) —[(daddyselbydataanalytics@gmail.com)**

--- 

*End of README*
