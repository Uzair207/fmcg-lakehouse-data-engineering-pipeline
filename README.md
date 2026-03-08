# FMCG Lakehouse Data Engineering Pipeline

## Overview

This project simulates a real-world enterprise data integration challenge.

A global FMCG company **Atlikon** acquires a fast-growing nutrition startup **SportsBar**. The startup operates on a fragmented data platform and delivers raw CSV files through AWS S3, while the parent company already uses a modern **Databricks Lakehouse architecture**.

To integrate the newly acquired company's data into the enterprise analytics platform, a scalable and governed data pipeline is required.

This project implements an **end-to-end Lakehouse data engineering solution** that ingests raw startup data, transforms it through Bronze, Silver, and Gold layers, and merges it with the parent company’s enterprise data model for unified analytics.

---

# Business Scenario

After acquiring SportsBar, Atlikon faced several data challenges:

• Raw CSV files delivered through AWS S3  
• Inconsistent schemas across systems  
• Data quality issues in customer, product, and pricing datasets  
• Daily operational data incompatible with the parent company's monthly reporting  
• Lack of a unified analytics layer

The goal was to build a **scalable, governed data pipeline** capable of integrating child company data into the parent organization’s enterprise data warehouse.

---

# Solution Architecture

The solution uses a **Lakehouse Medallion Architecture** implemented in Databricks.

```
S3 Raw Data
      ↓
Databricks Lakehouse
      ↓
Bronze Layer (Raw ingestion)
      ↓
Silver Layer (Data cleaning & standardization)
      ↓
Gold Layer (Analytics-ready tables)
      ↓
Parent Enterprise Data Model
      ↓
BI Dashboards
```

Architecture Diagram:

![Architecture](architecture/project_architecture.png)

---

# Data Pipeline Design

## Bronze Layer

Raw CSV files from the SportsBar S3 bucket are ingested into Databricks Bronze tables.

Key features:

• Raw schema preservation  
• Metadata tracking (file name, file size, ingestion timestamp)  
• Delta Lake tables with change data feed

Example ingestion step:

- CSV files loaded from S3
- Metadata columns added
- Data written into Bronze Delta tables

---

## Silver Layer

The Silver layer performs **data cleansing and transformation**.

Key transformations implemented:

• Duplicate removal  
• Data type standardization  
• Date normalization from multiple formats  
• Data quality fixes (city typos, product spelling errors)  
• Customer and product standardization  
• Enrichment with business attributes

Example:

Customer data is standardized to align with the parent company model.

```
customer = customer_name + "-" + city
market = "India"
platform = "Sports Bar"
channel = "Acquisition"
```

These transformations ensure compatibility with the parent organization's schema.

---

## Gold Layer

The Gold layer contains **analytics-ready dimensional and fact tables**.

Gold tables include:

- `dim_customers`
- `dim_products`
- `dim_gross_price`
- `dim_date`
- `fact_orders`

These tables are built from the cleaned Silver data.

Example fact structure:

```
date
product_code
customer_code
sold_quantity
```

---

# Parent Company Data Integration

Child company data is merged into the parent enterprise tables using Delta Lake MERGE operations.

Example:

• Customer dimension merge  
• Product dimension merge  
• Pricing dimension merge  
• Fact table integration

Daily child company transactions are aggregated to **monthly grain** to match the parent organization's reporting model.

---

# Incremental Data Processing

The pipeline supports **incremental ingestion**.

Steps:

1. New CSV files arrive in the S3 landing directory
2. Files are ingested into Bronze
3. Staging tables isolate new records
4. Silver and Gold layers update using Delta MERGE
5. Monthly aggregates are recalculated only for impacted months

This approach ensures:

• Scalable processing  
• Reduced compute cost  
• Efficient enterprise data updates

---

# Analytical Data Model

A denormalized analytical view powers the BI dashboard.

Example analytical view:

```
vw_fact_orders_enriched
```

This view joins:

- Fact orders
- Customer dimension
- Product dimension
- Pricing dimension
- Date dimension

and calculates:

```
total_amount_inr = sold_quantity * price_inr
```

---

# Dashboard Insights

The Gold layer powers an interactive analytics dashboard with insights such as:

• Total Revenue  
• Total Quantity Sold  
• Top Selling Products  
• Revenue by Channel  
• Monthly Revenue Trend  
• Sales Heatmap by Channel and Month  

These visualizations enable business users to understand:

• seasonal sales patterns  
• top performing products  
• distribution channel performance  

---

# Tech Stack

Databricks  
PySpark  
Delta Lake  
AWS S3  
SQL  
Lakehouse Architecture  
Medallion Data Architecture

---

# Repository Structure

```
architecture/
    project_architecture.png

code/
    setup_catalog.py
    utilities.py
    dim_date_table_creation.py

    customers_pipeline.py
    products_pipeline.py
    pricing_pipeline.py

    full_load_fact.py
    incremental_load_fact.py

dashboard/
    sales_dashboard.png

dataset/
    sample_data

README.md
```

---

# Author

Uzair Abid Lakhiar