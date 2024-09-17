# Primary Key

Test de la colonne “Country” comme primary key

```python
SELECT
  Country, # on part sur un test de la colonne "Country"
  COUNT(*) AS nb # on lui demande de rajouter une colonne "nb" dans laquelle on va compter la somme de chaque "id"
FROM
  `projet-data-analytics-416309.Project.country_continent`
GROUP BY
  Country # on regroupe les résultats par "Country"
HAVING
  nb > 2 # on lui demande d'appliquer un filtre qui nous montre les potentiels résultats supérieurs à 2
ORDER BY
  nb desc # on lui demande de classer ses réusltats par ordre décroissant pour tout de suite voir les éventuels "2"
```

---

# Cleaning

Cette table va nous servir de table centrale pour les différents JOIN ultérieurs. Il s’agit ici de nettoyer les noms de pays pour les uniformiser sur l’ensemble du dataset. Cette query pourra ensuite être appliqué à n’importe quelle table, afin d’appliquer le même nettoyage.

```python
SELECT
    Region AS region,
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
                                                        Country, 
                                                        'áéíóúàèìòùâêîôûãẽĩõũäëïöüçñÁÉÍÓÚÀÈÌÒÙÂÊÎÔÛÃẼĨÕŨÄËÏÖÜÇÑÅ-,*', 
                                                        'aeiouaeiouaeiouaeiouaeioucnAEIOUAEIOUAEIOUAEIOUAEIOUCNA '
                                                    ), 
                                                    r'Tanzania the United Republic of', 'Tanzania'
                                                ), 
                                                r'Korea \(the Republic of\)', 'South Korea'
                                            ), 
                                            r"Korea \(the Democratic People's Republic of\)", "North Korea"
                                        ), 
                                        r'United Kingdom of Great Britain and Northern Ireland', 'United Kingdom'
                                    ), 
                                    r'Palestine State of', 'Palestine'
                                ), 
                                r"Lao People's Democratic Republic", "Laos"
                            ), 
                            r"Macao \(Special Adminstrative Region China", 'Macao'
                        ), 
                        r'Syrian Arab Republic', 'Syria'
                    ),
                    r'\s+', ' ' # Replace double spaces with a single space
                ),
                r'\([^)]*\)', ''  # Remove parentheses and everything in between
            ),
            r'\[[^\]]*\]', '' # Remove brackets and everything in between
        )
    ) AS country
FROM
    `projet-data-analytics-416309.Project.country_continent`
```

## Step by step pour rajouter nom des continents a n’importe quelle table:

- Effectuer un `FULL OUTER JOIN` avec la table `country_continent_clean` avec la query suivante, puis comparer les `NULL`

```sql
SELECT 
  country_cleaned,
  country,
  region
FROM `projet-data-analytics-416309.Project.animal_countries` as an
FULL OUTER JOIN `projet-data-analytics-416309.Project_table_clean.country_continent_clean` as co
ON co.country = an.country_cleaned
```

- Appliquer la query de cleaning (ci-dessus) a notre table d’intérêt en rajoutant un REGEXP_REPLACE par condition spécifique
- Create a column with only 3 conservation status & update the table:

```sql
CREATE OR REPLACE TABLE `projet-data-analytics-416309.final_tables.animals_final` AS
SELECT *,
  CASE 
    WHEN iucnStatus IN ('Least concern', 'Least Concern') THEN 'Least concern'
    WHEN iucnStatus IN ('Extinct', 'Extinct in the wild', 'Extinct in wild') THEN 'Extinct'
    WHEN iucnStatus IN ('Vulnerable', 'Near threatened', 'Endangered', 'Critically endangered', 'Dangered') THEN 'Endangered'
    WHEN iucnStatus IN ('NA') THEN 'NA'
    END AS Conservation_status
FROM `projet-data-analytics-416309.final_tables.animals_final`;
```
