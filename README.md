# B2B Wholesale Sales & Margin Intelligence Dashboard

**Project Type:** Independent Business Intelligence & Analytics Portfolio Project  
**Author:** Rennyson Rana  
**Tools Used:** Power BI Desktop, Microsoft Excel, Power Query, DAX  

---

## 1. Project Overview & Problem Statement
Wholesale and distribution businesses frequently struggle with visibility across fragmented sales, inventory, and margin data. When sales figures are evaluated only by top-line revenue, businesses risk over-allocating sales effort and warehouse capacity to high-volume products that yield low profit margins.

In this project, I built an end-to-end business intelligence solution to analyze wholesale transaction data, identify customer purchasing patterns, and isolate high-yield product categories. The goal was to replace manual weekly spreadsheet reporting with an automated data model and an interactive Power BI dashboard that allows leadership to make data-driven pricing, inventory, and account management decisions.

---

## 2. Business Requirements & Analytics Scope

### Key Business Questions Addressed
1. What is the real gross profit margin yield across each distinct product line?
2. Which customer tiers and regional markets drive the highest sales concentration?
3. How do order volumes trend month-over-month to support proactive inventory forecasting?
4. How can non-technical stakeholders dynamically filter accounts and regions without breaking formulas?

### Deliverables
* **Automated Data Model:** Ingest and structure raw transactional records into an optimized Star Schema.
* **Core KPI Metrics:** Executive cards for Total Revenue, Gross Profit ($), Profit Margin (%), and Total Orders.
* **Interactive Visualizations:** Slicers for geographic regions (Midwest, East Coast, West Coast, South) and date ranges.
* **Data Integrity:** Standardized customer account keys and normalized product catalogs.

---

## 3. Data Architecture & Modeling

I structured the dataset (~2,500 historical order records) into an industry-standard **Star Schema** to optimize query performance and eliminate circular relationship dependencies:

* **Fact Table (`Fact_Sales`):** Transactional order line items capturing `OrderID`, `OrderDate`, `CustomerID`, `ProductID`, `Quantity`, `Revenue`, `TotalCost`, and `GrossMargin`.
* **Dimension Table (`Dim_Customers`):** Customer ID, business account name, geographic region, and customer classification (Wholesale Distributor, Retail Shop, Chain Store).
* **Dimension Table (`Dim_Products`):** Product SKU, description, category line, unit cost, and wholesale list price.
* **Dimension Table (`Dim_Date`):** Dedicated calendar table spanning 2025–2026 supporting time-intelligence functions.

### Relational Schema:
* `Dim_Customers[CustomerID]` (1) ───► `Fact_Sales[CustomerID]` (*)
* `Dim_Products[ProductID]` (1) ───► `Fact_Sales[ProductID]` (*)
* `Dim_Date[Date]` (1) ───► `Fact_Sales[OrderDate]` (*)

---

## 4. Custom DAX Measures

To power dynamic aggregations and comparative analytics, I authored custom DAX formulas in Power BI:

```dax
// 1. Total Revenue across filtered context
Total Revenue = SUM(Fact_Sales[Revenue])

// 2. Gross Margin ($)
Gross Margin = SUM(Fact_Sales[GrossMargin])

// 3. Profit Margin Percentage
Margin % = DIVIDE([Gross Margin], [Total Revenue], 0)

// 4. Year-over-Year Revenue Growth (Time-Intelligence)
YoY Revenue Growth = 
VAR CurrentSales = [Total Revenue]
VAR PriorYearSales = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR(Dim_Date[Date]))
RETURN
IF(
    ISBLANK(PriorYearSales), 
    BLANK(), 
    DIVIDE(CurrentSales - PriorYearSales, PriorYearSales, 0)
)
