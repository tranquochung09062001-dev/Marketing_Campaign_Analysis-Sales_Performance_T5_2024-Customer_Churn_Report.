![Marketing Campaign Performance Dashboard](./images/Marketing_Dashboard_Cover.png)

# 📊 Marketing Campaign Analysis, Sales Performance (T5/2024) & Customer Churn Report | Facebook Ads + Fashion E-commerce | Power BI

**Repository:** [Marketing_Campaign_Analysis-Sales_Performance_T5_2024-Customer_Churn_Report.](https://github.com/tranquochung09062001-dev/Marketing_Campaign_Analysis-Sales_Performance_T5_2024-Customer_Churn_Report.)

_Analyzing advertising campaign performance (ROAS, ROI, CPA, CTR) alongside sales revenue by product, campaign, and customer segment | Power BI_

**+ Business question:** Which campaigns, products, and customer segments are delivering the best ROAS/ROI? Where is ad spend being wasted (high CPA, low CTR)? Why is overall ROAS high while overall ROI is negative?

**+ Domain:** Marketing Analytics / Performance Marketing (Women's fashion — Dresses, Skirts, Outfit Sets)

---

## 📑 Table of Contents
1. [📌 Background & Overview](#-background--overview)
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)
3. [🧠 Design Thinking Process](#-design-thinking-process)
4. [⚒️ Main Process](#️-main-process)
5. [📊 Key Insights & Visualizations](#-key-insights--visualizations)
6. [🛠️ Skills & Tools Applied](#️-skills--tools-applied)
7. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)

---

## 📌 Background & Overview

### 📖 What is this project about?

This project analyzes **marketing campaign performance (Facebook Ads)** alongside **fashion retail sales data**, building an interactive Power BI dashboard with **3 pages**: Overview, Campaign, Product. Objectives:

✔️ Track the core performance metrics: **ROAS, ROI, CPA, CTR, CPM, Total Spend, Total Impression**. 

✔️ Compare ad efficiency across product categories (Dresses, Skirts, Outfit Sets...) to spot budget "stars" and budget "drains".

✔️ Analyze ad-driven revenue by customer tier (Diamond/Gold/Platinum/Silver VIP, Membership).

✔️ Track performance for individual campaigns (CPA, CTR, Impressions, Engagement, Comments, Inbox) by day.

✔️ Analyze demand by size and material to support production/inventory planning.

✔️ Provide interactive filters (Category, Date, Campaign Name) to explore the data from multiple angles.

### 👤 Who is this project for?

✔️ Performance Marketing / Ads teams
✔️ Data analysts & business analysts
✔️ Product / Merchandising teams (size, material, and category planning)
✔️ Leadership needing real-time visibility into ad budget efficiency

---

## 📂 Dataset Description & Data Structure

### 📌 Data Source
- **Source:** Internal Facebook Ads campaign data combined with order data from a women's fashion retailer
- **Format:** Multiple tables in a snowflake schema, managed within the Power BI Data Model

### 📊 Data Structure & Relationships

#### 1️⃣ Table Schema

**Table 1: `fact_order`** (Fact table — order-level data)

| Column Name | Description |
|---|---|
| Ads/Direct | Whether the order came from ads or direct traffic |
| Cấp độ khách hàng (Customer Tier) | Membership tier (Diamond/Gold/Platinum/Silver VIP, Membership) |
| Chiết khấu (Discount) | Discount applied |
| Danh mục sản phẩm (Product Category) | Category (Dress, Skirt, Outfit Set...) |
| Giá / Giá vốn (Price / Cost) | Selling price and cost price |
| ID | Order ID |
| Mã sản phẩm / Mã sản phẩm 1 (Product Code) | Product identifier |

**Table 2: `fact_mkt_camp_by_sku_cost`** (Fact table — ad cost by product/day)

| Column Name | Description |
|---|---|
| CommentsTheo AM | Comment count by Account Manager |
| Inbox + CommentsTheo AM | Total inbox + comments |
| InboxTheo AM | Inbox count |
| Tiền đã chạy Theo Sản phẩm (Spend by Product) | Ad spend run for each product |
| Bài chạy theo ngày (Posts run per day) | Number of ad posts run per day |
| Bình luận về bài viết (Post Comments) | Total comments |
| CP/KQTheo AM (Cost per Result) | Cost per result |
| CPC / CPCTheo AM | Cost per click |

**Table 3: `dim_mkt_camp_cost`** (Dimension/Fact — daily campaign cost)

| Column Name | Description |
|---|---|
| Campaign_id | Campaign identifier |
| Click | Click count |
| CPC | Cost per click |
| CPM | Cost per 1,000 impressions |
| Loại ngân sách chiến dịch (Budget Type) | Budget type (daily/lifetime) |
| Lượt hiển thị (Impressions) | Impression count |
| Ngân sách chiến dịch (Campaign Budget) | Allocated budget |
| Ngày (Date) | Recorded date |
| Phân phối chiến dịch (Delivery) | Delivery method |

**Table 4: `dim_danh_sach_san_pham`** (Dimension — product master data)

| Column Name | Description |
|---|---|
| Chất liệu (Material) | Fabric (Chiffon, Linen, Silk, Organza...) |
| Danh mục (Category) | Product category |
| Giá bán / Giá bán + VAT (Sale Price / w/ VAT) | Selling price before/after VAT |
| Giá nhập / Giá vốn (Import Price / Cost) | Import price and cost price |
| ID | Unique identifier |
| Loại sản phẩm (Product Type) | Product classification |
| Mã danh mục (Category Code) | Category code |

**Table 5: `Dimdate`** (Dimension — date)

| Column Name | Description |
|---|---|
| Date | Date |
| Month / Year / YearMonth | Used for trend analysis |

**Table 6: `fact_mkt_camp_by_sku_...`** (Bridge table — SKU × Date link)

| Column Name | Description |
|---|---|
| Mã Sản phẩm (Product Code) | Product code |
| Ngày (Date) | Date |

#### 2️⃣ Data Relationships

The model follows a **snowflake schema**:

- `Dimdate` (1 → \*) connects to `fact_order` and `fact_mkt_camp_by_sku_cost` via `Date` → enables time-based trend analysis.
- `dim_danh_sach_san_pham` (1 → \*) connects to `fact_order` via product code → enables analysis by category, material, and price.
- `dim_danh_sach_san_pham` (1 → \*) connects to the bridge table `fact_mkt_camp_by_sku_...` → links ad spend by SKU back to the product.
- `fact_mkt_camp_by_sku_cost` has a many-to-many relationship with `dim_mkt_camp_cost` and the bridge table, used to aggregate ad spend at the SKU-by-day grain.

![Data Model](./images/Model_View.png)

---

## 🧠 Design Thinking Process

**1️⃣ Empathize**

![Step 1 - Empathize](./images/marketing_design_thinking_01_empathize.png)

Identified the primary stakeholder (**Head of Performance Marketing**), someone who needs to quickly assess ad budget efficiency across multiple campaigns and products at once, but doesn't have time to manually reconcile ad-spend files against order files.

**2️⃣ Define**

![Step 2 - Define](./images/marketing_design_thinking_02_define.png)

Problem statement: *"The Marketing Lead needs a unified dashboard that merges ad and order data to quickly identify which campaigns/products are actually profitable (not just ROAS, but ROI too), enabling smarter budget reallocation."*

**3️⃣ Ideate**

![Step 3 - Ideate](./images/marketing_design_thinking_03_ideate.png)

Mapped out the decision points that needed support: comparing ROAS/ROI by product category, spotting campaigns with abnormal CPA, evaluating revenue contribution by customer tier, and understanding demand by size/material to optimize inventory.

**4️⃣ Prototype & Review**

![Step 4 - Prototype and Review](./images/marketing_design_thinking_04_prototype.png)

Structured the report into **3 pages** (Overview → Campaign → Product), moving from the big picture of ad performance down to campaign-level and product-level detail.

---

## ⚒️ Main Process

1️⃣ **Data Cleaning & Preprocessing**
- Standardized product codes across `fact_order`, `fact_mkt_camp_by_sku_cost`, and `dim_danh_sach_san_pham`
- Processed the bridge table to resolve the many-to-many relationship between ad spend by SKU and by date

2️⃣ **Exploratory Data Analysis (EDA)**
- Examined the distribution of ROAS/ROI by product category and by day
- Cross-checked ad cost metrics (CPC, CPM, CPA) against revenue to spot anomalies

3️⃣ **Power BI Visualization**
- Built a snowflake schema in the Power BI Data Model, handling the many-to-many bridge table
- Created DAX measures: Roas, Rois, CPA, CAC, CTR, and "ROAS by Product" (using `ALLEXCEPT` to hold the value fixed per product regardless of other report filters)
- Designed a 3-page report with cross-filtering slicers (Category, Date, Campaign Name)

---

## 📊 Key Insights & Visualizations

### 1️⃣ Overview

![Overview](./images/Overview.jpg)

📌 **Insight 1 — High ROAS does not mean positive ROI, and this paradox needs attention:**
- **Observation:** The system overall reaches a **ROAS of 7.67** (ad revenue is 7.67x ad spend) on **Total Spend of 394.1M₫** and **Total Impression of 24M**, yet **ROI is actually negative at -21.7%**. This shows that although ad-driven revenue is many times higher than ad spend, after subtracting cost of goods, discounts, and other operating costs, the system's actual profit is **negative**.
- **Recommendation:** Don't rely on ROAS alone as the primary KPI for evaluating campaigns — track ROI/ROIS in parallel to avoid a false sense of "ad efficiency" while the business is actually losing money.

📌 **Insight 2 — "Fitted-Waist Dress" is a standout star, while most other categories sit at an average level:**
- **Observation:** In the **Roas Categories** chart, **Fitted-Waist Dress (Váy Chiết Eo Ôm) reaches a ROAS of 22.09** — far ahead of the second-best category, **A-Line Flared Dress (Váy Suông Xoè, 10.32)**, while the remaining categories (Separate Top Set, Separate Skirt Set, Separate Pants Set, Outfit Set, Dress Set) only range **4.5–8.5**.
- **Recommendation:** Prioritize additional ad budget for the Fitted-Waist Dress category, and study the campaign/creative content currently running for it in detail so the approach can be replicated across lower-ROAS categories.

### 2️⃣ Campaign

![Campaign](./images/Campaign.jpg)

📌 **Insight 3 — Low CTR may be the underlying cause of high CPA:**
- **Observation:** Overall CTR is only **0.17%**, while CPA reaches **201.3K₫**. Comparing campaigns in the table, some campaigns (e.g. the "Gina set" group) show CPA ranging widely — from about **5.1K₫ to over 20.8K₫** — indicating inconsistent targeting/creative quality across campaigns.
- **Recommendation:** Review and optimize creative content and targeting for campaigns with low CTR/high CPA, prioritizing lessons learned from the lowest-CPA campaigns within the same product group.

### 3️⃣ Product

![Product](./images/Product.jpg)

📌 **Insight 4 — Demand is heavily concentrated in sizes M and S, with almost no demand for XL:**
- **Observation:** The **"Số lượng by Size" (Quantity by Size)** chart shows sizes **M (1,285)** and **S (1,097)** accounting for the majority of units sold, while **L (655)** and **XS (414)** are noticeably lower, and **XL has only 2 units sold**.
- **Recommendation:** Concentrate inventory and production on sizes S/M, minimize XL production given its near-zero demand, and reconsider the size-L strategy to avoid excess inventory.

📌 **Insight 5 — The highest-ROAS category also has the highest CPA, yet remains the most efficient:**
- **Observation:** In the detailed category table, **Fitted-Waist Dress** has a **ROAS of 22.09** but also the **highest CPA at 246,033₫** among all categories. Despite the higher cost per result, its outstanding ROAS shows it's still the most profitable category per ad dollar spent.
- **Recommendation:** Accept the higher CPA for Fitted-Waist Dress since its ROAS performance more than compensates, but monitor it closely to ensure cost of goods/discounts don't pull this category's ROI negative like the system-wide trend.

---

## 🛠️ Skills & Tools Applied

| Category | Skills / Tools |
|---|---|
| **Data Modeling** | Designed a snowflake schema (multiple fact/dimension tables, resolved a many-to-many bridge table between SKU and date) |
| **Data Preparation** | Power Query (standardized product codes, processed multi-level ad-spend data) |
| **DAX (Power BI)** | Measures for Roas, Rois, CPA, CAC, CTR; `CALCULATE` + `ALLEXCEPT` technique to build measures "locked to product" regardless of other report filters |
| **Data Visualization** | Designed a 3-page report, combo charts, donut chart, drill-down matrix tables, cross-filtering slicers |
| **Business Analysis** | Analyzed marketing campaign performance tied to actual profitability (ROI), not just revenue (ROAS) |
| **Analytical Thinking** | Identified the high-ROAS/negative-ROI paradox, diagnosed high CPA through the lens of CTR |
| **Data Storytelling** | Structured findings using an Observation → Recommendation format for each dashboard page |

---

## 🔎 Final Conclusion & Recommendations

1. **High ROAS does not equal positive profit**

   Insight: System-wide ROAS reaches 7.67, yet ROI is actually negative at -21.7%.

   Recommendation: Always track ROI/ROIS alongside ROAS when evaluating ad campaign performance.

2. **Fitted-Waist Dress is the most profitable category**

   Insight: ROAS reaches 22.09, far outperforming all other categories (4.5–10.3).

   Recommendation: Increase ad budget for this category and replicate its creative/targeting approach across other categories.

3. **Low CTR is a bottleneck worth fixing**

   Insight: Overall CTR is only 0.17%, average CPA is 201.3K₫, with wide variance between campaigns.

   Recommendation: Optimize creative and targeting per campaign, learning from the lowest-CPA campaigns.

4. **Demand is heavily concentrated in sizes S/M**

   Insight: Sizes M and S make up nearly 90% of units sold; XL demand is essentially negligible.

   Recommendation: Adjust production/inventory planning to match actual size demand and avoid excess stock.

5. **Accept higher CPA for high-performing categories**

   Insight: Fitted-Waist Dress has the highest CPA but remains the best-ROAS category.

   Recommendation: Keep investing in this category, but monitor cost of goods/discounts closely to prevent its ROI from turning negative.

---

> **Note:** This README follows the same structure as the previous sample reports. Replace the image paths (`./images/...`) with actual screenshots exported from your dashboard.
