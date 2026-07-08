# E-commerce Supply Chain Dashboard (Power BI)

A supply chain management dashboard for e-commerce data, with 3 pages: **Overview**, **Seller Performance**, and **Logistics**. Data is preprocessed in Python before being loaded into Power BI.

## 1. Data Preprocessing

`DataPreprocessing.ipynb` is used to clean and merge data from 5 source tables into a single flat table, avoiding redundant/duplicated data when building the relationship model in Power BI.

**Input data sources:**

- `df_Customers.csv` — customer information
- `df_Orders.csv` — order information
- `df_OrderItems.csv` — line-item details per order
- `df_Payments.csv` — payment information
- `df_Products.csv` — product information

**Main processing steps:**

1. Load the 5 CSV files into separate pandas DataFrames.
2. Sequentially merge the tables on their shared keys (`order_id`, `customer_id`, `product_id`) using an `inner join`:
   - `OrderItems` + `Orders` → on `order_id`
   - result + `Customers` → on `customer_id`
   - result + `Products` → on `product_id`
   - result + `Payments` → on `order_id`
3. Export the result as a single consolidated table (`powerbi_ecommerce_dashboard.csv`) — this is the source table imported directly into Power BI, which helps:
   - Avoid building multiple complex relationships between tables in Power BI.
   - Prevent row duplication caused by many-to-many relationships.
   - Speed up DAX calculations since everything runs on one flat table.
4. Run data quality checks: identify rows with missing `order_id`, cross-check against `payment_value` to catch anomalies.
5. Do a quick sanity check on monthly order trends (2017 vs 2018) to validate the data before building the dashboard.

## 2. How the Power BI Dashboard Was Built

The dashboard has 3 main pages, sharing a common filter panel on the left sidebar (Year, Month, State, Product Category, Payment Type, etc.) so viewers can slice the data as needed.

### Page 1 — Overview

Gives a high-level view of overall business performance:

- **KPI cards**: Total Orders, Total Payment Value, Total Delivered Order, Total Products, Cancelled Orders — each card also shows Target/Achieved (%) to compare actuals against targets.
- **Top 3 Product Categories**: a donut chart showing the share of the best-selling product categories.
- **Total Revenue by Month**: an area chart comparing Orders/Revenue between the selected year and the previous year.
- **Cancel Rate vs Late Delivery Rate**: a monthly bar chart comparing cancellation rate against late delivery rate.
- **Late Delivery Rate by State** and **Cancel Rate by State**: rankings of the top 8 states with the highest late delivery / cancellation rates.

### Page 2 — Seller Performance

Analyzes individual seller performance and risk level:

- **Seller Revenue Pareto Chart**: ranks sellers by revenue with a cumulative % line, applying the 80/20 Pareto principle to identify the sellers driving most of the revenue.
- **Seller Risk Map**: a scatter/bubble chart (top 40 sellers by order count) plotting order volume against late delivery rate, classifying sellers into Low/Medium/High risk, with bubble size representing revenue.
- **Seller Price Positioning vs Reliability**: compares late delivery rate across 3 price tiers (Budget, Mid-range, Premium) to assess the relationship between price positioning and delivery reliability.
- **Seller Category Diversification**: a combo bar + line chart showing the number of sellers by how many product categories they sell in, compared against average revenue per seller.

### Page 3 — Logistics

Focuses on delivery time and shipping quality:

- **KPI cards**: Avg Total Lead Time, Avg Delivery Time, Avg Processing Time, On-Time Delivery Rate, Avg Late Days (Late Orders Only).
- **Where Does Lead Time Go? — Processing vs Delivery**: a monthly bar chart breaking lead time into processing time and delivery time to identify which stage consumes the most time.
- **Lead Time Distribution**: a histogram of lead time by day range, with percentile markers (P50, P90, P95) to surface outliers/long-tail cases.
- **Late Delivery Rate — Geographic View**: a map visual showing late delivery rate by region.
- **Top 8 States — Delay Rate & Shipping Cost**: a detail table comparing order count, delay rate, average shipping cost, and deviation from the overall average, by state.


## 3. Tech Stack

- **Python (pandas)**: data cleaning, merging, and quality checks.
- **Power BI**: data visualization, KPI cards, DAX measures, and interactive dashboard.
