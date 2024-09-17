# Cleaning + Join

- Sub query : Cleaning des noms de pays qui vont servir de primary key
- Query finale : on fait un `FULL OUTER JOIN` avec la table de référence `country_continent` pour avoir les noms de continents en face de chaque pays. `WHERE region IS NULL` permet de voir les pays qui n'ont pas de continent attribué, et donc les pays qui n'ont pas été nettoyés par la query de cleaning. On peut rajouter à la query de cleaning les cas particuliers si besoin.

```sql
WITH sub AS (
SELECT
*,
TRIM(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
REGEXP_REPLACE(
TRANSLATE(
Country_and_Area, 
'áéíóúàèìòùâêîôûãẽĩõũäëïöüçñÁÉÍÓÚÀÈÌÒÙÂÊÎÔÛÃẼĨÕŨÄËÏÖÜÇÑÅ-,*', 
'aeiouaeiouaeiouaeiouaeioucnAEIOUAEIOUAEIOUAEIOUAEIOUCNA '),
r'Tanzania the United Republic of', 'Tanzania'),
r'Korea \(the Republic of\)', 'South Korea'),
r"Korea \(the Democratic People's Republic of\)", "North Korea"),
r'United Kingdom of Great Britain and Northern Ireland', 'United Kingdom'),
r'Palestine State of', 'Palestine'),
r"Lao People's Democratic Republic", "Laos"),
r"Macao \(Special Adminstrative Region China", 'Macao'),
r'Syrian Arab Republic', 'Syria'),
r'\s+', ' '),
r'\([^)]*\)', ''),
r'\[[^\]]*\]', '')
) AS country_clean
FROM
`projet-data-analytics-416309.Land.Forest_area_c`
)

-----

SELECT
fo.Country_and_Area,
fo.country_clean,
cc.country,
cc.region
FROM sub AS fo
FULL OUTER JOIN projet-data-analytics-416309.Land.country_continent_clean AS cc
ON fo.country_clean=cc.country
--WHERE region IS NULL
```

---

# Transformer la structure de la table

- À l’origine, la data base Forest était sous la forme :

| region | Forest_Area__1990 | Forest_Area__2000 | Forest_Area__2010 | Forest_Area__2015 | Forest_Area__2020 |
|---|---|---|---|---|---|
| Africa | 549244.0 | 529578.0 | 507004.0 | 495342.0 | 483846.0 |


- En utilisant Python, on utilise la fonction .melt() de Pandas pour réordonner l’architecture du dataframe sous la forme suivante :

| region | year | area |
|---|---|---|
|Africa | 1990 | 549244.0 |
|Africa | 2000 | 529578.0 |
|Africa | 2010 | 507004.0 |
|Africa | 2015 | 495342.0 |
|Africa | 2020 | 483846.0 |

```python
df_melt = df.melt(id_vars='region', var_name='year', value_name='area')

df_melted
```

---

# Appliquer un filtre pour afficher seulement les données de l’Amérique du sud

Pour avoir un zoom sur l’Amérique du sud

```sql
SELECT
  country,
  region,
  Forest_Area__1990,
  Forest_Area__2000,
  Forest_Area__2010,
  Forest_Area__2015,
  Forest_Area__2020,
  Total_Land_Area__2020,
  Forest_Area_as_a__Proportion_of_,
  Deforestation_2015_2020,
  Total_Forest_Area_Affected_by_Fire_2015
FROM
  `projet-data-analytics-416309.Land.forest`
WHERE country IN ("Argentina", "Paraguay", "Bolivia", "Uruguay", "Chile", "Suriname", "Colombia", "Peru", "Brazil")
ORDER BY Forest_Area_as_a__Proportion_of_ DESC
```

---

# Transformation des valeurs absolues en pourcentage

Les surfaces de forêts sont en hectares/pays. Il s’agit de les passer en pourcentage/région pour que les surfaces deviennent compréhensibles.

```sql
# la 1ere sub query vient aggréger le total des surfaces en hectares par régions
WITH sub AS (
SELECT
region,
ROUND(sum(Forest_Area__1990),0) AS Forest_Area__1990,
ROUND(sum(Forest_Area__2000),0) AS Forest_Area__2000,
ROUND(sum(Forest_Area__2010),0) AS Forest_Area__2010,
ROUND(sum(Forest_Area__2015),0) AS Forest_Area__2015,
ROUND(sum(Forest_Area__2020),0) AS Forest_Area__2020
FROM
  `projet-data-analytics-416309.Land.forest`
WHERE region IS NOT NULL
GROUP BY region
)

# La 2e query prend le total des surfaces en hectares par régions, et le passe en pourcentage
SELECT
region,
Forest_Area__1990,
ROUND(SAFE_DIVIDE(Forest_Area__1990, Forest_Area__1990) * 100, 1) AS Forest_Area__1990_Percentage,
Forest_Area__2000,
ROUND(SAFE_DIVIDE(Forest_Area__2000, Forest_Area__1990) * 100, 1) AS Forest_Area__2000_Percentage,
Forest_Area__2010,
ROUND(SAFE_DIVIDE(Forest_Area__2010, Forest_Area__1990) * 100, 1) AS Forest_Area__2010_Percentage,
Forest_Area__2015,
ROUND(SAFE_DIVIDE(Forest_Area__2015, Forest_Area__1990) * 100, 1) AS Forest_Area__2015_Percentage,
Forest_Area__2020,
ROUND(SAFE_DIVIDE(Forest_Area__2020, Forest_Area__1990) * 100, 1) AS Forest_Area__2020_Percentage
FROM sub
```

---

# Cleaning de la nomenclature

```sql
SELECT
  CASE
    WHEN region ="Asia and the Pacific" THEN "Asia and Pacific"
    ELSE region
  END AS region,
  * EXCEPT(region)
FROM
  `projet-data-analytics-416309.Land.Forest_as_percent`
```

---

# INNER JOIN
Living_Planet_Index X Forest_Area_Melt*

Le but est de joindre les deux tables, pour ensuite faire un test statistique et vérifier les corrélations “déclin de la forêt” Vs. “déclin de la biodiversité”.

*La table Forest_area est restructurée en amont avec la fonction .melt() dans Python, pour avoir une structure similaire à la table Living_Planet_Index.

```sql
SELECT
  lpi.*,
  fo.*
FROM
  projet-data-analytics-416309.Animal. lpi_1990_2018_clean AS lpi
INNER JOIN
  projet-data-analytics-416309.Land.forest_area_region_melt_clean AS fo
ON
  lpi.Region=fo.region
  AND lpi.Year=fo.year
```

---

# Test statistique de correlation

La table lpi_x_forest créée précédemment, est importée dans Python pour faire le test statistique.

```sql
df.corr()
```

![](https://github.com/NoChip247/Biodiversite/blob/889602364a6dbc7e46f6e8e6c8f674859d0f4622/assets/documentation/SQL%20%26%20Python/forest_area_table/1.png)

```sql
import seaborn as sns

sns.heatmap(df.corr(), annot=True)
```

![](https://github.com/NoChip247/Biodiversite/blob/889602364a6dbc7e46f6e8e6c8f674859d0f4622/assets/documentation/SQL%20%26%20Python/forest_area_table/2.png)

*0 = pas/peu de correlation*

*-1 ou 1 = plus on se rapproche de -1 et plus on a de correlation négative. Plus on se rapproche de 1 et plus on a de corrélation positive.*

**New_Average_Index X Year** = -0,65. Le déclin de la biodiversité (New_Average_Index) est expliqué à 65% par le temps qui passe. Statistiquement, plus le temps passe, plus la biodiversité décline.

**New_Average_Index X area** = -0,25. Le déclin de la biodiversité (New_Average_Index) est expliqué à 25% par la diminution de la surface des forêts. Plus la surface de forêt diminue, plus la biodiversité décline.
