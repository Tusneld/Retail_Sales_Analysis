# Retail Sales Analysis Dashboard

**A Power BI business intelligence solution for multi-region retail performance tracking, Like-for-Like (LFL) growth analysis, and customer segmentation - built on a 5,000-row transactional dataset spanning 50 stores, 20 products, and 2 fiscal years.**

---
![Retail Sales Dashboard](https://github.com/Tusneld/Retail_Sales_Analysis/blob/04b6afdae46aca2966c80c3ef063afe8ac24db36/Retail%20Sales%20Dashboard/Dashboard.JPG)
---

## Table of Contents

- [Overview](#overview)
- [Business Problem](#business-problem)
- [Dataset](#dataset)
- [Understanding LFL (Like-for-Like) in Retail](#understanding-lfl-like-for-like-in-retail)
- [Business Questions](#business-questions)
- [Solution & Approach](#solution--approach)
- [Data Model](#data-model)
- [DAX Measures](#dax-measures)
- [Tech Stack](#tech-stack)
- [Findings & Recommendations](#findings--recommendations)
- [Lessons Learned](#lessons-learned)
- [How to Use This Project](#how-to-use-this-project)
- [Author](#author)

## Overview

This project delivers a **multi-page retail sales dashboard** built in Power BI, analyzing two full fiscal years (2023–2024) of transactional data across a 50-store retail network. Rather than reporting raw totals, the dashboard is purpose-built to answer the question every retail executive actually cares about: **is the business growing because it's getting better, or just because it's getting bigger?**

That distinction - organic performance vs. network expansion - is the difference between a dashboard that looks impressive and one that actually drives decisions. This project is built around the latter.

## Business Problem

The organization lacked a unified, data-driven view of its retail operations. Decision-makers had no real-time visibility into critical performance indicators - Like-for-Like (LFL) store growth, customer segment behavior, and regional sales distribution — making it difficult to separate genuine operational wins from growth that was simply a byproduct of opening more stores. Without that separation, marketing spend, staffing decisions, and expansion plans were all being made on incomplete information.

## Dataset

| Table | Rows | Description |
|---|---|---|
| `Retail_Sales` | 5,000 | Transaction-level fact table: order, store, product, quantity, pricing, discount, cost, profit, sales channel, customer segment, and date fields |
| `Store_Master` | 50 | Store dimension: store ID, name, region, city, and LFL eligibility flag |
| `Product_Master` | 20 | Product dimension: product ID, name, category, unit price, and unit cost |

**Time span:** January 2023 – December 2024 (2 full fiscal years, enabling true year-over-year comparison)
**Grain:** One row per order line in `Retail_Sales`, joined to `Store_Master` and `Product_Master` via a star schema (see [Data Model](#data-model))

## Understanding LFL (Like-for-Like) in Retail

**Like-for-Like (LFL)**, also known as **Like-to-Like (LTL)**, is a fundamental performance metric in the retail industry. It measures the sales performance of stores that have been open for the same duration across two distinct time periods. By focusing solely on these established locations, the metric strips away the noise created by new store openings or closures.

### Why LFL Matters

- **True Growth Assessment:** Isolates the performance of core operations. Comparing identical stores year-over-year reveals whether the existing business is genuinely growing, independent of revenue changes driven by physical store expansion.
- **Operational Benchmarking:** Provides an accurate gauge of store productivity, distinguishing success driven by operational efficiency or customer demand from growth that's simply a function of adding more locations.
- **Strategic Insight:** Helps leadership identify which stores are thriving and which require intervention - the foundation of a stable, profitable retail network.

## Business Questions

1. What is the Year-over-Year (YoY) sales growth percentage?
2. Which products, regions, and cities are the primary drivers of revenue?
3. How does the current store network perform when filtered by the **LFL (Like-for-Like)** metric?
4. What is the Average Order Value (AOV), and how does it correlate with total profitability?

## Solution & Approach

- **Data Modeling:** Implemented a star schema with clean one-to-many relationships between the `Retail_Sales` fact table and the `Store_Master` and `Product_Master` dimension tables.
- **KPI Development:** Built an extensive suite of DAX measures covering both primary business tracking (Total Sales, YoY Growth) and granular secondary analysis (AOV, Average Discount %, Average Cost).
- **Interactive Visualization:** Designed a dense, single-page executive dashboard featuring monthly trend lines, regional and city-level comparisons, category and product breakdowns, and customer segment analysis - all responsive to Year and LFL_Store slicers.

## Data Model

```
Store_Master (1) ────< Retail_Sales >──── (1) Product_Master
   Store_ID                                    Product_ID
   Store_Name         [Fact Table]              Product_Name
   Region             Order_ID                  Category
   City               Quantity                  Unit_Price
   LFL_Store          Sales, Cost, Profit        Unit_Cost
                       Sales_Channel
                       Customer_Segment
                       Year, Month, Month_Name
```
---
![Retail Sales Dashboard](https://github.com/Tusneld/Retail_Sales_Analysis/blob/04b6afdae46aca2966c80c3ef063afe8ac24db36/Retail%20Sales%20Dashboard/star_schema.JPG)
---

A star schema was chosen deliberately over a flattened single table: it keeps the fact table lean, avoids duplicating store and product attributes across 5,000 rows, and - critically - ensures DAX measures using `CALCULATE` and `FILTER` evaluate correctly against filter context rather than silently over- or under-counting.

## DAX Measures

### Primary KPIs

```dax
Total Sales      = SUM('RetailSales'[Sales])
Total Quantity   = SUM('RetailSales'[Quantity])
Total Profit     = SUM('RetailSales'[Profit])
Total Stores     = DISTINCTCOUNT('StoreMaster'[StoreID])

Sales 2023       = CALCULATE([Total Sales], 'RetailSales'[Year] = 2023)
Sales 2024       = CALCULATE([Total Sales], 'RetailSales'[Year] = 2024)
YoY Growth %     = DIVIDE([Sales 2024], [Sales 2023]) - 1
```

### Secondary KPIs

```dax
Total Orders         = DISTINCTCOUNT('RetailSales'[OrderID])
Average Cost         = AVERAGE('RetailSales'[Cost])
Average Order Value  = DIVIDE([Total Sales], [Total Orders])
Average Discount %   = AVERAGE('RetailSales'[DiscountPercentage])
Average Price        = AVERAGE('RetailSales'[UnitPrice])
```

## Tech Stack

- **BI Tool:** Power BI Desktop
- **Data Processing:** Power Query (ETL for header cleaning and transformation)
- **Data Modeling:** Star schema design with one-to-many relationship mapping
- **Language:** DAX (Data Analysis Expressions)

## Findings & Recommendations

- **Finding:** The business achieved **21.9% YoY revenue growth** between 2023 and 2024, with total sales reaching **$185M** across **50 stores** and **13K units sold**.
- **Finding:** LFL analysis shows that certain regions are outperforming others in *organic* growth - growth from existing stores, not new openings - which is the more reliable signal for where operational excellence is actually happening.
- **Finding:** The customer base splits nearly evenly across Regular, Premium, and New Customer segments (~33% each), suggesting no single segment is being disproportionately relied upon for revenue.
- **Recommendation:** Direct marketing and loyalty campaigns toward the "Regular" customer segment to boost retention, since this group represents recurring, lower-acquisition-cost revenue.
- **Recommendation:** Use the LFL filter as a standing check before approving any new store investment - a region growing 21.9% overall but flat on LFL is expanding, not necessarily improving.

## Lessons Learned

- **Data Integrity:** Ensuring proper header structure and cleaning data in Power Query is essential to preventing null-value errors that quietly corrupt downstream calculations.
- **Modeling Best Practices:** Prioritizing one-to-many relationships over many-to-many significantly improves both measure accuracy and dashboard performance at scale.
- **Visual Hierarchy:** Consistent color theming and purposeful icon use materially improve executive engagement - a dashboard that requires a legend to be read has already lost half its audience.
- **Metric Design:** A single well-chosen metric like LFL can surface more strategic insight than a dozen surface-level KPIs, because it isolates a variable that actually matters (organic performance) instead of drowning it in aggregate noise.

## How to Use This Project

No prior Power BI experience required - here's how to explore it:

1. **Install Power BI Desktop** (free) from [Microsoft's official site](https://www.microsoft.com/en-us/power-platform/products/power-bi/desktop).
2. **Download the project files** from this repository: the `.pbix` dashboard file and the source dataset (`Retail_DAX_Analytics_5000.xlsx`).
3. **Open the `.pbix` file** in Power BI Desktop - the full data model, relationships, and visuals load automatically.
4. **Use the slicers** at the top of the dashboard (Year, LFL_Store) to compare 2023 vs. 2024 performance, or isolate Like-for-Like stores to see organic growth in isolation.
5. **Hover over any chart or bar** to see exact figures via tooltip.
6. **Want to extend it?** Open the Model view to see the star schema, or the DAX measures pane to see exactly how each KPI is calculated - a good starting point for adapting this template to a different retail dataset.

*Prefer not to install anything? Ask for a Power BI Service published link or a static PDF/image export for quick viewing on any device.*

## Author

**Tusnelde Endjala**
Data Analyst & Aspiring Data & ML Engineer | Power BI · SQL · Python · Data Modeling 

📂 [Portfolio](https://tusnelde.vercel.app) · 💻 [GitHub](https://github.com/Tusneld)

**Last Updated:** September 2026
