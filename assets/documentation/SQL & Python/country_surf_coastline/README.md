Name of the final table: “country_surf_coastline_clean_final_cont”

JOIN table `fertilizer` x `continents`

```sql
SELECT * 
FROM `projet-data-analytics-416309.Land.country_surf_coastline_clean_final` as surf
LEFT JOIN `projet-data-analytics-416309.Land.country_continent_clean` as co
ON surf.Country = co.country;
```
