# 🛒 Instacart Sales Analysis with SQL

This project involves creating a relational database schema to analyze product sales, customer orders, and departmental performance for an e-commerce grocery platform like Instacart. The analysis focuses on revenue, profit, order volume, and time-based trends.

---

## 🧱 Database Schema

```sql
-- Aisle Table
CREATE TABLE aisle(
    aisle_id INT PRIMARY KEY,
    aisle VARCHAR(50)
);

-- Department Table
CREATE TABLE departments(
    department_id INT PRIMARY KEY,
    department VARCHAR(50)
);

-- Product Table
CREATE TABLE product(
    product_id INT PRIMARY KEY,
    product_name VARCHAR(255),
    aisle_id INT REFERENCES aisle(aisle_id),
    department_id INT REFERENCES departments(department_id),
    unit_cost DECIMAL(4,2),
    unit_price DECIMAL(4,2)
);

-- Orders Table
CREATE TABLE orders(
    order_id INT PRIMARY KEY,
    user_id INT,
    product_id INT REFERENCES product(product_id),
    quantity INT,
    order_date DATE,
    order_down INT,
    order_hour_of_the_day INT,
    days_since_prior_order INT,
    order_status VARCHAR(50)
);
```

---

## 📊 Business Insights & Analysis

### 🔝 Top-Selling Products by Revenue
```sql
SELECT
    product.product_name,
    SUM(orders.quantity) AS total_quantity,
    SUM(orders.quantity * product.unit_price) AS total_revenue
FROM orders
JOIN product ON product.product_id = orders.product_id
GROUP BY product.product_id, product.product_name
ORDER BY total_revenue DESC;
```

### 🛒 Aisles with Highest Sales Volume
```sql
SELECT
    aisle.aisle,
    SUM(orders.quantity) AS total_sales_volume
FROM orders
JOIN product ON product.product_id = orders.product_id
JOIN aisle ON aisle.aisle_id = product.aisle_id
GROUP BY aisle.aisle
ORDER BY total_sales_volume DESC;
```

### 💰 Top 3 Departments by Profit in 2019
```sql
SELECT
    departments.department_id,
    departments.department,
    SUM((product.unit_price - product.unit_cost) * orders.quantity) AS total_profit
FROM orders
JOIN product ON product.product_id = orders.product_id
JOIN departments ON departments.department_id = product.department_id
WHERE EXTRACT(YEAR FROM orders.order_date) = 2019
GROUP BY departments.department_id, departments.department
ORDER BY total_profit DESC
LIMIT 3;
```

### 🐣 Most Profitable Product During Easter 2022
```sql
SELECT
    p.product_name,
    SUM((p.unit_price - p.unit_cost) * o.quantity) AS total_profit
FROM product p
JOIN orders o ON p.product_id = o.product_id
WHERE o.order_date = '2022-04-17'
GROUP BY p.product_name
ORDER BY total_profit DESC
LIMIT 1;
```

### 📈 Most Profitable Year
```sql
SELECT
    EXTRACT(YEAR FROM orders.order_date) AS profit_year,
    SUM((product.unit_price - product.unit_cost) * orders.quantity) AS total_profit
FROM orders
JOIN product ON orders.product_id = product.product_id
GROUP BY profit_year
ORDER BY total_profit DESC
LIMIT 1;
```

### 🎄 Top 5 Products by Revenue (Xmas 2022 – Jan 2, 2023)
```sql
SELECT
    product.product_name,
    SUM(orders.quantity * product.unit_price) AS total_revenue,
    SUM((product.unit_price - product.unit_cost) * orders.quantity) AS total_profit
FROM orders
JOIN product ON orders.product_id = product.product_id
WHERE orders.order_date BETWEEN '2022-12-25' AND '2023-01-02'
GROUP BY product.product_name
ORDER BY total_revenue DESC
LIMIT 5;
```

### 🕐 Peak Ordering Time During COVID Period
```sql
SELECT
    order_hour_of_the_day,
    SUM(orders.quantity) AS order_volume
FROM orders
WHERE order_date BETWEEN '2020-04-11' AND '2023-04-07'
GROUP BY order_hour_of_the_day
ORDER BY order_volume DESC
LIMIT 1;
```

### 💵 Revenue from Q2 & Q3 of 2016
```sql
SELECT
    SUM(orders.quantity * product.unit_price) AS total_revenue
FROM orders
JOIN product ON product.product_id = orders.product_id
WHERE EXTRACT(YEAR FROM order_date) = 2016
AND EXTRACT(QUARTER FROM order_date) IN (2, 3);
```

### 🌙 Most Bought Products at Night (2020–2022)
```sql
SELECT
    product.product_name,
    COUNT(orders.order_id) AS order_count
FROM orders
JOIN product ON orders.product_id = product.product_id
WHERE EXTRACT(YEAR FROM orders.order_date) BETWEEN 2020 AND 2022
AND orders.order_hour_of_the_day BETWEEN 7 AND 12
GROUP BY product.product_name
ORDER BY order_count DESC
LIMIT 3;
```

### 🧃 Revenue from Juice Products
```sql
SELECT
    product.product_name,
    SUM(orders.quantity * product.unit_price) AS total_revenue
FROM orders
JOIN product ON product.product_id = orders.product_id
WHERE product.product_name LIKE '%juice%'
GROUP BY product.product_name;
```

---

## 🛠️ Tools Used

- PostgreSQL / SQL
- Analytical queries (JOINs, GROUP BY, FILTERs, DATE functions)
- Retail Sales Metrics

---

## 📌 Outcome

This project showcases a comprehensive SQL-driven approach to analyzing retail order data, identifying high-performing products and departments, detecting seasonal trends, and maximizing profit insights.

