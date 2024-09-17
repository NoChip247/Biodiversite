Join LPI table (2017 index) with the fully joined table

```sql
SELECT 
  fi.*,
  ROUND(lpi.Average_Index, 2) AS LPI_2017,
FROM `projet-data-analytics-416309.final_tables.final_join` as fi
LEFT JOIN `projet-data-analytics-416309.final_tables.lpi` as lpi
ON fi.region = lpi.Region
WHERE Year = 2017;
```

Create a column with the conservation status aggregated by country

```sql
SELECT 
  Country,
  SUM(CASE WHEN Conservation_status LIKE 'Least concern' THEN 1 ELSE 0 END) AS Least_concern,
  SUM(CASE WHEN Conservation_status LIKE 'Endangered' THEN 1 ELSE 0 END) AS Endangered,
  SUM(CASE WHEN Conservation_status LIKE 'Extinct' THEN 1 ELSE 0 END) AS Extinct, 
 FROM `projet-data-analytics-416309.final_tables.animals_final` 
 GROUP BY Country
```

Here, the deforestation measures human deforestation 2015-2020 and the forest fires:

```sql
SELECT 
  country,
  region,
  ((COALESCE(Deforestation_2015_2020, 0)) + (COALESCE(Total_Forest_Area_Affected_by_Fire_2015, 0))) AS Deforestation
 FROM `projet-data-analytics-416309.final_tables.forest`
WHERE country IS NOT NULL
```

```sql
CREATE OR REPLACE TABLE `projet-data-analytics-416309.final_tables.final_join` AS
SELECT 
  fi.country,
  fi.region,
  TotGHGemissions,
  Nitrogen,
  fe.Deforestation
FROM `projet-data-analytics-416309.final_tables.ghg_fertilizer_final` as fi
LEFT JOIN `projet-data-analytics-416309.final_tables.final_join` as fe
ON fe.country = fi.country
```

Join table final - The round should divide by the number of values and not by 4 (eg. if there is a null it should divide by 3). Option : create a column that counts the non null values

```sql
SELECT 
  fi.*,
  di.Climatic_DI,
  di.Geophysical_DI,
  di.Meteo_DI,
  di.Hydrological_DI,
  ROUND((IFNULL(Climatic_DI, 0) + IFNULL(Geophysical_DI, 0) + IFNULL(Meteo_DI,0) + IFNULL(Hydrological_DI, 0))/4, 2) AS Disasters
FROM `projet-data-analytics-416309.final_tables.pre-final_3` as fi
LEFT JOIN `projet-data-analytics-416309.final_tables.Natural_disasters_Fulljoin` as di 
ON fi.country = di.country
```

Addition of the number of treaties (include the null values for the dates which indicate that the treaty was not signed)

```sql
SELECT 
  COUNT (Convention_name) as nb_treaties,
  country_cleaned
FROM `projet-data-analytics-416309.Natural_Disaster.Governance_Join` 
WHERE `date` IS NOT Null
GROUP BY country_cleaned
;SELECT 
  tb.*,
  co.nb_treaties
FROM `projet-data-analytics-416309.final_tables.pre-final_3` as tb 
LEFT JOIN `projet-data-analytics-416309.final_tables.convention_number` as co
ON tb.country = co.country_cleaned
```

Final join

```sql
SELECT 
  COUNT (Convention_name) as nb_treaties,
  country_cleaned
FROM `projet-data-analytics-416309.Natural_Disaster.Governance_Join` 
WHERE `date` IS NOT Null
GROUP BY country_cleaned
;
```
