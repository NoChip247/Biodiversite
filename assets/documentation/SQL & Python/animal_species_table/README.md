# Animal_species ← species_big.csv

Table renommée sur Big Query en Animal_species

Lors de l’import, 2 erreurs. J’ai du autoriser pour pouvoir loader le fichier (Laurence)

Cleaning: voir les queries “animal_species_queries” sur Big Query

Cleaning with steps & basic data viz in colabs:

https://colab.research.google.com/drive/13_3KOXCmsCE-ynwdK56jQ_h5uIWBl3e8?usp=sharing

Cleaning des pays sur BigQuery pour rajouter le continent (join avec table country_continent):

- Full outer join avec la table country_continent_clean avec le code suivant & compatif des null/NA

```sql
SELECT
country_cleaned,
country,
region
FROM projet-data-analytics-416309.Project.animal_countries as an
FULL OUTER JOIN projet-data-analytics-416309.Project_table_clean.country_continent_clean as co
ON co.country = an.country_cleaned
```

- Traitement des null/NA avec le code suivant:

```sql
SELECT DISTINCT
    country_exploded,
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
                                                                                country_exploded, 
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
        r'&', "and"
        ),
        r'Democratic Republic of the Congo', "Congo"
        ),
        r'Republic of the Congo', 'Congo'
        ),
        r'Hawai\'i', 'Hawaii'
        ),
        r'Vietnam', 'Viet Nam'
        ),
        r'Czech Republic', 'Czechia'
        ),
        r'Brunei', 'Brunei Darussalam'
        ),
        r'United States', 'United States of America'
        ),
        r'Russia', 'Russian Federation'
        ),
        r'East Timor', 'Timor Leste'
        ),
        r'Alaska', 'United States of America'
                    ),
                    r'\s+', ' ' # Replace double spaces with a single space
                ),
                r'\([^)]*\)', ''  # Remove parentheses and everything in between
            ),
            r'\[[^\]]*\]', '' # Remove brackets and everything in between                            
    )
    ) AS country_cleaned
FROM
    `projet-data-analytics-416309.Project.animal_species_clean3`
```

- Liste des null non traites (influence mineure sur la biodiversite des continents):

| 2 | Prince Edward Islands | null | null |  |
| --- | --- | --- | --- | --- |
| 3 | NA | null | null |  |
| 4 | Bonaire | null | null |  |
| 5 | null | null | null |  |
| 6 | United States of America Virgin Islands | null | null |  |
| 7 | Canary Islands | null | null |  |
| 8 | Sint Eustatius | null | null |  |
| 9 | Andaman Islands | null | null |  |
| 10 | Saba | null | null |  |
| 11 | British Virgin Islands | null | null |  |
| 12 | Saint Helena | null | null |  |
| 13 | Galapagos | null | null |  |
| 14 | Domesticated | null | null |  |
| 15 | Nicobar Islands | null | null |  |
| 16  | Azores | null | null |  |
|  17  | Madeira | null | null |  |
|  18  | Kerguelen Islands | null | null |  |

- Join avec les continents, puis sauvegarde de la nouvelle table:

```sql
WITH country_cleaned2 AS (
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
                                                                                country_exploded, 
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
        r'&', "and"
        ),
        r'Democratic Republic of the Congo', "Congo"
        ),
        r'Republic of the Congo', 'Congo'
        ),
        r'Hawai\'i', 'Hawaii'
        ),
        r'Vietnam', 'Viet Nam'
        ),
        r'Czech Republic', 'Czechia'
        ),
        r'Brunei', 'Brunei Darussalam'
        ),
        r'United States', 'United States of America'
        ),
        r'Russia', 'Russian Federation'
        ),
        r'East Timor', 'Timor Leste'
        ),
        r'Alaska', 'United States of America'
                    ),
                    r'\s+', ' ' # Replace double spaces with a single space
                ),
                r'\([^)]*\)', ''  # Remove parentheses and everything in between
            ),
            r'\[[^\]]*\]', '' # Remove brackets and everything in between
                            
    )
    ) AS country_cleaned
FROM
    `projet-data-analytics-416309.Project.animal_species_clean3`)
SELECT 
    sp.*,
    co.region
FROM country_cleaned2 AS sp
LEFT JOIN `projet-data-analytics-416309.Project_table_clean.country_continent_clean` as co
ON co.country = sp.country_cleaned;
```

Update 08.03.2024: join avec la table country_continent_clean pour rajouter Latin America

Cleaning des pays qui n’ont pas de continent assigne sur Big Query

```sql
SELECT * 
FROM `projet-data-analytics-416309.Animal.animals_final` 
WHERE region IS NOT NULL
```

Enregistrement de la table sous le nom ‘animal_final’ dans le dossier “animal”

Creation d’une colonne avec le nom du status de conservation

```sql
SELECT 
  Country,
  SUM(CASE WHEN Conservation_status LIKE 'Least concern' THEN 1 ELSE 0 END) AS Least_concern,
  SUM(CASE WHEN Conservation_status LIKE 'Endangered' THEN 1 ELSE 0 END) AS Endangered,
  SUM(CASE WHEN Conservation_status LIKE 'Extinct' THEN 1 ELSE 0 END) AS Extinct, 
 FROM `projet-data-analytics-416309.final_tables.animals_final` 
 GROUP BY Country
```
