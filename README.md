Apple Global Sales — Data Engineering & Analytics Project

An end-to-end data pipeline built for portfolio purposes, covering data extraction and cleaning in Python, data warehouse design in SQL Server, and an interactive business intelligence dashboard in Power BI.
Project Overview

This project follows a structured analytics engineering pipeline using real-world Apple global sales data spanning 2022–2024. The goal was to transform raw transactional data into a well-modelled data warehouse and produce a dashboard that answers concrete business questions.

The pipeline covers four stages:

    Data cleaning and feature engineering in Python
    Star schema design and loading into SQL Server
    Business queries written in T-SQL
    Interactive dashboard built in Power BI

Dataset

Source: Apple Global Sales Dataset (Kaggle)
Raw file: apple_global_sales_dataset.csv
Rows: 11,500 transactions | Columns: 27
Field	Description
sale_id	Unique transaction identifier
sale_date	Date of transaction (2022–2024)
product_name, category	Apple product and category (iPhone, Mac, iPad, etc.)
country, region, city	Geographic location of sale
units_sold, revenue_usd	Volume and revenue metrics
sales_channel	Apple Store, Online, Carrier, Reseller, etc.
customer_segment	Individual, Business, Education, Government
return_status	Kept, Returned, or Exchanged
Tech Stack
Stage	Tool
Data cleaning & engineering	Python (pandas, numpy) — Jupyter Notebook
Data warehouse	SQL Server (SSMS 22)
Python to SQL Server	sqlalchemy + pyodbc
Business intelligence	Power BI Desktop



Step 1 — Data Cleaning and Feature Engineering

Notebook: notebooks/01_cleaning_and_loading.ipynb
Null handling

Three columns contained missing values:

    storage — 4,804 nulls filled with "Unknown"
    previous_device_os — 8,056 nulls filled with "Unknown"
    customer_rating — 3,360 nulls filled with column median

Text standardisation

All categorical text columns were stripped of whitespace and converted to title case to ensure consistent grouping in the warehouse.
Feature engineering

Three columns were added to support richer analysis:

    revenue_after_discount — units sold multiplied by discounted price
    is_returned — binary flag (1 if return_status is Returned)
    is_high_value — binary flag (1 if revenue_usd >= 1,000)
    day_of_week and week_number — extracted from sale_date for time analysis

Step 2 — Star Schema Design

The cleaned data was modelled into a star schema with one fact table and four dimension tables.

fact_sales
├── product_id      → dim_product (product_name, category, storage, color)
├── location_id     → dim_location (country, region, city, currency, fx_rate)
├── customer_id     → dim_customer (segment, age_group, previous_os)
└── date_id         → dim_date (sale_date, year, quarter, month, day, week)

Table	Rows	Description
fact_sales	11,500	One row per transaction
dim_product	~300	Unique product/storage/color combinations
dim_location	519	Unique country/region/city combinations
dim_customer	140	Unique customer segment combinations
dim_date	1,096	One row per calendar date in range

Tables were loaded into SQL Server using sqlalchemy with Windows Authentication. Primary keys and foreign key constraints were added in SSMS after loading.
Step 3 — Business Questions Answered

The following questions were addressed using T-SQL queries and visualised in Power BI:

    Which product categories generate the most revenue?
    Which sales channel performs best by region?
    What is the monthly and quarterly revenue trend over 2022–2024?
    Which customer segment has the highest average order value?
    What is the return rate by product category and channel?
    Which countries contribute the most to total revenue?
    How does discount percentage affect revenue and return rate?
    What are the top 10 products by units sold and by revenue?

Step 4 — Power BI Dashboard

The dashboard connects directly to SQL Server using the native Power BI connector and is structured across four pages:

    Overview — KPI cards and high-level revenue and volume metrics
    By Geography — revenue and units by region, country, and city
    By Product — top products, category breakdown, return rates
    By Customer — segment and age group analysis, channel performance

Navigation between pages is handled using Power BI bookmarks and button actions.

Key Findings

    iPhone drives the largest share of revenue globally, with the Pro Max line being the highest-revenue SKU. The Apple Store and Online channels consistently outperform third-party resellers on average order value. Return rates are highest in the Accessories category. Business and Education segments show notably higher average transaction values than Individual customers. 

Author
Fatma Mohammed Mattar
Built as a portfolio project demonstrating end-to-end data engineering skills across Python, SQL Server, and Power BI.
