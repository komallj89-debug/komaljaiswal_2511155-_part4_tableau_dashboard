## Business Problem Summary

This project addresses a core business analytics challenge: transforming raw transactional sales data into a clear, actionable executive dashboard that enables senior leadership to monitor performance, identify risks, and make data-driven decisions.

Specifically, the dashboard answers the following business questions:
1. How is total revenue trending over time, and are there seasonal patterns?
2. Which geographic regions are most and least profitable?
3. Which product categories and sub-categories drive profit vs. loss?
4. How do customer segments compare in revenue, profit, and return behavior?
5. What is the relationship between discount level and profitability?
6. How do different shipping modes affect delivery speed and return rates?
7. Where are returns most concentrated, and what is the business cost?

---

## Dataset Description

**File:** `data/dashboard_sales_data.xlsx`
**Records:** 4,200 orders
**Time Period:** January 2024 – December 2025
**Dimensions:** 20 columns

| Field | Type | Description |
|-------|------|-------------|
| order_id | String | Unique order identifier |
| order_date | Date | Date the order was placed |
| ship_date | Date | Date the order was shipped |
| customer_id | String | Unique customer identifier |
| customer_segment | String | Consumer, Corporate, or Home Office |
| region | String | East, West, North, or South |
| state | String | US state |
| city | String | City name |
| category | String | Furniture, Office Supplies, or Technology |
| sub_category | String | Product sub-category (13 values) |
| product_name | String | Full product name |
| ship_mode | String | Same Day, First Class, Second Class, Standard Class |
| sales | Float | Order revenue in USD |
| quantity | Integer | Number of units ordered |
| discount | Float | Discount fraction applied (0.0–1.0) |
| profit | Float | Order profit in USD (can be negative) |
| return_flag | Integer | 1 = returned, 0 = not returned |
| delivery_days | Integer | Days between order and delivery |
| customer_rating | Float | Post-purchase satisfaction score (1.0–5.0) |
| campaign_channel | String | Organic, Social, or Referral |

**Key Summary Statistics:**
- Total Sales: $217,017,651.92
- Total Profit: $33,306,312.84
- Overall Profit Margin: 15.35%
- Average Order Value (AOV): $51,670.87
- Overall Return Rate: 4.55%

---

## Tableau Workbook Description

**File:** `tableau/executive_dashboard.twbx`
**Tool:** Tableau Public / Tableau Desktop

The workbook contains 7 individual worksheets and 1 consolidated executive dashboard:

| Sheet | Visualization | Purpose |
|-------|--------------|---------|
| Sheet 1 | Line Chart | Monthly sales and profit trend over time |
| Sheet 2 | Bar Chart | Sales and profit by region |
| Sheet 3 | Horizontal Bar Chart | Profit by category and sub-category |
| Sheet 4 | Highlight Table | Multi-metric segment performance comparison |
| Sheet 5 | Bar Chart | Average delivery days and return rate by ship mode |
| Sheet 6 | Scatter Plot | Discount level vs. profit per order |
| Sheet 7 | Bar Chart | Return rate by category and region |
| Dashboard | Combined View | All 7 sheets with KPIs, filters, and narrative |

---

## Calculated Fields

The following 5 calculated fields were created in Tableau to support the dashboard. Use these exact formulas in the Tableau Calculated Field editor.

### 1. Profit Margin
```
[Profit] / [Sales]
```
*Format as Percentage (1 decimal place). Measures the percentage of each sales dollar retained as profit. Use ISNULL guard if needed:*
```
IF [Sales] != 0 THEN [Profit] / [Sales] ELSE 0 END
```

### 2. Cost
```
[Sales] - [Profit]
```
*Represents the cost of goods and operations per order. Useful for understanding cost structure without a dedicated COGS field.*

### 3. Average Order Value (AOV)
```
SUM([Sales]) / COUNTD([Order ID])
```
*Use as a Table Calculation or LOD expression. Returns the average revenue per distinct order. Alternatively as a Level of Detail (LOD) expression:*
```
{ FIXED : SUM([Sales]) / COUNTD([Order ID]) }
```

### 4. Return Rate
```
SUM([Return Flag]) / COUNT([Order ID])
```
*Format as Percentage. Calculates what fraction of orders in the current view were returned. The `return_flag` field encodes 1 = returned, 0 = not returned, making SUM equivalent to a count of returns.*

### 5. Shipping Delay Bucket
```
IF [Delivery Days] <= 2 THEN "0–2 Days (Fast)"
ELSEIF [Delivery Days] <= 5 THEN "3–5 Days (Normal)"
ELSE "6+ Days (Delayed)"
END
```
*Creates a human-readable delivery speed category. Use this in bar charts and filters to allow leadership to quickly identify delayed shipments without interpreting raw numbers.*

---

## Dashboard Components

The executive dashboard (`executive_dashboard.twbx`) contains the following components arranged on a single canvas:

**Top KPI Bar (3 Metric Tiles):**
- **Total Sales** — SUM([Sales]) formatted as $M
- **Total Profit** — SUM([Profit]) formatted as $M
- **Overall Profit Margin** — [Profit Margin] calculated field, formatted as %

**Visualizations (7 Charts):**
1. Sales Trend Line Chart (full width, top section)
2. Regional Performance Bar Chart (left column)
3. Category Profitability Horizontal Bar (right column)
4. Customer Segment Highlight Table (center)
5. Shipping Mode Bar Chart (bottom left)
6. Discount vs. Profit Scatter Plot (bottom center)
7. Return Rate Bar Chart (bottom right)

**Dashboard Title:** "Executive Sales & Profitability Dashboard — FY2024–FY2025"

---

## Filters Used

The following interactive filters are applied to the dashboard. All filters are configured to apply to **All Worksheets Using This Data Source** (set via the filter dropdown > Apply to Worksheets > All Using This Data Source).

| Filter | Field | Type | Purpose |
|--------|-------|------|---------|
| Date Range | order_date | Relative Date / Range | Limit view to a time period |
| Region | region | Multi-select dropdown | Filter to specific region(s) |
| Customer Segment | customer_segment | Multi-select dropdown | Isolate Consumer, Corporate, or Home Office |
| Category | category | Multi-select dropdown | Filter to Furniture, Office Supplies, or Technology |
| Ship Mode | ship_mode | Multi-select dropdown | Analyze specific shipping methods |

---

## Key Business Insights Summary

1. **Sales grew 4.3% YoY** — from $106.2M in 2024 to $110.8M in 2025, with seasonal dips in April–May each year.
2. **South region leads revenue** at $64.7M; the East region is most margin-efficient at 13.25% avg order margin.
3. **Technology drives 84.2% of total profit** ($28.04M of $33.3M) at an 18.2% margin, versus Furniture's weak 6.9%.
4. **Furniture return rate is 7.7%** — more than double Technology's 3.0% — compounding already-thin margins.
5. **Discounts above 30% generate negative profit** (avg -$1,601 per order), revealing a critical pricing discipline gap.
6. **Same Day shipping has the lowest return rate** (2.5%); First Class has the highest (5.1%) despite premium speed.
7. **Home Office segment has the highest return rate** (5.7%) despite being the top-revenue segment at $74.5M.
8. **AOV is $51,670.87** — with profit margin largely determined by discount level and category, not order size alone.

---

## Dashboard Story Summary

The dashboard tells the story of a high-growth technology-led business that is performing well at the top line but faces structural challenges in category concentration, discount discipline, and Furniture economics. Technology is the undisputed profit engine, the South leads geographically, and all customer segments deliver comparable margins. However, 84% profit concentration in one category is a strategic risk, Furniture's 6.9% margin makes it a drag rather than a contributor, and orders with 30%+ discounts are loss-making. The recommended priorities are: implementing a discount cap, auditing Furniture returns, and launching Q2 promotional campaigns to address the April–May revenue dip.

Full narrative available in: `outputs/dashboard_story.md`

---

## Assumptions

1. The `return_flag` field encodes returns as 1 (returned) and 0 (not returned). No partial returns are modeled.
2. Profit figures are taken as provided in the dataset; cost of goods sold (COGS) is inferred as Sales − Profit and not independently verified.
3. `delivery_days` is calculated as calendar days between `order_date` and `ship_date`. This may not reflect actual customer receipt date if transit time post-shipment is not captured.
4. All monetary values are in USD with no currency conversion applied.
5. Discount is expressed as a decimal fraction (0.0 to 1.0), representing 0% to 100% discount.
6. The dataset is treated as complete and representative; no data quality issues (duplicates, missing values) have been identified in the initial analysis.
7. `customer_rating` (1.0–5.0) is available in the dataset but not included as a primary dashboard KPI due to its completeness assumptions. It can be added as an additional chart if required.

---

## Screenshots

*Screenshots should be taken after the Tableau dashboard is built and saved. Place all images in the `screenshots/` folder.*

| File | Description |
|------|-------------|
| `screenshots/full_dashboard.png` | Full screenshot of the completed executive dashboard canvas |
| `screenshots/sales_trend_view.png` | Close-up of Sheet 1 — Sales Trend Line Chart |
| `screenshots/regional_performance_view.png` | Close-up of Sheet 2 — Regional Performance Bar Chart |
| `screenshots/category_profitability_view.png` | Close-up of Sheet 3 — Category Profitability Chart |
| `screenshots/filter_interaction_view.png` | Screenshot showing a filter being applied and the dashboard updating |

*[Add actual screenshots here after Tableau dashboard is completed]*

---

## Repository Structure

```
studentname_studentid_part4_tableau_dashboard/
├── README.md                          ← This file
├── data/
│   └── dashboard_sales_data.xlsx      ← Source data file
├── tableau/
│   └── executive_dashboard.twbx       ← Packaged Tableau workbook
├── outputs/
│   ├── business_insights.md           ← 8 data-backed business insights
│   ├── dashboard_story.md             ← Executive narrative and recommendations
│   └── chart_selection_justification.md ← Justification for all 7 chart choices
└── screenshots/
    ├── full_dashboard.png
    ├── sales_trend_view.png
    ├── regional_performance_view.png
    ├── category_profitability_view.png
    └── filter_interaction_view.png
```

---

*Part 4 — Business Analytics Assignment | Tableau Executive Dashboard*
