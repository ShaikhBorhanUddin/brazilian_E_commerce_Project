# Q1: Display total sales revenue per month 

This question is significant for business intelligence in the Olist project because it provides a clear view of how sales revenue evolves over time, enabling stakeholders to monitor performance trends, identify seasonal patterns, and assess the overall financial health of the platform. By analyzing monthly revenue, the business can make informed decisions on marketing strategies, inventory planning, and growth forecasting, ensuring that operations align with periods of high and low demand to optimize profitability.

## Solution
```SQL
SELECT 
    DATE_TRUNC('month', order_purchase_timestamp) AS month,
    SUM(oi.price + oi.freight_value) AS total_revenue
FROM orders o
JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY month
ORDER BY month;
```
This SQL query calculates the total monthly sales revenue by aggregating the sum of product prices and freight values from the `order_items` table, joined with the `orders` table using the `order_id`. The `DATE_TRUNC('month', order_purchase_timestamp)` function extracts the month from each order's purchase timestamp, allowing the revenue data to be grouped by month. By summing `price + freight_value`, it captures the complete revenue generated from both product sales and shipping fees. The results are grouped by month and ordered chronologically, producing a time-series dataset that highlights how revenue trends over time.

## Output:
|month              |total_revenue|
|-------------------|-------------|
|2016-09-01 00:00:00|354.75       |
|2016-10-01 00:00:00|56808.84     |
|2016-12-01 00:00:00|19.62        |
|2017-01-01 00:00:00|137188.49    |
|2017-02-01 00:00:00|286280.62    |
|2017-03-01 00:00:00|432048.59    |
|2017-04-01 00:00:00|412422.24    |
|2017-05-01 00:00:00|586190.95    |
|2017-06-01 00:00:00|502963.04    |
|2017-07-01 00:00:00|584971.62    |
|2017-08-01 00:00:00|668204.60    |
|2017-09-01 00:00:00|720398.91    |
|2017-10-01 00:00:00|769312.37    |
|2017-11-01 00:00:00|1179143.77   |
|2017-12-01 00:00:00|863547.23    |
|2018-01-01 00:00:00|1107301.89   |
|2018-02-01 00:00:00|986908.96    |
|2018-03-01 00:00:00|1155126.82   |
|2018-04-01 00:00:00|1159698.04   |
|2018-05-01 00:00:00|1149781.82   |
|2018-06-01 00:00:00|1022677.11   |
|2018-07-01 00:00:00|1058728.03   |
|2018-08-01 00:00:00|1003308.47   |
|2018-09-01 00:00:00|166.46       |

The SQL query output shows a month-by-month breakdown of total revenue generated by Olist from September 2016 to September 2018. Revenue started very low in late 2016, with a significant increase beginning in early 2017. From January 2017 onward, there is a consistent upward trend, peaking notably in November 2017 with over 1.17 million in revenue—likely due to seasonal shopping events. Revenue remained strong throughout 2018, consistently exceeding one million per month until August. The final month, September 2018, shows an unusually low value, likely indicating incomplete data. Overall, the data reflects strong growth and seasonal surges in the platform's sales performance.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet101.png?raw=true)

# Q2: Show top 5 most selling product categories

This question is significant in the context of business intelligence for the Olist project because it helps identify which product categories contribute the most to sales volume, providing valuable insight into customer demand and market trends. By knowing the top-selling categories, Olist can optimize inventory management, prioritize partnerships with suppliers in high-performing segments, and tailor marketing efforts to promote popular or complementary products. This data-driven approach enhances strategic planning, supports revenue growth, and ensures the platform aligns its offerings with consumer preferences.

## Solution
```SQL
SELECT 
    pct.product_category_name_english,
    COUNT(oi.product_id) AS total_sales
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
JOIN product_category_name_translation pct ON p.product_category_name = pct.product_category_name
GROUP BY pct.product_category_name_english
ORDER BY total_sales DESC
LIMIT 5;
```
This SQL query retrieves the top 5 best-selling product categories in terms of total items sold. It joins the `order_items`, `products`, and `product_category_name_translation` tables to match each sold product with its translated English category name. By counting the number of product IDs in each category (`COUNT(oi.product_id)`), the query determines the sales volume for each category. It then groups the data by category name, orders the results in descending order of total sales, and limits the output to the top 5. This helps identify the most popular product categories, which is crucial for inventory and marketing strategies.

## Output
|product_category_name_english|total_sales|
|-----------------------------|-----------|
|bed_bath_table               |11115      |
|health_beauty                |9670       |
|sports_leisure               |8641       |
|furniture_decor              |8334       |
|computers_accessories        |7827       |

The query results show that the top-selling product category is **bed_bath_table** with **11,115** sales, followed by **health_beauty** (**9,670**), **sports_leisure** (**8,641**), **furniture_decor** (**8,334**), and **computers_accessories** (**7,827**). These categories represent the highest volume of products sold, indicating strong consumer demand and offering valuable insights for Olist to optimize stock levels, marketing focus, and supplier relationships in these areas.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%202205.png?raw=true)

# Q3: What is the average delivery time by state?

This question helps uncover regional differences in delivery performance by calculating the average delivery time (in days) for orders delivered to each Brazilian state. For a company like Olist, understanding how delivery times vary geographically is critical for evaluating logistics efficiency, identifying bottlenecks in the supply chain, and improving customer satisfaction. It also enables better planning with delivery partners and helps set realistic expectations for customers based on their location.

## Solution
```SQL
SELECT 
    c.customer_state,
    AVG(DATE_PART('day', o.order_delivered_customer_date - o.order_purchase_timestamp)) AS avg_delivery_days
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE o.order_delivered_customer_date IS NOT NULL
GROUP BY c.customer_state
ORDER BY avg_delivery_days;
```
This SQL query calculates the **average delivery time (in days)** for orders delivered to each Brazilian state by subtracting the purchase timestamp from the actual delivery date. It joins the `orders` table with the `customers` table on the `customer_id` field to associate each order with a customer’s state. It filters out any orders that do not have a recorded delivery date (`order_delivered_customer_date IS NOT NULL`) to ensure accuracy in the delivery time calculation. The result is grouped by each state (`customer_state`) and sorted in ascending order of average delivery time, helping Olist assess which states have faster or slower deliveries and optimize logistics accordingly.

## Output
|customer_state|avg_delivery_days   |
|--------------|--------------------|
|SP            |8.298061489072724   |
|PR            |11.52671135486492   |
|MG            |11.543813298106562  |
|DF            |12.509134615384616  |
|SC            |14.479560191711306  |
|RS            |14.819236526946108  |
|RJ            |14.84918643244556   |
|GO            |15.15074092999489   |
|MS            |15.191155492154065  |
|ES            |15.331829573934836  |
|TO            |17.226277372262775  |
|MT            |17.59367945823928   |
|PE            |17.965473948524796  |
|RN            |18.824894514767934  |
|BA            |18.86640049140049   |
|RO            |18.91358024691358   |
|PI            |18.993697478991596  |
|PB            |19.95357833655706   |
|AC            |20.6375             |
|CE            |20.817826426896012  |
|SE            |21.029850746268657  |
|MA            |21.11715481171548   |
|PA            |23.316067653276956  |
|AL            |24.04030226700252   |
|AM            |25.986206896551725  |
|AP            |26.73134328358209   |
|RR            |28.975609756097562  |

The data reveals that the average delivery time varies significantly across Brazilian states, with São Paulo (SP) having the fastest average delivery at approximately 8.3 days, while Roraima (RR) experiences the longest at nearly 29 days. Southeastern and southern states like SP, PR, and MG generally have faster delivery times, likely due to better infrastructure and proximity to distribution centers. In contrast, northern and northeastern states such as RR, AP, and AM face longer delivery times, highlighting potential logistical challenges in these regions. This insight can guide Olist in improving delivery efficiency and customer satisfaction in underperforming areas.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%203%20(1).png?raw=true)

# Q4: Display top 10 sellers by revenue

The question aims to identify which sellers contribute most to the overall sales performance on the Olist platform. This insight is crucial in the context of business intelligence as it helps Olist recognize high-performing vendors who drive substantial revenue. Understanding top sellers allows the business to maintain strong relationships with them, design incentive programs, and analyze their practices for replicable success across other vendors. It also helps in identifying potential risks if too much revenue depends on a few sellers and informs inventory, partnership, and strategic planning decisions.

## Solution
```SQL
SELECT 
    s.seller_id,
    SUM(oi.price + oi.freight_value) AS total_revenue
FROM order_items oi
JOIN sellers s ON oi.seller_id = s.seller_id
GROUP BY s.seller_id
ORDER BY total_revenue DESC
LIMIT 10;
```
This SQL query retrieves the top 10 sellers based on total revenue generated from their sales on the platform. It works by selecting the `seller_id` from the `order_items` table and calculating the `total_revenue` for each seller by summing both the product `price` and the `freight_value` (shipping fee). The `GROUP BY oi.seller_id` clause ensures that the revenue is aggregated for each individual seller. The results are then sorted in descending order of revenue using `ORDER BY total_revenue DESC`, ensuring that the sellers generating the most revenue appear first. Finally, the `LIMIT 10` clause restricts the output to only the top 10 highest-earning sellers.

## Output
|seller_id                         |total_revenue|
|----------------------------------|-------------|
|4869f7a5dfa277a7dca6462dcf3b52b2  |249640.70    |
|7c67e1448b00f6e969d365cea6b010ab  |239536.44    |
|53243585a1d6dc2643021fd1853d8905  |235856.68    |
|4a3ca9315b744ce9f8e9374361493884  |235539.96    |
|fa1c13f2614d7b5c4749cbc52fecda94  |204084.73    |
|da8622b14eb17ae2831f4ac5b9dab84a  |185192.32    |
|7e93a43ef30c4f03f38b393420bc753a  |182754.05    |
|1025f0e2d44d7041d6cf58b6550e0bfa  |172860.69    |
|7a67c85e85bb2ce8582c35f2203ad736  |162648.38    |
|955fee9216a65b617aa5c0531780ce60  |160602.68    |

The summary of the query output shows the top 10 sellers on the platform ranked by total revenue earned. The highest-earning seller generated approximately $249,641, while the tenth highest earned around $160,603. These sellers stand out for their strong sales performance, combining product prices and shipping fees to contribute significantly to the platform’s total revenue.

# Q5: Display most preferred payment methods

In the context of business intelligence for the Olist project, identifying the most preferred payment methods is crucial for understanding customer purchasing behavior and optimizing the payment experience. This insight helps the business determine which payment options are most popular, ensuring those methods are prioritized for customer convenience. It also informs strategic decisions related to partnerships with payment providers, identifies any barriers in the checkout process, and supports the development of targeted marketing campaigns or promotions tied to specific payment types. Ultimately, knowing the preferred payment methods enhances customer satisfaction and can lead to increased conversion rates and revenue.

## Solution
```SQL
SELECT 
    payment_type,
    COUNT(*) AS total_transactions,
    SUM(payment_value) AS total_value
FROM order_payments
GROUP BY payment_type
ORDER BY total_value DESC;
```
This SQL query analyzes customer payment behavior by aggregating transaction data from the `order_payments` table. It selects the type of payment (`payment_type`), counts how many transactions were made using each type (`total_transactions`), and calculates the total monetary value of those payments (`total_value`). The data is grouped by `payment_type` to aggregate values for each unique method (e.g., credit card, boleto). Finally, the results are ordered in descending order of `total_value`, showing which payment methods contributed the most revenue. This helps identify the most economically significant payment options used on the Olist platform.

## Output
|payment_type|total_transactions|total_value|
|------------|------------------|-----------|
|credit_card |76795             |12542084.19|
|boleto      |19784             |2869361.27 |
|voucher     |5775              |379436.87  |
|debit_card  |1529              |217989.79  |
|not_defined |3                 |0.00       |

The SQL output shows that credit cards are the most preferred and widely used payment method on the Olist platform, accounting for 76,795 transactions and generating over 12.5 million in total payment value. Boleto follows with nearly 20,000 transactions and approximately 2.87 million in value. Vouchers and debit cards were used far less frequently, contributing relatively minor amounts to the total revenue. The "not_defined" category appeared only three times and recorded no monetary value, indicating either data entry issues or unclassified transactions.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%202.png?raw=true)
###
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%202%20(1).png?raw=true)

# Q6: Show customer repeat purchase rate

In the context of the Olist project, analyzing the **customer repeat purchase rate** is crucial for understanding customer loyalty and retention. This metric helps the business identify how many customers return to make additional purchases after their first transaction. A high repeat purchase rate indicates customer satisfaction, effective marketing, and strong brand trust, while a low rate may suggest issues with product quality, customer experience, or delivery logistics. For a marketplace like Olist, where long-term profitability heavily depends on recurring customers rather than one-time buyers, this insight can directly influence strategies related to customer relationship management, personalized promotions, and lifecycle marketing.

## Solution
```SQL
SELECT
    COUNT(DISTINCT customer_id) AS total_customers,
    COUNT(DISTINCT CASE WHEN customer_order_count > 1 THEN customer_id END) AS repeat_customers,
    ROUND(100.0 * COUNT(DISTINCT CASE WHEN customer_order_count > 1 THEN customer_id END) / COUNT(DISTINCT customer_id), 2) AS repeat_purchase_rate_percentage
FROM (
    SELECT customer_id, COUNT(order_id) AS customer_order_count
    FROM orders
    GROUP BY customer_id
) subquery;
```
This SQL query calculates the **repeat purchase rate** of customers in the Olist dataset. It starts with a subquery that groups orders by each `customer_id` and counts how many orders each customer has made, producing a table of `customer_id` with their corresponding `customer_order_count`. The outer query then counts the total number of unique customers (`total_customers`) and separately counts how many of those customers have placed more than one order (`repeat_customers`). Finally, it calculates the `repeat purchase rate percentage` by dividing the number of repeat customers by the total number of customers, multiplying by 100, and rounding the result to two decimal places. This metric reflects how many customers return for another purchase, which is a key indicator of customer satisfaction and loyalty.

## Output
|total_customers|repeat_customers|repeat_purchase_rate_percentage|
|---------------|----------------|-------------------------------|
|99441          |0               |0.00                           |

The chart indicates that out of **99,441 total customers**, **none (0 customers)** have made more than one purchase, resulting in a **0.00% repeat purchase rate**. This suggests that **all customers in the dataset made only a single order**. From a business intelligence perspective for the Olist project, this is a critical insight, as it highlights a lack of customer retention. It may imply that customers are not finding enough value to return, or that Olist's marketing and loyalty strategies are not effectively encouraging repeat purchases. Addressing this issue could involve improving customer experience, post-purchase engagement, or incentives for return shopping.

# Q7: Display lowest 10 average review scores per seller

In the context of the Olist project, **Question 7 aims to identify the bottom 10 sellers based on average customer review scores**, which can reveal underperforming sellers that may be affecting overall customer satisfaction and brand reputation. By spotlighting these sellers, Olist can take corrective actions—such as seller training, stricter onboarding standards, or even reconsidering seller partnerships—to improve quality assurance. From a business intelligence standpoint, monitoring seller performance via customer feedback is essential to maintaining a high-quality marketplace experience and reducing churn due to negative user experiences.

## Solution
```SQL
SELECT 
    oi.seller_id,
    ROUND(AVG(orw.review_score), 2) AS average_review_score
FROM order_items oi
JOIN order_reviews orw ON oi.order_id = orw.order_id
GROUP BY oi.seller_id
ORDER BY average_review_score ASC
LIMIT 10;
```
This SQL query retrieves the bottom 10 sellers based on their average customer review scores. It joins the `order_items` table (`oi`) with the `order_reviews` table (`orw`) using the `order_id` to connect items sold with their respective customer reviews. For each `seller_id`, it calculates the average `review_score` and rounds the result to two decimal places using the `ROUND()` function. The query groups the data by seller and then sorts the sellers in ascending order of their average review scores—bringing the lowest-rated sellers to the top. Finally, the `LIMIT 10` clause restricts the output to only the 10 sellers with the lowest average scores, making it easy to identify potentially problematic vendors.

## Output
|seller_id                         |average_review_score|
|----------------------------------|--------------------|
|cecd97bc34ed8330bd4cd15713eda670  |1.00                |
|d1a5cc844736958c11b8efab9a2b4c87  |1.00                |
|cf281cf8b7affbdfe751b29cc85580e1  |1.00                |
|60ad151920c4f6f3ebbe8cfdf6166779  |1.00                |
|c200c73f9d7e5a08ca439d6a0803da7c  |1.00                |
|f9eda05b67bef472deaddbba84aca289  |1.00                |
|9591fc341b1bfb7ef561e2968ec6e011  |1.00                |
|34aefe746cd81b7f3b23253ea28bef39  |1.00                |
|9c3a1c416c765687bc79a04113269929  |1.00                |
|da2782c804606d2a5d8e1760dbb3e7ec  |1.00                |

The query results show that the 10 lowest-rated sellers on the platform all received an average review score of 1.00, which is the minimum possible. This consistently poor feedback suggests significant issues with these sellers, such as product quality, service, or delivery problems. Identifying these sellers is crucial for Olist to take corrective actions, such as investigating customer complaints, offering support to improve service quality, or reconsidering their presence on the platform to maintain overall customer satisfaction.

# Q8: What is the average delivery time and the percentage of delayed deliveries across different states?

This question is significant for the Olist project because it helps assess the efficiency and reliability of order fulfillment across different geographic regions. By analyzing the **average delivery time** and the **percentage of delayed deliveries** by state, Olist can identify logistical bottlenecks, underperforming courier partners, or regional challenges that may be affecting customer satisfaction. These insights allow the business to optimize shipping strategies, allocate resources more effectively, improve delivery SLAs, and enhance the overall customer experience in regions where delays are frequent or delivery times are too long.

## Solution
```SQL
SELECT 
    c.customer_state,
    ROUND(AVG(EXTRACT(DAY FROM o.order_delivered_customer_date - o.order_purchase_timestamp)), 2) AS avg_delivery_days,
    ROUND(100.0 * SUM(
        CASE 
            WHEN o.order_delivered_customer_date > o.order_estimated_delivery_date THEN 1
            ELSE 0
        END
    ) / COUNT(*), 2) AS delayed_delivery_percentage
FROM 
    orders o
JOIN 
    customers c ON o.customer_id = c.customer_id
WHERE 
    o.order_delivered_customer_date IS NOT NULL
GROUP BY 
    c.customer_state
ORDER BY 
    delayed_delivery_percentage DESC;
```
This SQL query analyzes delivery performance across different customer states by calculating two key metrics: the average delivery time and the percentage of delayed deliveries. It joins the `orders` table with the `customers` table using the `customer_id` to group the data by `customer_state`. The first metric, `avg_delivery_days`, is computed by subtracting the purchase timestamp from the actual delivery date, extracting the day difference, and calculating the average per state. The second metric, `delayed_delivery_percentage`, calculates the proportion of orders where the actual delivery date exceeded the estimated delivery date. This is done by summing up cases where a delay occurred and dividing it by the total number of orders per state, converting it to a percentage. The result is ordered by delay percentage in descending order, helping identify states with the worst delivery performance.

## Output
|customer_state|avg_delivery_days|delayed_delivery_percentage|
|--------------|-----------------|---------------------------|
|AL            |24.04            |23.93                      |
|MA            |21.12            |19.67                      |
|PI            |18.99            |15.97                      |
|CE            |20.82            |15.32                      |
|SE            |21.03            |15.22                      |
|BA            |18.87            |14.04                      |
|RJ            |14.85            |13.47                      |
|TO            |17.23            |12.77                      |
|PA            |23.32            |12.37                      |
|ES            |15.33            |12.23                      |
|RR            |28.98            |12.20                      |
|MS            |15.19            |11.55                      |
|PB            |19.95            |11.03                      |
|PE            |17.97            |10.80                      |
|RN            |18.82            |10.76                      |
|SC            |14.48            |9.75                       |
|GO            |15.15            |8.18                       |
|RS            |14.82            |7.15                       |
|DF            |12.51            |7.07                       |
|MT            |17.59            |6.77                       |
|SP            |8.30             |5.89                       |
|MG            |11.54            |5.62                       |
|PR            |11.53            |5.00                       |
|AP            |26.73            |4.48                       |
|AM            |25.99            |4.14                       |
|AC            |20.64            |3.75                       |
|RO            |18.91            |2.88                       |

The data reveals notable disparities in delivery efficiency across Brazilian states. Alagoas (AL) had the highest average delivery time at 24.04 days and the highest delayed delivery rate at 23.93%, indicating significant logistical challenges. Conversely, states like São Paulo (SP) and Paraná (PR) showed much better performance, with SP having the lowest average delivery time of 8.3 days and a relatively low delay rate of 5.89%. Interestingly, some remote states such as Amapá (AP) and Amazonas (AM) had high delivery times (over 25 days) but low delay rates, suggesting that while deliveries take longer due to distance, they generally meet expectations. This analysis helps Olist identify regions where delivery logistics may require improvement and where customer expectations are being met more reliably.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%208a.png?raw=true)
###
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%208b.png?raw=true)
# Q9: Track monthly active sellers

Tracking monthly active sellers is significant for the Olist project as it provides critical insights into seller engagement and marketplace activity over time. By monitoring how many unique sellers are participating each month, Olist can assess platform growth, seller retention, and seasonal trends. It also helps identify any drops or spikes in seller activity that may signal operational issues, market fluctuations, or the effectiveness of marketing and onboarding strategies. Ultimately, this metric supports data-driven decision-making to optimize seller management, improve supply availability, and enhance overall marketplace performance.

## Solution
```SQL
SELECT 
    DATE_TRUNC('month', o.order_purchase_timestamp) AS month,
    COUNT(DISTINCT oi.seller_id) AS active_sellers
FROM orders o
JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY month
ORDER BY month;
```
This SQL query calculates the number of **active sellers per month** in the Olist marketplace. It begins by truncating the `order_purchase_timestamp` to the month level using `DATE_TRUNC('month', ...)`, allowing the grouping of data by month. Then, it joins the orders table with the `order_items` table on `order_id`, which links each order to its corresponding seller(s). For each month, it counts the **distinct** `seller_id` values, meaning it only counts each seller once per month, regardless of how many sales they made. The `GROUP BY month` clause organizes the results monthly, and `ORDER BY month` ensures the output is chronologically sorted. This query effectively tracks how many unique sellers were active in each month.

## Output
|month              |active_sellers|
|-------------------|--------------|
|2016-09-01 00:00:00|3             |
|2016-10-01 00:00:00|143           |
|2016-12-01 00:00:00|1             |
|2017-01-01 00:00:00|227           |
|2017-02-01 00:00:00|427           |
|2017-03-01 00:00:00|499           |
|2017-04-01 00:00:00|506           |
|2017-05-01 00:00:00|583           |
|2017-06-01 00:00:00|539           |
|2017-07-01 00:00:00|606           |
|2017-08-01 00:00:00|708           |
|2017-09-01 00:00:00|731           |
|2017-10-01 00:00:00|776           |
|2017-11-01 00:00:00|965           |
|2017-12-01 00:00:00|861           |
|2018-01-01 00:00:00|970           |
|2018-02-01 00:00:00|947           |
|2018-03-01 00:00:00|996           |
|2018-04-01 00:00:00|1123          |
|2018-05-01 00:00:00|1115          |
|2018-06-01 00:00:00|1175          |
|2018-07-01 00:00:00|1261          |
|2018-08-01 00:00:00|1278          |
|2018-09-01 00:00:00|1             |

The data shows the monthly trend of **active sellers** on the Olist platform from September 2016 to September 2018. Initially, seller activity was minimal, with only **3 active sellers in Sep 2016**. Over time, the number of active sellers steadily increased, peaking at **1,278 in August 2018**, indicating strong platform growth and seller engagement. Notably, there are outliers in **December 2016** and **September 2018**, showing **only 1 active seller**, likely due to data entry gaps or anomalies. Overall, the platform experienced consistent seller growth over the observed period.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%209.png?raw=true)

# Q10: Identify top customers who spent the most

Identifying the **top-spending customers** is vital for **customer segmentation and relationship management**. These high-value customers can be targeted with **personalized marketing**, **loyalty programs**, or **exclusive deals** to boost retention. Additionally, understanding their buying behavior helps Olist optimize product offerings, delivery service, and enhance overall customer satisfaction—ultimately driving **higher customer lifetime value** and **revenue growth**.

## Solution
```SQL
SELECT 
    c.customer_unique_id,
    ROUND(SUM(oi.price + oi.freight_value), 2) AS total_spent
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY c.customer_unique_id
ORDER BY total_spent DESC
LIMIT 10;
```
This SQL query identifies the **top 10 highest-spending customers** by calculating the total amount each customer spent, including product prices and freight costs. It joins the `customers`, `orders`, and `order_items` tables to track purchases, then groups by `customer_unique_id` (to account for customers with multiple accounts), sums up the total spent, and rounds it to 2 decimal places. Finally, it sorts customers in descending order of total spending and limits the result to the top 10. This insight is valuable for recognizing loyal or high-value customers, which is essential for targeted marketing and retention strategies.

## Output
|customer_unique_id                |total_spent|
|----------------------------------|-----------|
|0a0a92112bd4c708ca5fde585afaa872  |13664.08   |
|da122df9eeddfedc1dc1f5349a1a690c  |7571.63    |
|763c8b1c9c68a0229c42c9fc6f662b93  |7274.88    |
|dc4802a71eae9be1dd28f5d788ceb526  |6929.31    |
|459bef486812aa25204be022145caa62  |6922.21    |
|ff4159b92c40ebe40454e3e6a7c35ed6  |6726.66    |
|4007669dec559734d6f53e029e360987  |6081.54    |
|5d0a2980b292d049061542014e8960bf  |4809.44    |
|eebb5dda148d3893cdaf5b5ca3040ccb  |4764.34    |
|48e1ac109decbb87765a3eade6854098  |4681.78    |

The top 10 highest-spending customers on the Olist platform (based on total purchase + freight value) spent between **4,681.78 BRL** and **13,664.08 BRL**. The highest spender (`0a0a92112bd4c708ca5fde585afaa872`) spent **13,664.08 BRL**. These customers represent the platform’s most valuable buyers and are crucial for revenue generation. Understanding and engaging this segment can help in shaping personalized loyalty strategies and targeted marketing campaigns to drive repeat purchases and long-term retention.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%2010.png?raw=true)

# Q11: Top 5 Products with the Most Reviews and Their Average Rating

Identifying the top 5 most-reviewed products with their average ratings helps Olist understand which items are most popular and frequently purchased, providing insights into customer preferences, potential bestsellers, and product performance—valuable for marketing, inventory planning, and enhancing customer satisfaction.

## Solution
```SQL
SELECT 
    p.product_id,
    p.product_category_name,
    COUNT(r.review_id) AS total_reviews,
    ROUND(AVG(r.review_score), 2) AS average_rating
FROM 
    order_reviews r
JOIN 
    order_items i ON r.order_id = i.order_id
JOIN 
    products p ON i.product_id = p.product_id
GROUP BY 
    p.product_id, p.product_category_name
ORDER BY 
    total_reviews DESC
LIMIT 5;
```
This SQL query identifies the top 5 most-reviewed products and their average customer ratings. It starts from the `order_reviews` table, joins it with `order_items` using the `order_id`, and then connects to the `products` table using the `product_id` to associate each review with its corresponding product. For each product, it calculates the total number of reviews (`COUNT(r.review_id)`) and the average review score (`AVG(r.review_score)`), rounded to two decimal places. The results are grouped by both `product_id` and `product_category_name` to ensure distinct product-level aggregation. Finally, it orders the products by the number of reviews in descending order and limits the output to the top five, revealing the most discussed products along with how well they are rated by customers.

## Output
|product_id                        |product_category_name|total_reviews|average_rating|
|----------------------------------|---------------------|-------------|--------------|
|aca2eb7d00ea1a7b8ebd4e68314663af  |moveis_decoracao     |524          |4.02          |
|422879e10f46682990de24d770e7f83d  |ferramentas_jardim   |484          |3.95          |
|99a4788cb24856965c36a24e339b6058  |cama_mesa_banho      |480          |3.90          |
|389d119b48cf3043d311335e499d9c6b  |ferramentas_jardim   |389          |4.11          |
|368c6c730842d78016ad823897a372db  |ferramentas_jardim   |388          |3.92          |

The analysis reveals the top 5 most-reviewed products on the Olist platform, highlighting their popularity and customer satisfaction levels. The most reviewed product, from the **moveis_decoracao** (furniture and decor) category, received **524 reviews** with an average rating of **4.02**. Products in the **ferramentas_jardim** (garden tools) category dominate the list with three entries, having reviews ranging from **388 to 484** and average ratings between **3.92 and 4.11**. One product from the **cama_mesa_banho** (bed, bath, and table) category also appears, with **480 reviews** and a **3.90 average rating**. This data suggests that these product types not only have high customer engagement but also maintain relatively favorable customer satisfaction.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/1122.png?raw=true)

# Q12: Identify Orders with the Lowest Ratings and Longest Delivery Time

This analysis helps Olist identify problematic orders by highlighting those with **lowest customer ratings** and **longest delivery times**, offering critical insights into **operational inefficiencies, logistics issues, and poor customer experiences**, which can then be targeted for service improvement and customer retention strategies.

## Solution
```SQL
SELECT 
    o.order_id,
    r.review_score,
    EXTRACT(DAY FROM o.order_delivered_customer_date - o.order_purchase_timestamp) AS delivery_days
FROM 
    orders o
JOIN 
    order_reviews r ON o.order_id = r.order_id
WHERE 
    r.review_score = 1
ORDER BY 
    delivery_days DESC
LIMIT 10;
```
This SQL query identifies the top 10 orders that received the **lowest review score (1-star)** and also experienced the **longest delivery times**. It joins the `orders` and `order_reviews` tables based on `order_id`, filters only those orders with a review score of 1, calculates the number of days between purchase and delivery (`delivery_days`), and sorts the result in descending order of delivery duration. The output helps pinpoint the worst customer experiences, which are likely influenced by late deliveries, offering Olist actionable insights for improving logistics and customer satisfaction.

## Output
|order_id                          |review_score|delivery_days|
|----------------------------------|------------|-------------|
|95e42e6aaf6264cd3e77c06b32dc3003  |1           |NULL         |
|5869074071e0f56e9085dc3742c97b2a  |1           |NULL         |
|1b9ecfe83cdc259250e1a8aca174f0ad  |1           |NULL         |
|b68d69564a79dea4776afa33d1d2fcab  |1           |NULL         |
|ee64d42b8cf066f35eac1cf57de1aa85  |1           |NULL         |
|3b4ad687e7e5190db827e1ae5a8989dd  |1           |NULL         |
|8e24261a7e58791d10cb1bf9da94df5c  |1           |NULL         |
|d3c8851a6651eeff2f73b0e011ac45d0  |1           |NULL         |
|36530871a5e80138db53bcfd8a104d90  |1           |NULL         |
|38b7efdf33dd5561f4f5d4f6e07b0414  |1           |NULL         |

The output shows the 10 orders with the **lowest possible review score (1 star)** and their corresponding **delivery days**, but in all cases, the `delivery_days` value is `NULL`. This indicates that for these specific orders, the delivery date (`order_delivered_customer_date`) was not recorded in the database, possibly due to cancellations, lost shipments, or data entry issues. The combination of low ratings and missing delivery data suggests severe fulfillment problems, highlighting critical areas for operational improvement in Olist's delivery and tracking systems.

```SQL
SELECT
  CASE
    WHEN review_score >= 0 AND review_score < 1 THEN '0-1 Stars'
    WHEN review_score >= 1 AND review_score < 2 THEN '1-2 Stars'
    WHEN review_score >= 2 AND review_score < 3 THEN '2-3 Stars'
    WHEN review_score >= 3 AND review_score < 4 THEN '3-4 Stars'
    WHEN review_score >= 4 AND review_score < 5 THEN '4-5 Stars'
	WHEN review_score = 5 THEN '5 Stars'
    ELSE 'Unknown'
  END AS review_score_bin,
  COUNT(*) AS review_count
FROM order_reviews
GROUP BY review_score_bin
ORDER BY review_score_bin;
```
This SQL query groups all review scores from the `order_reviews` table into defined rating bins (e.g., '0-1 Stars', '1-2 Stars', ..., '5 Stars'), counts how many reviews fall into each bin using `COUNT(*)`, and then returns the results sorted by the review score bin. It uses a `CASE` statement to categorize the `review_score` values into ranges, enabling clearer analysis of how reviews are distributed across different star ratings.

## Output
|review_score_bin|review_count|
|----------------|------------|
|1-2 Stars       |11282       |
|2-3 Stars       |3114        |
|3-4 Stars       |8097        |
|4-5 Stars       |19007       |
|5 Stars         |56910       |

The chart displays the distribution of customer review scores categorized into rating bins. The majority of reviews fall under the "5 Stars" category with 56,910 reviews, indicating a strong prevalence of highly satisfied customers. This is followed by "4-5 Stars" with 19,007 reviews and "3-4 Stars" with 8,097, reflecting a generally positive sentiment. On the lower end, "1-2 Stars" account for 11,282 reviews, suggesting a notable number of dissatisfied customers, while "2-3 Stars" and "3-4 Stars" have fewer reviews comparatively. Overall, the data shows that most customers are satisfied, although there's a significant volume of negative feedback that should be investigated.

## Tableau Visualization
![Dashboard](https://github.com/ShaikhBorhanUddin/Brazilian_E_Commerce_Project/blob/main/Images/Sheet%201%20(1).png?raw=true)
