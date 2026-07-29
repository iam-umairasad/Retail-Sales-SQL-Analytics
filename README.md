# 📊 Retail Sales Analytics — SQL Business Intelligence Project

**End-to-end retail analytics built entirely in T-SQL** — from raw CSV ingestion to production-ready reporting views — designed to answer the questions a retail business actually asks: *Where is revenue coming from? Who are our best customers? Which products are dying on the shelf?*

![SQL Server](https://img.shields.io/badge/Database-SQL%20Server-CC2927?logo=microsoftsqlserver&logoColor=white)
![T-SQL](https://img.shields.io/badge/Language-T--SQL-blue)
![Focus](https://img.shields.io/badge/Focus-Business%20Intelligence-4B8BBE)
![Portfolio](https://img.shields.io/badge/Type-Portfolio%20Project-informational)

---

## 🧭 Project Overview

This project builds a full analytics pipeline on top of a historical retail dataset — customer profiles, a product catalog, and three years of sales transactions — and turns it into a set of business-ready insights and reusable reporting views.

Every script in this repository is built around business strategy first and technical implementation second, ensuring that:

- Handling **real data-quality problems** (missing keys, blank values, duplicate risk) before any analysis runs
- Guarding against **silent calculation errors** — integer division, divide-by-zero conditions, and double-counted totals — that can quietly distort business metrics if left unchecked
- Structures the codebase following modern data engineering practices: raw layer → exploration → analysis → **stable, queryable reporting views**

**At a glance, this analysis surfaced:**

| Metric | Value |
|---|---|
| Total revenue analyzed | **$29.36M** |
| Orders processed | **27,659** unique invoices |
| Customers profiled | **18,484** |
| Products in catalog | **295** |
| Data coverage | **Dec 2010 – Jan 2014** |
| Revenue concentration | **Bikes** drive ~96% of total revenue — a single-category dependency risk surfaced directly by the segmentation queries |
| Top markets | United States ($9.16M) and Australia ($9.06M) lead, together outperforming the UK, Germany, France, and Canada combined |

That last row is the kind of finding this project is built to produce automatically — not just "here's a query," but "here's what the query tells the business."

---

## 🗂️ Repository Architecture

```
RetailSalesAnalytics/
│
├── Datasets/
│   ├── csv-files/
│   │   ├── customer_behavior_report.csv    # Exported output of the customer behavior reporting view
│   │   ├── dim_customers.csv               # Customer profiles (18,484 rows)
│   │   ├── dim_products.csv                # Product catalog (295 rows)
│   │   ├── fact_sales.csv                  # Transaction-level sales (60,398 rows)
│   │   └── product_performance_report.csv  # Exported output of the product performance reporting view
│   └── RetailSalesAnalytics.bak            # Full SQL Server backup — restore for instant setup
│
├── Docs/
│   └── project_roadmap.png                 # Visual project roadmap
│
├── Scripts/
│   ├── 00_db_setup_and_ingestion.sql       # Phase 0 — Database + table creation, BULK INSERT loading
│   ├── 01_database_exploration.sql         # Phase 1 — Schema audit + data quality enforcement
│   ├── 02_dimentions_exploration.sql       # Phase 2 — Dimension/category auditing
│   ├── 03_date_exploration.sql             # Phase 3 — Timeline boundaries
│   ├── 04_measure_exploration.sql          # Phase 4 — Core business metrics (KPIs)
│   ├── 05_magnitude_analysis.sql           # Phase 5 — Aggregated comparisons across dimensions
│   ├── 06_ranking_analysis.sql             # Phase 6 — Top-N / Bottom-N performers
│   ├── 07_change_over_time_analysis.sql    # Phase 7 — Trend & seasonality analysis
│   ├── 08_commulative_analysis.sql         # Phase 8 — Running totals & moving averages
│   ├── 09_performance_analysis.sql         # Phase 9 — Year-over-year performance benchmarking
│   ├── 10_part_to_whole_analysis.sql       # Phase 10 — Proportional / percentage-of-total analysis
│   ├── 11_data_segmentation_analysis.sql   # Phase 11 — Customer & product segmentation
│   ├── customer_behavior_report.sql        # Phase 12 — Final view: customer behavior report
│   └── Product_Performance_Report.sql      # Phase 12 — Final view: product performance report
│
└── README.md
```

---

## 🚀 Getting Started

**Option A — Recommended (Fastest): Database Restoration**
1. Copy `RetailSalesAnalytics.bak` (found in `Datasets/`) to the local SQL Server default backup directory.
2. Open SQL Server Management Studio (SSMS): Right-click **Databases** → **Restore Database...** → Select **Device** → Browse and select the `.bak` file → Click **OK**.
3. The fully populated `RetailSalesAnalytics` database is now restored. Skip directly to executing analysis scripts starting from `01_database_exploration.sql` in the `Scripts/` folder.

**Option B — From scratch: Run the ingestion script**
1. Open `00_db_setup_and_ingestion.sql`.
2. Update the three `BULK INSERT ... FROM 'C:\...'` file paths to reference the exact local directory containing the source CSV files in `Datasets/csv-files/`.
3. Run the script in SQL Server Management Studio (SSMS) — it drops any existing copy of the database, rebuilds the schema, and loads all three tables.

---

## 🗺️ Roadmap & Phase-by-Phase Breakdown

The project follows a 13-phase roadmap, split into two stages: **Exploratory Data Analysis (EDA)**, which builds understanding of the raw data, and **Advanced Analytics**, which turns that understanding into business-facing insight.

### Stage 1 — Exploratory Data Analysis

| Phase | Script | What it does | Why it matters |
|---|---|---|---|
| 0 | `00_db_setup_and_ingestion.sql` | Builds the database, defines the `fact_sales` / `dim_customers` / `dim_products` star schema, and bulk-loads the CSVs | Establishes a clean, repeatable foundation — the entire project can be rebuilt from raw files in one run |
| 1 | `01_database_exploration.sql` | Audits table/column structure via `INFORMATION_SCHEMA`, then enforces `NOT NULL` and `PRIMARY KEY` constraints on key ID columns | Turns a "trust the data" assumption into an enforced guarantee — no blank customer, product, or order IDs can slip through |
| 2 | `02_dimentions_exploration.sql` | Lists distinct countries and audits the category → subcategory → product hierarchy | Confirms the catalog hierarchy is clean before it's used as a grouping key anywhere downstream |
| 3 | `03_date_exploration.sql` | Finds the first/last order dates and product launch dates; calculates customer age ranges | Establishes the true reporting window and flags that `GETDATE()` can't be used for age math on historical data |
| 4 | `04_measure_exploration.sql` | Calculates core KPIs — total sales, total quantity, average price, order counts, active vs. registered customers | Gives a single-glance business health check, packaged as a unified KPI summary table |
| 5 | `05_magnitude_analysis.sql` | Breaks totals down by country, category, subcategory, and customer | Answers "how big is each slice" across every major business dimension |
| 6 | `06_ranking_analysis.sql` | Surfaces Top-N / Bottom-N customers and products using `RANK()`, `DENSE_RANK()`, and `TOP` | Identifies VIP customers and best/worst-selling products for targeted action |

### Stage 2 — Advanced Analytics

| Phase | Script | What it does | Why it matters |
|---|---|---|---|
| 7 | `07_change_over_time_analysis.sql` | Tracks monthly/yearly sales trends using `DATETRUNC`, `YEAR()`, `MONTH()` | Surfaces seasonality and growth trajectory for planning and forecasting |
| 8 | `08_commulative_analysis.sql` | Builds running totals and moving average prices with `SUM() OVER()` / `AVG() OVER()` | Powers Year-to-Date and lifetime growth tracking without losing row-level detail |
| 9 | `09_performance_analysis.sql` | Benchmarks performance across time windows using window functions | Extends the cumulative view into a repeatable performance-tracking pattern |
| 10 | `10_part_to_whole_analysis.sql` | Calculates each category's percentage share of total revenue, orders, and customers | Deliberately avoids a common overcounting trap (see below) when measuring order share |
| 11 | `11_data_segmentation_analysis.sql` | Buckets products into cost tiers and customers into lifecycle segments (VIP, New, At Risk, Lost, etc.) via layered `CASE` logic | Identifies baseline segmentation limitations, serving as an exploratory draft that is formally refined in the final `customer_behavior_report.sql`. |
| 12 | `customer_behavior_report.sql` + `Product_Performance_Report.sql` | Consolidates every prior phase into two production `VIEW`s: a customer behavior report and a product performance report | The final deliverable — plug-and-play views that a BI tool (Power BI, Tableau) or analyst can query directly, no re-derivation needed |

---

## 🧠 Key Analytical & Engineering Decisions

This section is the "why" behind the code — outlines the strategic analytical choices and defensive engineering practices applied throughout the repository—detailing the key trade-offs, edge-case protections, and business logic decisions.

**1. Multi-Category Order Overcounting Risk (Phase 10)**
While financial revenue is strictly additive across product categories, transaction volume metrics are non-additive. Because a single customer invoice can contain items spanning multiple product categories, aggregating category-level order counts via window functions like `SUM() OVER()` creates an artificially inflated total order denominator due to shared invoice double-counting. 

To calculate the true **Transaction Volume Share by Category** (`percentage_of_total`), the logic executes an independent scalar subquery—`(SELECT COUNT(DISTINCT order_number) FROM fact_sales)`—to establish the absolute overall invoice count across the database as the constant baseline denominator.

**2. Integer division is a silent bug generator**
SQL Server divides two integers as an integer, so `5 / 10` returns `0`, not `0.5`. Every percentage and ratio calculation in this project explicitly casts to `FLOAT` before dividing (`CAST(x AS FLOAT) / y`), so percentages report real precision instead of quietly rounding to zero.

**3. Divide-by-zero defense**
Metrics like average order value, average monthly spend, and profit margin all divide by a count or total that could legitimately be zero (a customer with no orders yet, a product with no sales). Every one of these calculations is wrapped in `NULLIF(denominator, 0)` combined with `ISNULL(..., 0)` so the report degrades gracefully instead of crashing.

**4. Missing records are preserved, not dropped**
`LEFT JOIN`s are used deliberately throughout so that a sales row with a missing customer or product profile still appears in the results, rather than silently disappearing. The Product Performance view goes further with a `FULL OUTER JOIN` between the catalog and sales summary — this is the only way to simultaneously catch *unsold catalog items* (dead stock) and *sales records with no matching product metadata* in the same query.

**5. A fixed analytical anchor date, not `GETDATE()`**
Because the dataset is historical (ending January 2014), using `GETDATE()` for age or recency calculations would produce nonsensical results today. All recency and tenure metrics are calculated against a fixed snapshot date instead. The final reporting views tighten this further, anchoring to `2013-12-31` specifically so that year-over-year comparisons (2012 vs. 2013) compare two complete, unskewed 12-month periods rather than a full year against a partial one.



**6. Using `RANK()` vs. `DENSE_RANK()` a deliberate choice, not a default**
When ranking data in SQL, ties (like two products selling the exact same amount) are handled differently depending on the goal:

* **`RANK()` (Leaves Gaps):** If two items tie for 1st place, the next item becomes 3rd place (1, 1, 3). This is best for strict top-10 leaderboards where tied items take up multiple competition spots.
* **`DENSE_RANK()` (No Gaps):** If two items tie for 1st place, the next item becomes 2nd place (1, 1, 2). This ensures no rank numbers are skipped, which is useful when every tied customer or product needs to be included sequentially.


**7. Pre-aggregate before join**
The Product Performance view aggregates `fact_sales` down to one row per product *before* joining it to the product catalog, rather than joining the full transaction table directly. This condenses sales data to a single row per product before joining, preventing the database from processing thousands of duplicate intermediate rows and keeps the final `FULL OUTER JOIN` fast even as the fact table grows.

**8. Multi-Factor Customer Segmentation & Logic Refinement**
Initial customer grouping in script `11` relied on simple rule-based thresholds that created coverage gaps for edge cases. In the production reporting view (`v_customer_behavior_report`), this logic was overhauled into multi-factor layered `CASE` statements combining tenure (`months_since_first_purchase`), inactivity windows (`recency`), and lifetime volume (`total_sales`). 

This structural update ensures high-value accounts experiencing recent inactivity are accurately identified as "Core VIP (Cooling Down)" rather than falling into default broad buckets or misclassifying customer risk profiles.

---

## 🛠️ Technical Skills Demonstrated

| Category | Skills |
|---|---|
| **Database Design** | Star schema modeling (fact/dimension tables), surrogate vs. natural keys, `PRIMARY KEY` constraints, `NOT NULL` enforcement |
| **Data Ingestion** | `BULK INSERT`, `DATEFORMAT` handling, environment reset scripting (`DROP`/`CREATE DATABASE`) |
| **Core T-SQL** | `JOIN` strategy selection (`INNER`, `LEFT`, `FULL OUTER`), `GROUP BY`, `CASE` logic, `UNION ALL` |
| **CTEs & Subqueries** | Multi-layered `WITH` clauses for staged, readable transformations |
| **Window Functions** | `SUM() OVER()`, `AVG() OVER()`, `RANK()`, `DENSE_RANK()`, `ROW_NUMBER()`, `PARTITION BY` |
| **Date Intelligence** | `DATEDIFF`, `DATETRUNC`, `YEAR()`, `MONTH()`, `DATEPART()`, `FORMAT()` |
| **Defensive SQL** | `NULLIF`, `ISNULL`, explicit `FLOAT` casting to prevent integer division and divide-by-zero errors |
| **Business Logic Translation** | Multi-factor `CASE` segmentation (customer lifecycle tiers, product margin/obsolescence tiers) |
| **Reporting Layer** | `CREATE OR ALTER VIEW` for stable, reusable, BI-tool-ready reporting objects |

---

## 📌 Notes on the Data

This project uses a historical retail dataset (bikes, accessories, and clothing) spanning December 2010 to January 2014, structured as a classic star schema:

- **`fact_sales`** — one row per product line on an order (order number, product/customer keys, dates, revenue, quantity, price)
- **`dim_customers`** — customer demographics (country, gender, marital status, birthdate)
- **`dim_products`** — product catalog (category, subcategory, cost, product line, launch date)

---

## 🤝 Credits & Acknowledgments

This project follows the structure and dataset used in **Baraa Khatib Salkini's** YouTube tutorial, *"SQL Data Analyst Portfolio Project"* on his channel **Data With Baraa**. The phase roadmap, dataset, and the core project architecture is based on that tutorial — full credit to Baraa for the teaching structure and dataset design that made this project possible.

🔗 Source: [SQL Data Analyst Portfolio Project](https://youtu.be/2jGhQpbzHes?si=PSOcH7UpXmSSfw5f)

---

## 📬 Contact

Feel free to reach out or check out my other data projects on my [GitHub profile](https://github.com/Umair-Asad2001).
