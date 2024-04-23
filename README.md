# Project-
# Analyse Promotion and Provide tangible Insight to Sales Director of the AtliQ Mart

Explore the live Power BI dashboard for interactive visualizations:

View Power BI Dashboard
## Problem Statement 🎯:
🛒 AtliQ Mart, a retail giant with over 50 supermarkets in southern India, ran massive promotions during Diwali 2023 and Sankranti 2024 🎉.
Now, the Sales Director seeks insights to make informed decisions for future promotions. Let's analyze which promotions succeeded and which ones fell short! 💡
## Key Tasks and Findings

 1. 𝗗𝗮𝘁𝗮 𝗘𝘅𝗽𝗹𝗼𝗿𝗮𝘁𝗶𝗼𝗻: I meticulously explored sales data, examining trends, customer behavior, and product performance during the festive period.
 2. 𝗣𝗿𝗼𝗺𝗼𝘁𝗶𝗼𝗻 𝗘𝗳𝗳𝗲𝗰𝘁𝗶𝘃𝗲𝗻𝗲𝘀𝘀: Using SQL queries, I assessed the impact of various promotions on sales volume, revenue, and customer engagement.
 3. 𝗣𝗼𝘄𝗲𝗿 𝗕𝗜 𝗗𝗮𝘀𝗵𝗯𝗼𝗮𝗿𝗱: To visualize the results, I created an interactive Power BI dashboard. It revealed which promotions resonated with customers and which fell short.
# Business Requests
1. High-Value Products in 'BOGOF' Promotion

Objective: Identify high-value products featured in the 'BOGOF' (Buy One Get One Free) promotion.
```sql
SELECT DISTINCT  product_name
FROM dim_products p
LEFT JOIN fact_events e
 on p.product_code=e.product_code
WHERE base_price > 500 AND promo_type= "BOGOF";
```
2. Store Presence Overview
   
Objective: Provide an overview of the number of stores in each city.
```sql
SELECT city,
count(store_id)as store_count
FROM dim_stores
GROUP BY city
ORDER BY store_count DESC;
```
3. Promotional Campaign Revenue Analysis
   
Objective: Display total revenue generated before and after each promotional campaign.
```sql
SELECT c.campaign_name,
concat(format(sum(f.`quantity_sold(before_promo)`*base_price)/1000000,2),'M') as "total_revenue(before_promo)",
 concat(format(sum(f.`quantity_sold(after_promo)`*base_price)/1000000,2),'M') as "total_revenue(after_promo)"
FROM dim_campaigns c
left join fact_events f
on c.campaign_id=f.campaign_id
group by c.campaign_name ; 
```
4. Incremental Sold Quantity Analysis during Diwali Campaign
   
Objective: Calculate Incremental Sold Quantity (ISU%) for each category during the Diwali campaign.
```sql
SELECT t.category,
concat(format(((t.new_quantity- t.old_quality)/ t.old_quality)*100,2),"%") as "ISU(%)",
row_number () over( order  by ((t.new_quantity- t.old_quality)/ t.old_quality) desc) as rank_order 
FROM  
 (select p.category,
sum(`quantity_sold(after_promo)`) as new_quantity,
sum(`quantity_sold(before_promo)`) as old_quality
from fact_events e
left join dim_products p
on p.product_code=e.product_code
left join dim_campaigns c
on c.campaign_id=e.campaign_id
where e.campaign_id="CAMP_DIW_01"
group by p.category  ) as t ;
```
5. Top 5 Products by Incremental Revenue Percentage
   
Objective: Identify the top 5 products ranked by Incremental Revenue Percentage (IR%) across all campaigns.
```sql
select p.product_name,
p.category,concat(format((new_revenue-old_revenue)/old_revenue*100,2),"%") as "IR(%)"
from (select product_code,
        sum(`quantity_sold(before_promo)`)as old_revenue,
        sum(`quantity_sold(after_promo)`) as new_revenue
from fact_events
group by  product_code) as t
left join dim_products p
on p.product_code=t.product_code
order by (new_revenue-old_revenue)/old_revenue*100 desc
limit 5;
```


# Results and Insights
The analysis revealed several key insights:

1.High-value products featured in 'BOGOF' promotions.

2.Distribution of stores across different cities.

3.Total revenue generated before and after each promotional campaign.

4.Incremental sold quantity and revenue percentage during the Diwali campaign.

5.Top 5 products ranked by incremental revenue percentage.

6.These insights can help AtliQ Mart make informed decisions for future promotional activities, optimize resource allocation, and improve overall sales performance.


# Conclusion
Overall, the analysis provides valuable insights into the performance of promotional campaigns conducted by AtliQ Mart during Diwali 2023 and Sankranti 2024.
By leveraging data analytics, AtliQ Mart can enhance its marketing strategies, attract more customers, and drive higher sales during festive seasons.
