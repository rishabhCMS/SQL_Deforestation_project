# SQL Deforestation project

# Introduction

1. Understand which countries and regions around the world seem to have forests that have been shrinking in size

2. which countries and regions have the most significant forest area both in terms of total area and % of total area

# AIM

Making informed and well motivated decisions with respect to making the biggest impact given the few resources available

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

a. What was the percent forest of the entire world in 2016? Which region had the HIGHEST percent forest in 2016, and which had the LOWEST, to 2 decimal places?
~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016' and country_name = 'World'),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016' and country_name = 'World')
      
SELECT 
    ROUND(
	cast((forest_table.forest_area_sqkm/(land_table.total_area_sq_mi*2.59))*100
		AS NUMERIC),2  
	 ) AS prcnt_area
FROM land_table, forest_table
~~~~
![Part2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2a1.png)

~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016' and country_name != 'World' AND land_area.total_area_sq_mi IS NOT NULL),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016' and country_name != 'World' AND forest_area.forest_area_sqkm IS NOT 				NULL)
SELECT 	l.country_name,
		l.year,
		l.total_area_sq_mi*2.29 total_area_sq_km,
        	f.forest_area_sqkm,
	    	ROUND(
			cast((f.forest_area_sqkm/(l.total_area_sq_mi*2.59))*100
			AS NUMERIC),2  
		 ) AS prcnt_area        
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
ORDER BY prcnt_area ASC
LIMIT 10
~~~~
![Part2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2a2.png)


~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '2016' and country_name != 'World' AND land_area.total_area_sq_mi IS NOT NULL),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '2016' and country_name != 'World' AND forest_area.forest_area_sqkm IS NOT 				NULL)
SELECT 	l.country_name,
		l.year,
		l.total_area_sq_mi*2.29 total_area_sq_km,
        	f.forest_area_sqkm,
	    	ROUND(
			cast((f.forest_area_sqkm/(l.total_area_sq_mi*2.59))*100
			AS NUMERIC),2  
		 ) AS prcnt_area        
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
ORDER BY prcnt_area DESC
LIMIT 10
~~~~
![Part2 Image](https://github.com/rishabhCMS/SQL_Deforestation_project/blob/master/images/Part2a3.png)

b. What was the percent forest of the entire world in 1990? Which region had the HIGHEST percent forest in 1990, and which had the LOWEST, to 2 decimal places?
~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '1990' and country_name = 'World' AND land_area.total_area_sq_mi IS NOT NULL),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '1990' and country_name = 'World' AND forest_area.forest_area_sqkm IS NOT 				NULL)
SELECT 	l.country_name,
		l.year,
		l.total_area_sq_mi*2.29 total_area_sq_km,
        	f.forest_area_sqkm,
	    	ROUND(
			cast((f.forest_area_sqkm/(l.total_area_sq_mi*2.59))*100
			AS NUMERIC),2  
		 ) AS prcnt_area        
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
ORDER BY prcnt_area ASC
LIMIT 10
~~~~

~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '1990' and country_name != 'World' AND land_area.total_area_sq_mi IS NOT NULL),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '1990' and country_name != 'World' AND forest_area.forest_area_sqkm IS NOT 				NULL)
SELECT 	l.country_name,
		l.year,
		l.total_area_sq_mi*2.29 total_area_sq_km,
        	f.forest_area_sqkm,
	    	ROUND(
			cast((f.forest_area_sqkm/(l.total_area_sq_mi*2.59))*100
			AS NUMERIC),2  
		 ) AS prcnt_area        
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
ORDER BY prcnt_area ASC
LIMIT 10
~~~~
~~~~sql
WITH land_table AS (SELECT *
		    FROM land_area
		    WHERE year = '1990' and country_name != 'World' AND land_area.total_area_sq_mi IS NOT NULL),
      forest_table AS (SELECT *
		       FROM forest_area
		       WHERE year = '1990' and country_name != 'World' AND forest_area.forest_area_sqkm IS NOT 				NULL)
SELECT 	l.country_name,
		l.year,
		l.total_area_sq_mi*2.29 total_area_sq_km,
        	f.forest_area_sqkm,
	    	ROUND(
			cast((f.forest_area_sqkm/(l.total_area_sq_mi*2.59))*100
			AS NUMERIC),2  
		 ) AS prcnt_area        
FROM land_table l 
INNER JOIN forest_table f
ON f.country_name = l.country_name
ORDER BY prcnt_area DESC
LIMIT 10
~~~~

c. Based on the table you created, which regions of the world DECREASED in forest area from 1990 to 2016?


