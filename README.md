# SQL Deforestation project

# Introduction

1. Understand which countries and regions around the world seem to have forests that have been shrinking in size

2. which countries and regions have the most significant forest area both in terms of total area and % of total area

# AIM

Making informed and well motivated decisions with respect to making the biggest impact given the few resources available
## Create View
~~~~sql
CREATE VIEW forestation AS
SELECT f.country_code,
		f.country_name,
		r.region,
		r.income_group,
		f.year,
		f.forest_area_sqkm,
		l.total_area_sq_mi*2.59 AS land_area_sqkm,
		f.forest_area_sqkm/(l.total_area_sq_mi*2.59)*100 AS prcnt_area
		FROM forest_area f
		INNER JOIN land_area l
		ON f.country_code = l.country_code
		AND f.year = l.year
		JOIN regions r
		ON r.country_code = f.country_code
		Order by 1,5
~~~~

## Part 1: Global Situation

a. What was the total forest area (in sq km) of the world in 1990? Please keep in mind that you can use the country record denoted as “World" in the region table.

~~~~sql
SELECT * FROM forest_area
WHERE country_name= 'World' AND year = '1990'
~~~~
![Q1 expected result](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Q1.png)

b. What was the total forest area (in sq km) of the world in 2016? Please keep in mind that you can use the country record in the table is denoted as “World.”

~~~~sql
SELECT * FROM forest_area
WHERE country_name= 'World' AND year = '2016'
~~~~
![Q2 expected result](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Q2.png)


c. What was the change (in sq km) in the forest area of the world from 1990 to 2016?

~~~~sql
WITH t1990 AS (SELECT forest_area_sqkm
               FROM forest_area
               WHERE country_name = 'World' AND year = '1990'),
     t2016 AS (SELECT forest_area_sqkm
               FROM forest_area
               WHERE country_name = 'World' AND year = '2016')
SELECT (t1990.forest_area_sqkm - t2016.forest_area_sqkm) change
FROM t1990, t2016
~~~~
![Q3 expected result](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Q3.png)

d. What was the percent change in forest area of the world between 1990 and 2016?

~~~~sql
WITH t1990 AS (SELECT forest_area_sqkm
               FROM forest_area
               WHERE country_name = 'World' AND year = '1990'),
     t2016 AS (SELECT forest_area_sqkm
               FROM forest_area
               WHERE country_name = 'World' AND year = '2016')
SELECT t1990.forest_area_sqkm forest_area_sqkm_1990,
		t2016.forest_area_sqkm forest_area_sqkm_2016,
	(t1990.forest_area_sqkm - t2016.forest_area_sqkm) change,
	((t1990.forest_area_sqkm - t2016.forest_area_sqkm)/t1990.forest_area_sqkm)*100 as prcnt_change
FROM t1990, t2016
~~~~
![Q4 expected result](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Q4.png)


e. If you compare the amount of forest area lost between 1990 and 2016, to which country's total area in 2016 is it closest to?

~~~~sql
WITH Country_2016 AS (SELECT * 
                      FROM land_area
                      WHERE year = '2016')
SELECT *, 
		ABS((((Country_2016.total_area_sq_mi)*2.59) - 1324449)) diff_sq_km
FROM Country_2016
ORDER BY diff_sq_km
LIMIT 10
~~~~
![Q5 expected result](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Q5.png)

## Part 2 Regional Outlook

**a. What was the percent forest of the entire world in 2016? Which region had the HIGHEST percent forest in 2016, and which had the LOWEST, to 2 decimal places?**
~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016' AND country_name = 'World'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016' AND country_name = 'World')
SELECT 	r.region,
		SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
	    	ROUND(
			cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
			AS NUMERIC),2  
		 ) AS prcnt_area 
       
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
INNER JOIN regions r
ON r.country_code = f.country_code
GROUP BY 1
ORDER BY 4 DESC
LIMIT 10
~~~~
![Part2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2a1.png)

~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016')
SELECT 	r.region,
		SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
	    	ROUND(
			cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
			AS NUMERIC),2  
		 ) AS prcnt_area 
       
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
INNER JOIN regions r
ON r.country_code = f.country_code
GROUP BY 1
ORDER BY 4 DESC
LIMIT 10
~~~~
![Part2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2a2.png)


~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016')
SELECT 	r.region,
		SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
	    	ROUND(
			cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
			AS NUMERIC),2  
		 ) AS prcnt_area 
       
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
INNER JOIN regions r
ON r.country_code = f.country_code
GROUP BY 1
ORDER BY 4 ASC
LIMIT 10
~~~~
![Part2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2a3.png)

**b. What was the percent forest of the entire world in 1990? Which region had the HIGHEST percent forest in 1990, and which had the LOWEST, to 2 decimal places?**
~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '1990' and country_name = 'World'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '1990' and country_name = 'World')
SELECT 	r.region,
		SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
	    	ROUND(
			cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
			AS NUMERIC),2  
		 ) AS prcnt_area 
       
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
INNER JOIN regions r
ON r.country_code = f.country_code
GROUP BY 1
ORDER BY 4 DESC
LIMIT 10
~~~~
![Part 2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2b1.png)
~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '1990'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '1990')
SELECT 	r.region,
		SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
	    	ROUND(
			cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
			AS NUMERIC),2  
		 ) AS prcnt_area 
       
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
INNER JOIN regions r
ON r.country_code = f.country_code
GROUP BY 1
ORDER BY 4 DESC
LIMIT 10
~~~~
![Part 2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2b2.png)

~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '1990'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '1990')

SELECT 	r.region,
		SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
	    	ROUND(
			cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
			AS NUMERIC),2  
		 ) AS prcnt_area 
       
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
INNER JOIN regions r
ON r.country_code = f.country_code
GROUP BY 1
ORDER BY 4 ASC
LIMIT 10
~~~~
![Part 2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2b3.png)

**c. Based on the table you created, which regions of the world DECREASED in forest area from 1990 to 2016?**

~~~~sql
WITH t1 AS(WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '1990'),
               forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '1990')
			SELECT 	r.region,
					SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
				SUM(f.forest_area_sqkm) total_forest_area_sqkm,
					ROUND(
						cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
						AS NUMERIC),2  
					 ) AS prcnt_area 

			FROM land_table l 
			INNER JOIN forest_table f
			ON f.country_name = l.country_name
			INNER JOIN regions r
			ON r.country_code = f.country_code
			GROUP BY 1
			ORDER BY 4 DESC),
           
     t2 AS (WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016'),
      		forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016')
			SELECT 	r.region,
					SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
				SUM(f.forest_area_sqkm) total_forest_area_sqkm,
					ROUND(
						cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
						AS NUMERIC),2  
					 ) AS prcnt_area 

			FROM land_table l 
			INNER JOIN forest_table f
			ON f.country_name = l.country_name
			INNER JOIN regions r
			ON r.country_code = f.country_code
			GROUP BY 1
			ORDER BY 4 DESC)
            
SELECT t1.region, t1.prcnt_area prcnt_area_1990, t2.prcnt_area prcnt_area_2016,
		t1.prcnt_area - t2.prcnt_area diff
FROM t1
JOIN t2
ON t1.region = t2.region
ORDER BY 1 DESC
~~~~
![Part 2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2c.png)

## Part 3 Country-Level Detail

**a. Which 5 countries saw the largest amount decrease in forest area from 1990 to 2016? What was the difference in forest area for each?**

~~~~sql
WITH t1990 AS (SELECT *
               FROM forest_area
               WHERE country_name != 'World' AND year = '1990'),
     
     t2016 AS (SELECT *
               FROM forest_area
               WHERE country_name != 'World' AND year = '2016')

SELECT  t1990.country_name,
		t1990.forest_area_sqkm forest_area_sqkm_1990,
		t2016.forest_area_sqkm forest_area_sqkm_2016,
		(t1990.forest_area_sqkm - t2016.forest_area_sqkm) change,
		((t1990.forest_area_sqkm - t2016.forest_area_sqkm)/t1990.forest_area_sqkm)*100 as prcnt_change
FROM t1990
JOIN t2016
ON t1990.country_name = t2016.country_name
ORDER BY 4 DESC
LIMIT 5
~~~~
![Part 3 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part3a.png)

**b. Which 5 countries saw the largest percent decrease in forest area from 1990 to 2016? What was the percent change to 2 decimal places for each?**

~~~~sql
WITH t1990 AS (SELECT *
               FROM forest_area
               WHERE country_name != 'World' AND year = '1990' ),
     
     t2016 AS (SELECT *
               FROM forest_area
               WHERE country_name != 'World' AND year = '2016')

SELECT  t1990.country_name,
		t1990.forest_area_sqkm forest_area_sqkm_1990,
		t2016.forest_area_sqkm forest_area_sqkm_2016,
		(t1990.forest_area_sqkm - t2016.forest_area_sqkm) change,
		ROUND(
			CAST(((t1990.forest_area_sqkm - t2016.forest_area_sqkm)/t1990.forest_area_sqkm)*100 AS NUMERIC),2
			) as prcnt_change
FROM t1990
JOIN t2016
ON t1990.country_name = t2016.country_name
ORDER BY 5 DESC
LIMIT 5
~~~~
![Part 3 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part3b.png)


**c. If countries were grouped by percent forestation in quartiles, which group had the most countries in it in 2016?**

~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016' and country_name != 'World' AND total_area_sq_mi IS NOT NULL),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016' and country_name != 'World' AND forest_area_sqkm IS NOT NULL),
      t1 AS (SELECT 	f.country_name,
			SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
	        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
		    	ROUND(
				cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
				AS NUMERIC),2  
			 ) AS prcnt_area 
       
			FROM land_table l 
			INNER JOIN forest_table f
			ON f.country_name = l.country_name
			INNER JOIN regions r
			ON r.country_code = f.country_code
			GROUP BY 1
			ORDER BY 4 DESC
			)
	
SELECT t2.quartile, COUNT(*)

FROM(SELECT *, 
		CASE 
     		WHEN t1.prcnt_area > 75 THEN 4
     		WHEN t1.prcnt_area > 50 AND t1.prcnt_area <= 75 THEN 3
     		WHEN t1.prcnt_area > 25 AND t1.prcnt_area <= 50 THEN 2
     		ELSE 1
     	END AS quartile

	FROM t1) AS t2
GROUP BY 1
ORDER BY 2 DESC
~~~~
![Part 3 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part3c.png)

**d. List all of the countries that were in the 4th quartile (percent forest > 75%) in 2016.**

~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016' and country_name != 'World'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016' and country_name != 'World'),
      t1 AS (SELECT 	f.country_name,
			SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
	        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
		    	ROUND(
				cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
				AS NUMERIC),2  
			 ) AS prcnt_area 
       
			FROM land_table l 
			INNER JOIN forest_table f
			ON f.country_name = l.country_name
			INNER JOIN regions r
			ON r.country_code = f.country_code
			GROUP BY 1
			ORDER BY 4 DESC
			)
	
SELECT *, COUNT(*)

FROM(SELECT *, 
		CASE 
     		WHEN t1.prcnt_area > 75 THEN 4
     		WHEN t1.prcnt_area > 50 AND t1.prcnt_area <= 75 THEN 3
     		WHEN t1.prcnt_area > 25 AND t1.prcnt_area <= 50 THEN 2
     		ELSE 1
     	END AS quartile

	FROM t1) AS t2
WHERE t2.quartile = 4
GROUP BY 1,2,3,4,5
~~~~
![Part 3 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part3d.png)

**e. How many countries had a percent forestation higher than the United States in 2016?**
~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016' and country_name != 'World'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016' and country_name != 'World'),
      t1 AS (SELECT 	f.country_name,
			SUM(l.total_area_sq_mi*2.59) total_area_sqkm,
	        SUM(f.forest_area_sqkm) total_forest_area_sqkm,
		    	ROUND(
				cast((SUM(f.forest_area_sqkm)/(SUM(l.total_area_sq_mi*2.59)))*100
				AS NUMERIC),2  
			 ) AS prcnt_area 
       
			FROM land_table l 
			INNER JOIN forest_table f
			ON f.country_name = l.country_name
			INNER JOIN regions r
			ON r.country_code = f.country_code
			GROUP BY 1
			ORDER BY 4 DESC
			)
	
SELECT COUNT(*)

FROM(SELECT *, 
		CASE 
     		WHEN t1.prcnt_area > 75 THEN 4
     		WHEN t1.prcnt_area > 50 AND t1.prcnt_area <= 75 THEN 3
     		WHEN t1.prcnt_area > 25 AND t1.prcnt_area <= 50 THEN 2
     		ELSE 1
     	END AS quartile

	FROM t1) As t2
WHERE t2.prcnt_area > (
                          SELECT  prcnt_area
                          FROM    t1
                          WHERE   country_name = 'United States'
                          )
~~~~
![Part 3 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part3e.png)
