Join fertilizer table with continents

```sql
SELECT *
FROM `projet-data-analytics-416309.Pollution.Consumption of fertilizers per unit of agricultural land area_clean` as fe
FULL OUTER JOIN `projet-data-analytics-416309.Land.country_continent_clean` as cont
ON fe.Country = cont.country;
```

Join GHG table with continents

```sql
SELECT * 
FROM `projet-data-analytics-416309.Pollution.GHG_Emissions_clean` as ghg
FULL OUTER JOIN `projet-data-analytics-416309.Land.country_continent_clean` as cont
ON ghg.Country = cont.country
```

There are null values due to the outer join: cleaned with the following query on both tables. On ne delete pas les valeurs nulles sur la table d’origine car cela est une info supplémentaire ! ⇒ il faudrait que ces pays mesurent leurs emissions.

```sql
SELECT 
region, 
country_1,
Country,
latest_year_available,
TotGHGemissions__Mton_CO2_equivalent_,
__change_since_1990,
GHGemissions_per_capita__ton_CO2_equivalent_
FROM `projet-data-analytics-416309.Pollution.GHG_emissions_final`
WHERE region IS NOT NULL
```

Table GHG saved as GHG_final dans le dossier pollution

Table fertilizers saved as fertilizers_final dans le dossier pollution

```sql
SELECT 
Country,
Nitrogen__tons_ as Nitrogen,
Phosphate__tons_ as Phosphate,
Potash__tons_ as Potash,
country_1,
region
FROM `projet-data-analytics-416309.Pollution.Fertilizer_continent_final`
WHERE region IS NOT NULL
```

There were duplicates with Congo, treated as follows and table saved as fertilizer_final2 & GHG_final

```sql
SELECT 
*
FROM `projet-data-analytics-416309.Pollution.Fertilizer_final` 
WHERE Country LIKE 'Congo';

DELETE 
FROM `projet-data-analytics-416309.Pollution.Fertilizer_final` 
WHERE Country LIKE 'Congo' AND Nitrogen =0.05;

DELETE FROM `projet-data-analytics-416309.Pollution.Fertilizer_final` 
WHERE Country IN (
  SELECT Country
  FROM `projet-data-analytics-416309.Pollution.Fertilizer_final` 
  GROUP BY Country
  HAVING COUNT(*) > 1
);

SELECT 
  Country,
  count(*) AS nb
FROM
  `projet-data-analytics-416309.Pollution.Fertilizer_final`
GROUP BY
  Country
HAVING
  nb>=2
ORDER BY nb DESC;

Select * FROM `projet-data-analytics-416309.Pollution.Fertilizer_final` 
```
