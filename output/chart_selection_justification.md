# Chart Selection Justification
**Dashboard:** Executive Sales & Profitability Dashboard
**Dataset:** dashboard_sales_data.xlsx

This document justifies the chart type selection for each of the 7 dashboard visualizations, covering the business question answered, why the chart type is appropriate, the Tableau field configuration, design principles applied, and mistakes avoided.

---

## Chart 1: Sales Trend — Line Chart

### What Question It Answers
*"How has revenue trended over time, and are there identifiable seasonal patterns?"*

A trend question is fundamentally about change over a continuous time axis. The line chart is the universally accepted standard for this purpose.

### Why This Chart Type Is Appropriate
Line charts are optimal for visualizing continuous sequential data because the connecting line between data points reinforces the concept of change and direction. A bar chart of monthly sales would force viewers to mentally compare bar heights rather than follow a flowing trend. A scatter plot without connection would lose the temporal narrative entirely.

### Tableau Fields Used
- **Columns (X-axis):** `order_date` — set to continuous Month/Year (MONTH(order_date))
- **Rows (Y-axis):** `SUM(sales)` — aggregate measure
- **Color:** Optional secondary line for `SUM(profit)` to overlay profit trend
- **Label:** Add data labels only on annual summary points to avoid clutter
- **Reference Line:** Add average sales reference line to highlight above/below average months

### Design Principle Applied
**Signal-to-noise ratio:** Only the trend line and axis labels are displayed. Gridlines are minimized (light gray), and color is used sparingly. The Y-axis starts at zero to prevent visual exaggeration of the trend magnitude.

### Mistake Avoided
Starting the Y-axis above zero would make a 4.3% YoY growth look dramatically steeper than it actually is — a common manipulation that misleads leadership audiences. The Y-axis is anchored at $0 to preserve proportional honesty.

---

## Chart 2: Regional Performance — Bar Chart (or Filled Map)

### What Question It Answers
*"Which geographic regions generate the most sales and profit, and which underperform?"*

Regional performance can be shown either geographically (Map) or comparatively (Bar). The Bar chart is recommended as the primary view because exact comparison is more important than geographic context in this dataset.

### Why This Chart Type Is Appropriate
A horizontal bar chart ranks regions from highest to lowest in an instantly scannable format. A filled map (choropleth) shows geographic patterns but makes exact value comparison harder (audiences must rely on color intensity, which is perceptually imprecise). Since there are only 4 regions and the business question is "which is best/worst," a ranked bar chart communicates the answer faster.

### Tableau Fields Used
- **Rows:** `region` — sorted descending by SUM(sales)
- **Columns:** `SUM(sales)` as primary bar, `SUM(profit)` as a secondary overlapping bar or separate grouped bar
- **Color:** Dual color encoding — blue for Sales, green for Profit — to allow simultaneous comparison
- **Label:** Data labels on bar ends showing exact dollar values ($64.7M, $54.6M, etc.)
- **Size:** Uniform bar width (no size encoding needed)

### Design Principle Applied
**Pre-attentive attributes:** Color immediately separates Sales from Profit bars, allowing viewers to detect the profit-to-revenue gap pattern without reading labels. Bars are sorted by Sales (not alphabetically) to create a natural ranking hierarchy.

### Mistake Avoided
Using a pie chart for regional performance would be a critical mistake — pie charts cannot encode two measures (Sales and Profit) simultaneously, and small differences between slices (e.g., East vs. West at nearly identical revenue) are virtually impossible to perceive. The bar chart makes the $48.9M vs. $64.7M contrast immediately visible.

---

## Chart 3: Category & Sub-Category Profitability — Horizontal Bar Chart

### What Question It Answers
*"Which product categories and sub-categories drive the most profit, and which are loss-makers?"*

This is a part-to-whole ranking question that requires comparison of individual named items — ideal for a bar chart.

### Why This Chart Type Is Appropriate
A horizontal bar chart sorted by profit value reveals the ranking hierarchy instantly. The category-level grouping (Technology, Furniture, Office Supplies) can be shown via color, while sub-categories form the individual bars. This dual-level hierarchy is naturally handled by Tableau's nested dimension capability.

### Tableau Fields Used
- **Rows:** `sub_category` nested under `category`
- **Columns:** `SUM(profit)`
- **Color:** `category` — three distinct colors (e.g., blue = Technology, orange = Furniture, gray = Office Supplies) to visually group sub-categories
- **Label:** Profit value displayed on bar ends
- **Sort:** Descending by `SUM(profit)` within each category group

### Design Principle Applied
**Visual grouping by color:** Assigning a distinct color per category means viewers can immediately identify which technology sub-categories dominate and see that all of Furniture's sub-categories cluster at the low-profit end. This exploits Gestalt's Law of Similarity.

### Mistake Avoided
A stacked bar chart would be confusing here because it would aggregate sub-categories and make individual comparisons impossible. A treemap would work visually but is harder to read precise values from — the bar chart retains exact comparability.

---

## Chart 4: Customer Segment Performance — Highlight Table (Heat Map)

### What Question It Answers
*"How do the three customer segments compare across multiple performance dimensions simultaneously?"*

When comparing multiple metrics (Sales, Profit, Return Rate, Avg Discount) across a small number of categories (3 segments), a highlight table conveys more information per square inch than any single-measure chart.

### Why This Chart Type Is Appropriate
A highlight table (also called a heat map matrix) allows the viewer to scan across rows (metrics) and columns (segments) simultaneously. Color intensity encodes relative performance, making it easy to spot which segment underperforms on which dimension without reading every number. For a leadership dashboard, this multi-dimensional snapshot is more decision-useful than four separate charts.

### Tableau Fields Used
- **Columns:** `customer_segment` (Consumer, Corporate, Home Office)
- **Rows:** Metric names — SUM(sales), SUM(profit), AVG(return_flag) [as %], AVG(discount)
- **Color:** Sequential color scale (white → dark blue for positive metrics like Sales; white → dark red for risk metrics like Return Rate)
- **Label:** Actual values displayed in each cell (e.g., $74.5M, 5.7%, 15.3%)
- **Size:** Fixed cell size (highlight table format)

### Design Principle Applied
**Diverging color for risk metrics:** For return rate and discount, use a red color scale so high values (unfavorable) immediately catch the eye. For sales and profit, use a blue scale. This color-semantic alignment means viewers don't need to read the axis label to interpret good vs. bad.

### Mistake Avoided
Using three separate bar charts (one per segment) would fragment the comparison and prevent the viewer from scanning across all metrics at once. A highlight table collapses 12 data points into a single coherent view.

---

## Chart 5: Shipping Mode vs. Delivery Days — Horizontal Bar Chart

### What Question It Answers
*"How does shipping mode affect delivery speed, and which mode has the highest return rate?"*

This is a categorical comparison question — comparing a performance metric (delivery days, return rate) across a discrete set of categories (4 shipping modes).

### Why This Chart Type Is Appropriate
A horizontal bar chart with ship modes on the Y-axis and average delivery days on the X-axis ranks modes by speed in one glance. A secondary bar or reference mark can overlay return rate. The bar chart is ideal because the 4 shipping modes are discrete categories, not continuous, and viewers need to compare magnitudes (4.71 days vs. 0.40 days is a 10x difference that only a bar chart conveys proportionally).

### Tableau Fields Used
- **Rows:** `ship_mode` (sorted by AVG(delivery_days) ascending)
- **Columns:** `AVG(delivery_days)` as primary bar
- **Color:** Encode by `AVG(return_flag)` to add return rate as a color dimension (green = low returns, red = high returns)
- **Label:** Exact delivery day value and return rate % on each bar
- **Reference Line:** Average delivery days across all modes as a benchmark line

### Design Principle Applied
**Dual encoding:** Color encodes return rate while bar length encodes delivery time, allowing two questions to be answered from one chart. This follows the principle of information density — providing more insight without adding visual complexity.

### Mistake Avoided
A pie chart of delivery days per shipping mode would be meaningless — you cannot show an average in a pie chart, and the concept of "share of delivery days" has no business logic. A line chart would falsely imply these are time-series points, not discrete categories.

---

## Chart 6: Discount vs. Profit — Scatter Plot

### What Question It Answers
*"Is there a statistical relationship between discount level and profit per order, and at what discount threshold does profit turn negative?"*

This is a correlation question between two continuous numeric variables — the exact use case for a scatter plot.

### Why This Chart Type Is Appropriate
Only a scatter plot shows the joint distribution of two continuous variables across 4,200 individual data points simultaneously. It reveals not just the direction and strength of the relationship (negative correlation, r = -0.27) but also the distribution of outliers — high-discount, high-profit anomalies or low-discount, low-profit surprises that no aggregate chart would surface.

### Tableau Fields Used
- **Columns (X-axis):** `discount` (continuous, 0–1 scale displayed as 0%–100%)
- **Rows (Y-axis):** `SUM(profit)` or `AVG(profit)` at order level
- **Color:** `customer_segment` — to detect if any segment is particularly discount-sensitive
- **Size:** Optional — encode `quantity` to show high-volume orders
- **Label:** Trend line with R² value displayed (using Tableau's built-in analytics trend line)
- **Reference Line:** Add a Y=0 horizontal line to visually mark the profit breakeven point

### Design Principle Applied
**Trend line annotation:** Adding a linear regression trend line (available in Tableau's Analytics pane) turns a raw scatter plot into a statistically meaningful business tool. The slope of the line quantifies the profit cost of each percentage point of discount, making the business case for discount reform concrete.

### Mistake Avoided
A bar chart of average profit by discount bucket (while informative in the insights document) hides the within-bucket variance. A scatter plot shows that even at the 30%+ discount tier, there are some profitable orders — meaning the problem is not the discount itself but how it is applied. This nuance drives better policy decisions.

---

## Chart 7: Return Analysis by Category (and Region) — Bar Chart

### What Question It Answers
*"Which product categories and regions have the highest return rates, and how does this vary?"*

This is a categorical ranking question on a rate metric, best served by a ranked bar chart.

### Why This Chart Type Is Appropriate
A horizontal bar chart sorted by return rate immediately surfaces which categories need attention. The use of rate (%) rather than absolute count ensures that categories with different order volumes are compared fairly. A map could show regional variation geographically, but since the main story is category-level (Furniture at 7.7% vs. Technology at 3.0%), the bar chart keeps the most important comparison front and center.

### Tableau Fields Used
- **Rows:** `category` (and optionally `region` as a secondary view)
- **Columns:** `AVG(return_flag)` displayed as percentage (multiply by 100 or format as %)
- **Color:** Traffic-light encoding — green for return rate below 4% (below average), amber for 4–6%, red for above 6% (Furniture)
- **Label:** Exact return rate % on each bar
- **Reference Line:** Average return rate (4.55%) as a vertical benchmark line

### Design Principle Applied
**Traffic-light color coding:** Using green/amber/red is universally understood by business audiences and immediately communicates risk level without requiring the viewer to interpret the scale. Red = action required, green = acceptable performance.

### Mistake Avoided
Using absolute return counts (not rates) would be misleading — Furniture might appear to have fewer returns than Technology simply because it has fewer orders. Normalizing to a return rate ensures the comparison is apples-to-apples regardless of order volume differences between categories.

---

*Document prepared as part of the Executive Tableau Dashboard assignment*
*All chart recommendations are based on analysis of dashboard_sales_data.xlsx (4,200 orders, FY2024–FY2025)*
