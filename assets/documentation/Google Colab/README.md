# Ouvrir une table BigQuery dans un notebook (pandas_gbq)
```
import pandas_gbq
project_id = 'projet-data-analytics-416309'
sql = """
SELECT *
FROM projet-data-analytics-416309.Project.Animal_species_clean
"""
df = pandas_gbq.read_gbq(sql, project_id= project_id)
```
