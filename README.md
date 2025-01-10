# Ecommerce-Analysis

## Table of Contents


## Overview

This project analyzes the operations of Olist, the largest department store in Brazilian marketplaces. Olist connects small businesses across Brazil to e-commerce platforms, providing logistics support and a streamlined selling process. The dataset spans 2017 and 2018, providing valuable insights into customer behavior, product performance, and operational efficiency.

## Dataset Details

The analysis was conducted using the following datasets:

**Geolocation Dataset:** Contains geographical information for sellers and customers.

**Order Items Dataset:** Includes details like shipping dates, prices, and product IDs.

**Order Reviews Dataset:** Captures customer reviews for purchased products.

**Orders Dataset:** A central table linking details across datasets.

**Customers Dataset:** Contains customer demographic and transactional information.

**Order Payments Dataset:** Includes payment method details and transaction amounts.

**Products Dataset:** Contains product details like category, dimensions, and weights.

**Sellers Dataset:** Provides information on all registered sellers.

## Data Analysis

I worked on eCommerce data analysis using MySQL to explore various metrics.

**Weekday vs Weekend payment statistics**
```sql
SELECT 
    CASE 
        WHEN WEEKDAY(order_purchase_timestamp) IN (5, 6) THEN 'Weekend'
        ELSE 'Weekday'
    END AS day_type,
   round( SUM(payment_value),2) AS total_payment,
   concat(ROUND(SUM(payment_value) * 100 / SUM(SUM(payment_value)) OVER (), 2), '%') AS percentage_of_total
FROM 
    olist_order_payments_dataset p
INNER JOIN 
    olist_orders_dataset o 
ON 
    o.order_id = p.order_id
GROUP BY 
    day_type;
```
**Number of orders with a review score of 5 and payment via credit card**
```sql
SELECT 
    COUNT(DISTINCT r.order_id) AS total_reviews,
    COUNT(DISTINCT CASE 
                     WHEN r.review_score = 5 AND p.payment_type = 'credit_card' 
                     THEN r.order_id 
                   END) AS filtered_reviews
FROM 
    olist_order_reviews_dataset r
INNER JOIN 
    olist_orders_dataset o 
ON 
    r.order_id = o.order_id
LEFT JOIN 
    olist_order_payments_dataset p 
ON 
    o.order_id = p.order_id;
```
**Average delivery days for pet shop orders**
```sql
SELECT 
    AVG(DATEDIFF(order_delivered_customer_date, order_purchase_timestamp)) AS avg_days
FROM 
    olist_orders_dataset o
INNER JOIN 
    olist_order_items_dataset ot 
ON 
    o.order_id = ot.order_id
LEFT JOIN 
    olist_products_dataset pr
ON 
    ot.product_id = pr.product_id
WHERE 
    pr.product_category_name = 'pet_shop';
```
**Average prices and payment values from customers in São Paulo**
```sql
select round(avg(price),2) as avg_price, round(avg(payment_value),2) as avg_payment_value
from olist_order_items_dataset ot inner join olist_orders_dataset o on ot.order_id=o.order_id
right join olist_order_payments_dataset p on o.order_id=p.order_id
left join olist_customers_dataset c on o.customer_id=c.customer_id where customer_city='sao paulo';
```
**Relationship between Shipping days vs Review scores**
```sql
SELECT 
    r.review_score,
    AVG(DATEDIFF(o.order_delivered_customer_date, o.order_purchase_timestamp)) AS avg_shipping_days 
FROM 
    olist_orders_dataset o
INNER JOIN 
    olist_order_reviews_dataset r 
ON 
    o.order_id = r.order_id
WHERE 
    o.order_delivered_customer_date IS NOT NULL
GROUP BY 
    r.review_score
ORDER BY 
    r.review_score;
```

## Challenges Encountered

- Handling null values in datasets.
- Establishing relationships between datasets.
- Ensuring data accuracy during cleaning and transformation.
- Writing complex joins while ensuring the relationships between these tables are correct can be tricky.

## Key Findings
Product Categories and Pricing:

High-priced items generally received lower review scores.
Some categories outperform others in terms of both sales volume and customer satisfaction.

- Products with higher review scores had better sales performance.
- Delays in delivery negatively impacted review scores.
- Customer demand is higher in urban regions, aligning with efficient logistics coverage.
- Most transactions were completed via credit cards, with a preference for installment payments.
- Sellers with more diversified product portfolios had better sales metrics.

## Recommendations
- We recommend optimizing pricing for underperforming categories while maintaining competitiveness in high-demand segments. Offering discounts or bundle deals can further incentivize purchases in lower-rated categories.
- Leveraging customer feedback to improve product features and resolve common complaints will help boost satisfaction. Additionally, engaging with customers who leave poor reviews can foster loyalty and improve brand perception.
- Focusing on reducing delivery delays and prioritizing faster deliveries in high-demand urban areas will enhance customer satisfaction and retention.
- Using geolocation insights, tailored marketing efforts can unlock untapped regional potential. Seasonal promotions of highly rated products will further drive sales growth.

*Thank you for taking the time to explore my project. Your interest and feedback are truly appreciated!* 😊
