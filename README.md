# 🛍️ Customer_Behavior_Analysis
📊 End-to-end retail analytics pipeline tracking customer spend using Python, MySQL, and Power BI.

An end-to-end data analytics project focused on identifying retail purchasing patterns, customer spending tendencies, and core demographics. This project covers the full data lifecycle: from automated ETL and feature engineering in **Python**, through structured database validation via **MySQL**, to executive-level business intelligence in **Power BI**.

---

## 📌 Overview
The objective of this project is to transform raw retail transaction data into actionable business strategies for retail stakeholders. By deep-diving into a dataset of 3,900 customer touchpoints, the analysis successfully isolates revenue drivers across demographics, determines discount program efficiency, benchmarks shipping metrics, and models customer loyalty buckets.

---

## 📊 Dataset
* **📄 Source File:** `customer_shopping_behavior.csv` (Simulated retail transaction data)
* **📐 Dataset Dimensions:** 3,900 records × 18 attributes
* **🔑 Core Metrics:** Demographics (*Age, Gender, Location*), Transaction Details (*Item, Category, Purchase Amount, Season*), and Behavioral Markers (*Review Rating, Subscription Status, Shipping Type, Previous Purchases*).

---

## 🛠️ Tools & Technologies
* **🐍 Data Prep & Engineering:** Python 3, Pandas, NumPy
* **🗄️ Relational Database:** MySQL (Connected via PyMySQL & SQLAlchemy)
* **📉 Visualization & BI:** Power BI Desktop
* **💻 Development Environment:** Jupyter Notebook

---

## ⚙️ Project Steps & Architecture

### 1. 🧹 Data Cleaning & Feature Engineering (Python)
* **🔄 Schema Standardization:** Lowercased all column headers and swapped spaces with underscores for flawless, error-free database migration.
* **🏷️ Feature Binning:** Categorized numerical customer ages into dynamic demographic brackets (`Young Adult`, `Adult`, `Middle-aged`, and `Senior`) utilizing quantile cuts.
* **🔢 Frequency Normalization:** Structured text-based purchase intervals into a standardized, numeric day-scale column for cleaner modeling.
* **🚚 Automated Database Injection:** Engineered a programmatic pipeline using `SQLAlchemy` to automatically push the structured DataFrame straight into a local MySQL instance.

### 2. 🔍 Advanced Relational Queries (MySQL)
To challenge and validate business performance metrics, a series of complex relational queries were constructed. Key SQL design techniques implemented include:
* **📐 Aggregations & Grouping:** Extracted revenue streams filtered across behavioral dimensions like gender, location, and subscription statuses.
* **🎯 Subqueries:** Isolated premium, high-value consumer profiles (e.g., identifying shoppers utilizing discount codes who still maintained cart totals well above the historical average).
* **⚡ Window Functions (`ROW_NUMBER()`):** Built partitioned data matrices to cleanly surface the top 3 best-selling products across each unique product category.
* **🧩 Common Table Expressions (CTEs):** Formulated multi-tier customer lifetime segments (`New`, `Returning`, `Loyal`) built directly out of transaction history data.

---

## 💻 SQL Analysis Showcase
Here are a few production-ready queries utilized to extract complex insights out of the database instance:

### 🥉 Top 3 Best-Selling Products by Category
```SQL
WITH item_counts AS (
    SELECT
        category,
        item_purchased,
        COUNT(customer_id) AS total_orders,
        ROW_NUMBER() OVER(PARTITION BY category ORDER BY COUNT(customer_id) DESC) AS item_rank
    FROM customer
    GROUP BY category, item_purchased
)
SELECT
    item_rank, category, item_purchased, total_orders
FROM item_counts
WHERE item_rank <= 3;
```
### 👥 Customer Loyalty Cohort Segmentation
```SQL
WITH customer_type AS (
    SELECT
        customer_id,
        previous_purchases,
        CASE
            WHEN previous_purchases = 1 THEN 'New'
            WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
            WHEN previous_purchases > 10 THEN 'Loyal'
        END AS customer_segment
    FROM customer
)
SELECT
    customer_segment,
    COUNT(*) AS customer_segment_count
FROM customer_type
GROUP BY customer_segment;
```

## 🖥️ Power BI Dashboard

![Dashboard Page 1](/Images/Dashboard_Page1.png)

An interactive corporate dashboard built to track key retail performance vectors. Core components include:
* **📈 Executive Summary Cards:** Dynamic tracking for Total Revenue, Total Order Counts, Average Order Value (AOV), and Average Satisfaction Ratings.
* **👥 Demographic Breakdown Plots:** Comparative visual distributions highlighting spending differences across gender and targeted age brackets.
* **🎛️ Cross-Filtering Slicers:** Interactive filters enabling the immediate isolation of subscriber behaviors, seasonal changes, and shipping variations.

## 🚀 Key Business Insights & Results

* **🧑‍🤝‍🧑 Revenue Drivers:** Shoppers identifying as Male represented the dominant share of cumulative gross revenue within this retail dataset.
* **🔄 Retention Strengths:** The business maintains excellent retention health; the largest overall customer cluster falls into the "Loyal" cohort (customers documenting over 10 prior purchases).
* **🚚 Shipping Incentives:** Transaction analysis revealed that average cart totals remain statistically identical between Standard and Express shipping types, indicating premium speed variations do not necessarily encourage higher cart spend.
* **🎟️ Discount Elasticity:** Accurately mapped product groups that heavily depend on coupon activations to trigger sale conversions, providing clear pricing leverage for inventory management.
