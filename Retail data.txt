use PROJECT;

SELECT * FRom Retail_Data
DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantiy IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
--	Q1. Write a SQL query to retrieve all columns for sales made on '2022-11-05:
	
	SELECT * FROM Retail_Data
	WHERE sale_date= '2022-11-05';
-- Q2.Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 4 in the month of Nov-2022
SELECT 
  *
FROM Retail_Data
WHERE 
    category = 'Clothing'
    AND 
    SELECT FORMAT(GETDATE(), '2022-11') as sale_date; 
    AND
    quantity >= 4
-- Q3 Write a SQL query to calculate the total sales (total_sale) for each category.:
	
	SELECT 
	category,
	SUM(total_sale) as NET_SALE,
	COUNT(*) as TOTAL_orders
	FROM retail_data
	GROUP by category;
-- Q4	Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.

SELECT 
	ROUND(AVG(age), 2) as avg_age
	FROM Retail_Data

WHERE	category = 'beauty'
--Q5Write a SQL query to find all transactions where the total_sale is greater than 1000.:

SELECT * FROM retail_sales
WHERE total_sale > 1000

-- Q6 .Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM Retail_Data
GROUP 
    BY 
    category,
    gender
ORDER BY gender


--Q7 .Write a SQL query to calculate the average sale for each month. Find out best selling month in each year:

SELECT 
       year,
       month,
       avg_sale
FROM 
(    
    SELECT 
        YEAR(sale_date) AS year,
        MONTH(sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (
            PARTITION BY YEAR(sale_date) 
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY YEAR(sale_date), MONTH(sale_date)
) AS t1
WHERE rank = 1;
--Q8.Write a SQL query to find the top 5 customers based on the highest total sales **
	SELECT TOP (5)
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY SUM(total_sale) DESC;

--Q9. Write a SQL query to find the number of unique customers who purchased items from each category.:



SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category

-- Q10.Write a SQL query to create each shift and number of orders (Example Morning <12, Afternoon Between 12 & 17, Evening >17):

WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
