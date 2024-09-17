## Importer un .xlsx qui pose problème

1. Importer le .xlsx dans Google Drive
2. Ouvrir le .xlsx avec Google Sheet
3. Enregistrer une copie du .xlsx au format Google Sheet

![Capture d’écran 2024-03-05 à 11.38.46.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/6324b2c7-c874-4731-aaf6-cad85d31156a/0f5cab84-8ae2-49a4-b5c3-adef64f6ec01/Capture_decran_2024-03-05_a_11.38.46.png)

1. Dans BigQuery, lors de la création d’une nouvelle table, indiquer : 
    1. Créer une table à partir de : Drive
    2. Insérer l’URL du Google Sheet
    3. Format de fichier : Feuille de calcul Google Sheet
2. Cas spécifique: Problème d’affichage des headers lors de l’import.
- Vérifier le type des cellules (String, Int, Float, etc…)
- Si le problème persiste, procéder à l’entrée manuelle des headers, spécifier le **MÊME TYPE** des cellules que celui de la feuille de calcul.
