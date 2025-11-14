# Vendor Performance Analytics

## Project Overview
This repository contains a complete end-to-end vendor performance analytics project that examines procurement, sales, and inventory data to identify profitability issues, inventory inefficiencies, vendor concentration risks, and opportunities for pricing and promotional adjustments. The workflow integrates SQL for data extraction, Python for data processing and statistical analysis, and Power BI for visualization and reporting.

## Objectives
- Identify brands with low sales but high profit margins that could benefit from promotions or pricing adjustments.  
- Determine top vendors contributing to purchases and gross profit and assess vendor concentration risk.  
- Quantify the cost impact of bulk purchasing and identify unit-cost savings.  
- Assess inventory turnover to identify slow-moving stock and reduce holding costs.  
- Compare profitability profiles between top-performing and low-performing vendors and validate differences statistically.

## Data Sources
The analysis uses transactional and aggregated records with these key fields:
- VendorNumber, Brand  
- PurchasePrice, ActualPrice, Volume  
- TotalPurchaseQuantity, TotalPurchaseDollars  
- TotalSalesQuantity, TotalSalesDollars, TotalSalesPrice  
- TotalExciseTax, FreightCost  
- GrossProfit, ProfitMargin  
- StockTurnover, SalesToPurchaseRatio

Data layout:
- Raw CSV files are stored in the data/ folder.  
- vendor_sales_summary.csv contains the cleaned and aggregated summary used for reporting and dashboards.

## Exploratory Data Analysis
Key EDA steps and findings:
- Computed summary statistics (count, mean, std, min, quartiles, max) for all numeric fields.  
- Identified negative and zero-value issues:
  - Gross Profit minimum at -52,002.78 indicating some transactions were sold at a loss.  
  - Profit Margin contains negative infinity values where revenue is zero and cost > 0.  
  - Several products show zero TotalSalesQuantity, indicating unsold inventory.  
- Detected outliers and high variability:
  - PurchasePrice max = 5,681.81 vs mean ≈ 24.39; ActualPrice max = 7,499.99 vs mean ≈ 35.64.  
  - FreightCost range spans 0.09 to 257,032.07, indicating inconsistent logistics costs.  
  - StockTurnover ranges from 0 to 274.5, showing both dead stock and extremely high turnover cases.  
- Data filtering applied for reliable downstream analysis:
  - Exclude or flag records where GrossProfit ≤ 0.  
  - Exclude or flag records where ProfitMargin is non-finite or ≤ 0.  
  - Exclude SKUs with TotalSalesQuantity = 0 for profitability-focused analyses.

## Correlation Insights
Summary of correlation analysis:
- PurchasePrice vs TotalSalesDollars and GrossProfit: weak correlations (near zero), implying price alone does not strongly determine revenue or profit across the dataset.  
- TotalPurchaseQuantity vs TotalSalesQuantity: very strong positive correlation (~0.999), indicating purchases align with sales at aggregate levels.  
- ProfitMargin vs TotalSalesPrice: negative correlation (~ -0.18), suggesting increased selling price is associated with lower margins in some segments.  
- StockTurnover vs GrossProfit / ProfitMargin: weak negative correlations, indicating faster turnover does not necessarily produce higher margins.

## Research Questions & Findings

1. Brands for Promotional or Pricing Adjustments  
   - Identified 198 brands with low sales but high profit margins. These brands are candidates for targeted promotions or revised pricing strategies to increase volume without substantially eroding margin.

2. Top Vendors by Purchase Contribution  
   - The top 10 vendors account for 65.69% of total purchases, indicating significant vendor concentration risk and highlighting the need to diversify supplier relationships.

3. Impact of Bulk Purchasing on Cost Savings  
   - Bulk (large) orders show materially lower unit purchase prices (large order unit price ≈ 10.78 vs small order unit price ≈ 39.06), confirming bulk purchasing is an effective cost-reduction strategy.

4. Vendors with Low Inventory Turnover  
   - Total unsold inventory capital is estimated at approximately 2.71M. Several vendors exhibit low stock turnover and high unsold inventory values; these vendors should be targeted for inventory reduction strategies.

5. Profit Margin Comparison: Top vs Low-Performing Vendors  
   - Top vendors (by purchase contribution) show mean profit margin ≈ 31.17% (95% CI: 30.74%–31.61%).  
   - Low-performing vendors show mean profit margin ≈ 41.55% (95% CI: 40.48%–42.62%).  
   - Statistical tests reject the null hypothesis of no difference between groups, indicating distinct profitability models.

## Recommendations
- Re-evaluate pricing for low-sales, high-margin brands to stimulate volume growth without eroding profitability.  
- Diversify vendor partnerships to reduce supply risk associated with a small set of dominant vendors.  
- Negotiate and promote bulk purchase discounts to lower unit costs and preserve margins.  
- Implement targeted clearance strategies, promotions, or adjusted purchase quantities for slow-moving inventory to free working capital.  
- Optimize operational efficiency and margin improvement programs for top vendors; expand marketing and distribution support for low-volume, high-margin vendors.

## Tools Used
- SQL (SQLite or other relational DBMS) for data extraction and aggregation.  
- Python (Pandas, NumPy, SciPy, Matplotlib, Seaborn) for data cleaning, EDA, statistical testing, and plotting.  
- SQLAlchemy for ingestion of CSV data into a SQL database.  
- Jupyter Notebook for interactive analysis (Exploratory Data Analysis.ipynb).  
- Power BI (Vendor_performance.pbix) for dashboards and stakeholder reporting.

## How to Run the Analysis (Local)
1. Clone the repository to your local machine.  
2. Create and activate a Python virtual environment (Python 3.8+ recommended).  
3. Install dependencies:
   - Create a requirements.txt describing required packages, then run:
     - pip install -r requirements.txt
4. Place raw CSV files in the data/ directory.  
5. Ingest data into the database:
   - Run the ingestion script (example using SQLite):
     - python ingestion_db.py
   - Confirm the database file (for example inventory.db) is created and populated.  
6. Run EDA and analysis:
   - Open and run Exploratory Data Analysis.ipynb in Jupyter Notebook or JupyterLab to reproduce charts and tables.  
   - Execute the main analysis script for batch results:
     - python "Vendor Performance Analysis.py"
7. Generate or refresh dashboards:
   - Open Vendor_performance.pbix in Power BI Desktop and connect to vendor_sales_summary.csv or the database to view dashboards.  
8. Produce outputs:
   - vendor_sales_summary.csv contains aggregated data used in reports and dashboard visualizations.

## Repository Structure
- README.md  
- data/ (raw CSV files)  
- vendor_sales_summary.csv (aggregated summary)  
- ingestion_db.py (data ingestion)  
- get_vendor_summary.py (aggregation utilities)  
- Vendor Performance Analysis.py (main analysis script)  
- Exploratory Data Analysis.ipynb (interactive EDA)  
- Vendor_performance.pbix (Power BI report)

## Data Quality and Notes
- ProfitMargin contains non-finite values where revenue = 0; treat these records as missing or investigate root causes before using margin-based filters.  
- Validate extreme FreightCost and price values against source records to identify data entry or scaling errors.  
- Consider log-transformations, Winsorizing, or trimming for visualizations or models when distributions are heavily skewed.  
- Maintain a reproducible record of all data cleaning and filtering steps, and store filtered counts for auditability.
