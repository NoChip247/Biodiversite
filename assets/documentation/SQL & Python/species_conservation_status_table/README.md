```sql
SELECT 
Country
,region
,`order`
,SUM(CASE WHEN Conservation_status = 'Extinct' THEN 1 ELSE 0 END) AS Extinct
,SUM(CASE WHEN Conservation_status = 'Endangered' THEN 1 ELSE 0 END) AS Endangered
,SUM(CASE WHEN Conservation_status = 'Least concern' THEN 1 ELSE 0 END) AS Least_concern
, COUNT(sciName) as count_species
, CONCAT(Country, '_', `order`) AS primary_key
FROM `final_tables.animals_final`
WHERE `order` IN ('CARNIVORA', 'PRIMATES', 'RODENTIA', 'PHOLIDOTA', 'PERISSODACTYLA', 'ARTIODACTYLA') 
GROUP BY Region, Country, `order`;
```
