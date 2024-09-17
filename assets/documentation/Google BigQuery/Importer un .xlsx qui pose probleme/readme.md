## Importer un .xlsx qui pose problème

1. Importer le .xlsx dans Google Drive
2. Ouvrir le .xlsx avec Google Sheet
3. Enregistrer une copie du .xlsx au format Google Sheet

![](https://github.com/NoChip247/Biodiversite/blob/1fc9cc7775960f6b5fed0c695546057bda5835d2/assets/documentation/Google%20BigQuery/Importer%20un%20.xlsx%20qui%20pose%20probleme/ixeles.png)

1. Dans BigQuery, lors de la création d’une nouvelle table, indiquer : 
    1. Créer une table à partir de : Drive
    2. Insérer l’URL du Google Sheet
    3. Format de fichier : Feuille de calcul Google Sheet
2. Cas spécifique: Problème d’affichage des headers lors de l’import.
- Vérifier le type des cellules (String, Int, Float, etc…)
- Si le problème persiste, procéder à l’entrée manuelle des headers, spécifier le **MÊME TYPE** des cellules que celui de la feuille de calcul.
