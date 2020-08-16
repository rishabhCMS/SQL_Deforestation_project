# SQL Deforestation project

# Introduction

1. Understand which countries and regions around the world seem to have forests that have been shrinking in size

2. which countries and regions have the most significant forest area both in terms of total area and % of total area

# AIM

Making informed and well motivated decisions with respect to making the biggest impact given the few resources available

## Part 1: Questions to be addressed

a. What was the total forest area (in sq km) of the world in 1990? Please keep in mind that you can use the country record denoted as “World" in the region table.

~~~~sql
SELECT * FROM forest_area
WHERE country_name= 'World' AND year = '1990'
~~~~


b. What was the total forest area (in sq km) of the world in 2016? Please keep in mind that you can use the country record in the table is denoted as “World.”

~~~~sql
SELECT * FROM forest_area
WHERE country_name= 'World' AND year = '2016'
~~~~

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
