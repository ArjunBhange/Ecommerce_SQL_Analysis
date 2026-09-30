# 🛒 E-Commerce Sales Analysis (SQL)

A beginner-friendly **SQL database project** that analyzes an e-commerce business: customers, products, orders, sales, revenue and customer purchasing behavior.

It demonstrates **DDL, DML, DQL, Joins, Aggregations, Subqueries, CTEs, Window Functions, CASE statements, Indexes, Views and Query Optimization**.

> **Developed by:** Arjun Bhange

---

## 📑 Contents

1. [What this project does](#-what-this-project-does)
2. [Database design](#-database-design)
3. [Tables explained](#-tables-explained)
4. [Concepts covered](#-concepts-covered)
5. [Business questions answered](#-business-questions-answered)
6. [How the analysis works](#-how-the-analysis-works)
7. [How to run](#-how-to-run)
8. [Sample queries](#-sample-queries)
9. [Key findings](#-key-findings)
10. [Project structure](#-project-structure)
11. [Possible extensions](#-possible-extensions)

---

## 🎯 What this project does

- Stores customer, product, order and order-item information
- Calculates total and monthly revenue
- Identifies best-selling products
- Analyzes revenue by product category
- Identifies repeat and inactive customers
- Finds products that have never been sold
- Calculates average order value
- Performs customer spending segmentation
- Analyzes month-over-month revenue growth
- Calculates running revenue totals
- Identifies repeat purchases within 30 days
- Uses indexes and `EXPLAIN` for query optimization

**Business rule:**

| Rule | Implementation |
|---|---|
| Cancelled orders are excluded from revenue | `WHERE status <> 'Cancelled'` |
| Historical selling price is preserved | `order_items.price` |
| Revenue = quantity × selling price | Aggregation queries |
| Customer spending uses valid orders | CTE + aggregation |

---

## 🗺️ Database design

```text
customers
    |
    | 1 : N
    |
orders
    |
    | 1 : N
    |
order_items
    |
    | N : 1
    |
products
```

### Schema

```text
customers
├── customer_id (PK)
├── name
├── city
└── signup_date

products
├── product_id (PK)
├── name
├── category
└── price

orders
├── order_id (PK)
├── customer_id (FK)
├── order_date
└── status

order_items
├── order_id (PK, FK)
├── product_id (PK, FK)
├── quantity
└── price
```

---

## 🧱 Tables explained

| Table | Purpose | Key columns |
|---|---|---|
| `customers` | Stores customer information | `customer_id`, `name`, `city`, `signup_date` |
| `products` | Stores product information | `product_id`, `name`, `category`, `price` |
| `orders` | Stores customer orders | `order_id`, `customer_id`, `order_date`, `status` |
| `order_items` | Stores products included in each order | `order_id`, `product_id`, `quantity`, `price` |

---

## 🧠 Concepts covered

| Concept | What is used |
|---|---|
| **DDL** | `CREATE TABLE`, constraints, indexes, views |
| **DML** | `INSERT`, `UPDATE`, `DELETE` |
| **DQL** | `SELECT`, `WHERE`, `ORDER BY`, `GROUP BY`, `HAVING` |
| **Joins** | `INNER JOIN`, `LEFT JOIN`, multi-table joins |
| **Aggregation** | `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` |
| **Filtering** | `WHERE`, `HAVING`, `DISTINCT` |
| **CASE** | Status and customer classification |
| **Subqueries** | Nested business-analysis queries |
| **CTEs** | Reusable intermediate result sets |
| **Window Functions** | `RANK`, `DENSE_RANK`, `LAG`, running `SUM` |
| **Ranking** | Product and revenue rankings |
| **Time Analysis** | Monthly revenue and month-over-month growth |
| **Customer Analysis** | Repeat customers and spending segments |
| **Constraints** | `PRIMARY KEY`, `FOREIGN KEY`, `CHECK` |
| **Indexes** | Frequently used join/filter columns |
| **Views** | Reusable analytical queries |
| **Optimization** | `EXPLAIN` and indexing |

---

## 📊 Business questions answered

The project answers **14 real-world business questions**.

### Basic Analysis

1. What is the total revenue?
2. What is the monthly revenue?
3. Which products sell the most?
4. How many customers belong to each city?

### Intermediate Analysis

5. Which product categories generate the most revenue?
6. Which customers have placed multiple orders?
7. Which products have never been sold?
8. Which customers have never placed an order?

### Advanced Analysis

9. How are products ranked based on revenue?
10. How does revenue change month over month?
11. What is the running total of revenue?
12. What is the average order value?
13. How can customers be segmented based on spending?
14. Which customers made another purchase within 30 days?

---

## 🔄 How the analysis works

### 1. Data preparation

The project creates the required tables and inserts sample e-commerce data.

```text
Customers
    ↓
Orders
    ↓
Order Items
    ↓
Products
```

### 2. Revenue calculation

```text
Revenue = Quantity × Selling Price
```

Cancelled orders are excluded from revenue calculations.

### 3. Customer analysis

Customer orders are grouped to identify:

- Total spending
- Number of orders
- Average order value
- Repeat customers
- Spending segments

### 4. Advanced analysis

Window functions are used for:

- Product ranking
- Month-over-month comparison
- Running revenue totals
- Customer purchase analysis

---

## ▶️ How to run

1. Open your SQL database tool.
2. Create or select a database for the project.
3. Open `ecommerce_sales_analysis.sql`.
4. Execute the complete SQL script.
5. Run the individual analysis queries to view the results.

> ⚠️ If you execute the complete script again, tables and sample data may be recreated depending on the commands included in the SQL file.

---

## 🔍 Sample queries

### Total Revenue

```sql
SELECT SUM(oi.quantity * oi.price) AS total_revenue
FROM orders o
JOIN order_items oi
    ON o.order_id = oi.order_id
WHERE o.status <> 'Cancelled';
```

### Revenue by Category

```sql
SELECT
    p.category,
    SUM(oi.quantity * oi.price) AS revenue
FROM products p
JOIN order_items oi
    ON p.product_id = oi.product_id
JOIN orders o
    ON oi.order_id = o.order_id
WHERE o.status <> 'Cancelled'
GROUP BY p.category
ORDER BY revenue DESC;
```

### Best-Selling Products

```sql
SELECT
    p.name,
    SUM(oi.quantity) AS units_sold
FROM products p
JOIN order_items oi
    ON p.product_id = oi.product_id
JOIN orders o
    ON oi.order_id = o.order_id
WHERE o.status <> 'Cancelled'
GROUP BY p.name
ORDER BY units_sold DESC;
```

### Product Ranking

```sql
SELECT
    p.name,
    SUM(oi.quantity * oi.price) AS revenue,
    RANK() OVER (
        ORDER BY SUM(oi.quantity * oi.price) DESC
    ) AS revenue_rank
FROM products p
JOIN order_items oi
    ON p.product_id = oi.product_id
JOIN orders o
    ON oi.order_id = o.order_id
WHERE o.status <> 'Cancelled'
GROUP BY p.name;
```

### Month-over-Month Revenue

```sql
WITH monthly_revenue AS (
    SELECT
        SUBSTR(order_date, 1, 7) AS month,
        SUM(oi.quantity * oi.price) AS revenue
    FROM orders o
    JOIN order_items oi
        ON o.order_id = oi.order_id
    WHERE o.status <> 'Cancelled'
    GROUP BY SUBSTR(order_date, 1, 7)
)
SELECT
    month,
    revenue,
    LAG(revenue) OVER (ORDER BY month) AS previous_month_revenue
FROM monthly_revenue
ORDER BY month;
```

---

## 📈 Key findings

Based on the sample data:

- **Total revenue:** ₹64,343
- **Valid orders:** 19
- **Average order value:** Approximately ₹3,386
- **Top category:** Fashion — ₹30,776
- **Fashion contribution:** 47.8% of total revenue
- **Electronics contribution:** 28.6%
- **Home contribution:** 14.3%
- **Sports contribution:** 9.3%
- **Best-selling product:** Cotton T-Shirt — 16 units
- **Second best-selling product:** USB-C Cable — 12 units
- **Never sold:** Dumbbell Set
- **Highest-spending customer:** Aarav Sharma — ₹21,178 across 6 orders
- **Customer with no orders:** Divya Menon
- **Peak month:** October 2025 — ₹15,887

> November 2025 contains only one order, so its lower revenue represents a partial month in this sample dataset.

---

## ⚡ Optimization

The project demonstrates:

- Indexes on frequently used join columns
- `EXPLAIN` for query execution analysis
- Views for reusable analysis
- CTEs to organize complex queries

---

## 📁 Project structure

```text
ecommerce-sales-analysis/
├── README.md
├── ecommerce_sales_analysis.sql
└── Ecommerce_SQL_Analysis.pptx
```

### Files

| File | Description |
|---|---|
| `README.md` | Project documentation |
| `ecommerce_sales_analysis.sql` | Database creation, sample data and analysis queries |
| `Ecommerce_SQL_Analysis.pptx` | Project presentation |

---

## 🚀 Possible extensions

- Load a larger and more realistic dataset
- Add customer cohort analysis
- Perform customer retention analysis
- Add more sales metrics
- Connect SQL views to Power BI or Tableau
- Create an interactive sales dashboard
- Add additional product and customer analysis

---

## 👤 Author

**Arjun Bhange**

If you found this project useful, consider giving it a ⭐ on GitHub!
