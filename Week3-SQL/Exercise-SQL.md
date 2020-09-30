
# SQL:  Structured Query Language  Exercise

### Getting Started
1. Go to BigQuery UI https://console.cloud.google.com/bigquery
2. Add in the public data sets. 
	3. Click the Add Data icon
	4. Add any dataset
	5. `bigquery-public-data` should become visible and populate in the BigQuery UI. 
3. Add your queries where it says [YOUR QUERY HERE].
4. Make sure you add your query in between the triple tick marks. 
---

For this section of the exercise we will be using the `bigquery-public-data.austin_311.311_service_requests`  table. 

5. Write a query that tells us how many rows are in the table. 
	```
	SELECT
  		COUNT(*)
	FROM
  		`bigquery-public-data.austin_311.311_service_requests`;
	```

6. Write a query that tells us how many _distinct_ values there are in the complaint_description column.
	``` 
	SELECT
  		COUNT(DISTINCT complaint_description) as dist_complaint
	FROM
  		`bigquery-public-data.austin_311.311_service_requests`;
	```
  
7. Write a query that counts how many times each owning_department appears in the table and orders them from highest to lowest. 
	``` 
	SELECT
  		owning_department,
  		COUNT(unique_key) AS count
	FROM
  		`bigquery-public-data.austin_311.311_service_requests`
	GROUP BY
  		owning_department
	ORDER BY
  		count DESC;
	```

8. Write a query that lists the top 5 complaint_description that appear most and the amount of times they appear in this table. (hint... limit)
	```
	SELECT
  		complaint_description,
  		COUNT(unique_key) AS count
	FROM
  		`bigquery-public-data.austin_311.311_service_requests`
	GROUP BY
  		complaint_description
	ORDER BY
  		count DESC
	LIMIT
  		5;
  	```
9. Write a query that lists and counts all the complaint_description, just for the where the owning_department is 'Animal Services Office'.
	```
	SELECT
  		complaint_description,
  		COUNT(unique_key) AS count
	FROM
  		`bigquery-public-data.austin_311.311_service_requests`
	WHERE
  		owning_department = 'Animal Services Office'
	GROUP BY
  		complaint_description
	ORDER BY
  		count DESC;
	```

10. Write a query to check if there are any duplicate values in the unique_key column (hint.. There are two was to do this, one is to use a temporary table for the groupby, then filter for values that have more than one count, or, using just one table but including the  `having` function). 
	```
	SELECT
  		unique_key, COUNT(unique_key) as count
	FROM
  		`bigquery-public-data.austin_311.311_service_requests`
	GROUP BY
  		unique_key
	HAVING
  		COUNT(unique_key) > 1;
  	```


### For the next question, use the `census_bureau_usa` tables.

1. Write a query that returns each zipcode and their population for 2000 and 2010. 
	```
	WITH
  		Table_2000 AS (
  	SELECT
    	zipcode,
    	population AS population_2000
 	FROM
    `bigquery-public-data.census_bureau_usa.population_by_zip_2000`
  	GROUP BY
    	zipcode,
    	population),
  	Table_2010 AS (
  	SELECT
    	zipcode,
    	population AS population_2010
  	FROM
    	`bigquery-public-data.census_bureau_usa.population_by_zip_2010`
  	GROUP BY
    	zipcode,
    	population)
	SELECT
  	Table_2000.zipcode,
  	Table_2000.population_2000,
  	Table_2010.population_2010
	FROM
  		Table_2000
	INNER JOIN
  		Table_2010
	ON
  	Table_2000.zipcode = Table_2010.zipcode
	```

### For the next section, use the  `bigquery-public-data.google_political_ads.advertiser_weekly_spend` table.
1. Using the `advertiser_weekly_spend` table, write a query that finds the advertiser_name that spent the most in usd.
	```
	SELECT
  		advertiser_name,
  		spend_usd
	FROM
  		`bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	ORDER BY
  		spend_usd DESC
	LIMIT
  		1
 	```
2. Who was the 6th highest spender? (No need to insert query here, just type in the answer.)
	```
	-- using the query above
	BIDEN FOR PRESIDENT
	6365800
	```

3. What week_start_date had the highest spend? (No need to insert query here, just type in the answer.)
	```
	2020-02-23
	```

4. Using the `advertiser_weekly_spend` table, write a query that returns the sum of spend by week (using week_start_date) in usd for the month of August only. 
	```
	SELECT
  		SUM(spend_usd) AS sum,
  		week_start_date
	FROM
  		`bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	WHERE
  		EXTRACT(month
  		FROM
    		week_start_date) = 08
	GROUP BY
  		week_start_date
	ORDER BY
  		sum DESC
	```
5.  How many ads did the 'TOM STEYER 2020' campaign run? (No need to insert query here, just type in the answer.)
	```
	50
	```
6. Write a query that has, in the US region only, the total spend in usd for each advertiser_name and how many ads they ran. (Hint, you're going to have to join tables for this one). 
	```
	SELECT
  		advertiser_name,
  		SUM(spend_usd) AS sum,
  		COUNT(*) AS count
	FROM
  		`bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	GROUP BY
 		advertiser_name
	ORDER BY
  		sum DESC
	```
7. For each advertiser_name, find the average spend per ad. 
	```
	SELECT
  		advertiser_name,
  		ROUND(AVG(spend_usd), 2) AS avg_per_ad,
	FROM
  		`bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	GROUP BY
  		advertiser_name
	ORDER BY
  		avg_per_ad DESC
	```
8. Which advertiser_name had the lowest average spend per ad that was at least above 0. 
	``` 
	SELECT
  		advertiser_name,
  		ROUND(AVG(spend_usd), 2) AS avg_per_ad,
	FROM
  		`bigquery-public-data.google_political_ads.advertiser_weekly_spend`
	GROUP BY
  		advertiser_name
	HAVING
  		avg_per_ad > 0
	```
## For this next section, use the `new_york_citibike` datasets.

1. Who went on more bike trips, Males or Females?
	```
	SELECT
  		gender,
  		COUNT(*) AS total_trips
	FROM
  		`bigquery-public-data.new_york_citibike.citibike_trips`
	GROUP BY
  		gender
	ORDER BY
  		total_trips DESC
	LIMIT
  		1
	```
2. What was the average, shortest, and longest bike trip taken in minutes?
	```
	SELECT
  		AVG(tripduration / 60) AS avg_trip,
  		MIN(tripduration / 60) AS shortest_trip,
  		MAX(tripduration / 60) AS longest_trip
	FROM
  		`bigquery-public-data.new_york_citibike.citibike_trips`
	```

3. Write a query that, for every station_name, has the amount of trips that started there and the amount of trips that ended there. (Hint, use two temporary tables, one that counts the amount of starts, the other that counts the number of ends, and then join the two.) 
	```
	WITH
  		t_start AS (
  		SELECT
    		start_station_name,
    		COUNT(*) AS total_start
  		FROM
    		`bigquery-public-data.new_york_citibike.citibike_trips`
  		GROUP BY
    		start_station_name),
  		t_end AS (
  		SELECT
    		end_station_name,
    		COUNT(*) AS total_end
  		FROM
    		`bigquery-public-data.new_york_citibike.citibike_trips`
  		GROUP BY
    		end_station_name)
	SELECT
  		t_start.start_station_name,
  		t_start.total_start,
  		t_end.total_end
	FROM
  		t_start
	INNER JOIN
  		t_end
	ON
  		t_start.start_station_name = t_end.end_station_name
  ```
# The next section is the Google Colab section.  
1. Open up this [this Colab notebook](https://colab.research.google.com/drive/1kHdTtuHTPEaMH32GotVum41YVdeyzQ74?usp=sharing).
2. Save a copy of it in your drive. 
3. Rename your saved version with your initials. 
4. Click the 'Share' button on the top right.  
5. Change the permissions so anyone with link can view. 
6. Copy the link and paste it right below this line. 
	* YOUR LINK: https://colab.research.google.com/drive/18kOyaPuIJsaNG-jac6Os_B1atU8cG2wp?usp=sharing
9. Complete the two questions in the colab notebook file. 
