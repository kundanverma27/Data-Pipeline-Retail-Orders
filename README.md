# End-to-End Retail Data Analytics Project Using Python and SQL Server

This project demonstrates a complete data analytics workflow involving data extraction, cleaning, transformation, loading into SQL Server, and analysis using SQL queries. The dataset used is the **Retail Orders** dataset downloaded from Kaggle via the Kaggle API.

---

## Project Overview

- **Data Extraction:** Download the dataset from Kaggle using the Kaggle API in Python.
- **Data Cleaning & Processing:** Load the data into pandas, handle missing values, rename columns for consistency, and create new calculated columns such as discount, sale price, and profit.
- **Data Loading:** Load the cleaned data into a SQL Server database with optimized data types.
- **Data Analysis:** Perform SQL queries to extract insights such as top revenue-generating products, regional sales rankings, monthly sales growth, and profit growth by subcategory.

---

## Features

- Automated download of dataset using Kaggle API.
- Data cleaning including null value handling and column renaming.
- Creation of new columns for discount, sale price, and profit calculations.
- Conversion of date columns to proper datetime format.
- Loading data into SQL Server with manual table creation for optimized schema.
- SQL queries demonstrating advanced analytics and window functions.

---

## Prerequisites

- Python 3.x
- Pandas library
- Kaggle API installed and configured (`kaggle.json` placed in `.kaggle` folder)
- SQL Server instance with access credentials
- SQLAlchemy library for database connection
- `pyodbc` or appropriate ODBC driver for SQL Server

---

## Setup Instructions

1. **Kaggle API Setup**  
   - Create a Kaggle account and generate an API token (`kaggle.json`) from your account settings.  
   - Place the `kaggle.json` file in the `.kaggle` folder inside your home directory.

2. **Download Dataset**  
   - Use the Kaggle API command in Python to download the "retail orders" dataset as a ZIP file.  
   - Extract the `orders.csv` file using Python’s `zipfile` library.

3. **Data Cleaning and Processing**  
   - Load `orders.csv` into a pandas DataFrame.  
   - Handle missing values by treating specific strings as null.  
   - Rename columns to lowercase and replace spaces with underscores.  
   - Create new columns:  
     - `discount` = `list_price * discount_percent / 100`  
     - `sale_price` = `list_price - discount`  
     - `profit` = `sale_price - cost_price`  
   - Drop unnecessary columns (`list_price`, `discount_percent`, `cost_price`).  
   - Convert `order_date` to datetime format.

4. **Load Data into SQL Server**  
   - Create a connection to SQL Server using SQLAlchemy.  
   - Manually create the target table with optimized data types.  
   - Load the cleaned DataFrame into SQL Server using `to_sql` with `if_exists='append'`.

5. **Data Analysis with SQL**  
   - Run SQL queries to answer business questions such as:  
     - Top 10 highest revenue-generating products.  
     - Top 5 highest selling products per region.  
     - Month-over-month sales growth comparison for 2022 and 2023.  
     - Highest sales month per category.  
     - Subcategory with highest profit growth in 2023 compared to 2022.

---

## Sample SQL Queries

- **Top 10 Revenue Products**  
  ```sql
  SELECT TOP 10 product_id, SUM(sale_price) AS sales
  FROM df_orders
  GROUP BY product_id
  ORDER BY sales DESC;
Top 5 Products per Region
WITH RankedSales AS (
  SELECT reason, product_id, SUM(sale_price) AS sales,
         ROW_NUMBER() OVER (PARTITION BY reason ORDER BY SUM(sale_price) DESC) AS rn
  FROM df_orders
  GROUP BY reason, product_id
)
SELECT reason, product_id, sales
FROM RankedSales
WHERE rn <= 5;
