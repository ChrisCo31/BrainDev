---
tags: [data-engineering, spark, pandas, palantir-foundry, performance, dq]
created: 2026-07-09
sujet: Perfo Pandas vs PySpark dans un workbook Foundry
---
# ⚡ Pandas vs PySpark — Le piège du `toPandas()`
## 🎯 Le problème en une phrase
Dans un workbook Palantir, appeler `.toPandas()` sur un gros dataset **rapatrie toutes les données sur le driver** → erreur `Serialized results too large` (dépassement de `spark.driver.maxResultSize`, ex. 1024 MiB).
## 🧠 L'analogie
> [!info] Le driver vs les executors
> Spark = un chef de chantier (**driver**) + une équipe d'ouvriers (**executors**).
> - `toPandas()` demande aux ouvriers de **vider tout l'entrepôt sur le bureau du chef** pour qu'il compte seul → le bureau déborde.
> - La bonne approche : chaque ouvrier **compte son rayon**, et ne remonte au chef que le **total** (une ligne minuscule).
## 🔍 Pourquoi Pandas ne scale pas
| | Pandas | PySpark |
|---|---|---|
| Exécution | **1 seule machine** (le driver) | **Distribuée** (N executors) |
| Mémoire | Tout tient en RAM du driver | Réparti + spill sur disque |
| `toPandas()` | — | Sérialise tout vers le driver ⚠️ |
| Bon usage | Petits résultats (< quelques 100k lignes) | Gros volumes, agrégations |
## ✅ La solution : agréger en Spark natif
Ne jamais matérialiser le dataset entier. On calcule les métriques **en distribué** et on ne `collect()` qu'**une seule ligne** de résultat.
```python
from pyspark.sql import functions as F
def unique_values_count(input_df):
    df = input_df
    total = df.count()
    # Une seule passe Spark : distinct approx + nb de nulls par colonne
    aggs = []
    for i, c in enumerate(df.columns):
        aggs.append(F.approx_count_distinct(F.col(c)).alias(f"nu_{i}"))
        aggs.append(F.count(F.when(F.col(c).isNull(), F.lit(1))).alias(f"nn_{i}"))
    row = df.agg(*aggs).collect()[0]   # 1 seule ligne → aucun risque
    resultats = []
    for i, c in enumerate(df.columns):
        n_unique = row[f"nu_{i}"]
        n_null = row[f"nn_{i}"]
        pct = round(n_unique / total * 100, 2) if total else 0.0
        resultats.append((c, int(n_unique), pct, int(n_null), int(total)))
    schema = ["column", "n_unique", "pct_unique", "n_null", "total_rows"]
    return df.sparkSession.createDataFrame(resultats, schema).orderBy(F.desc("n_unique"))
```
## 📌 Règles à retenir
- [ ] Éviter `.toPandas()` / `.collect()` sur un dataset complet.
- [ ] Faire les agrégations **côté Spark**, ne rapatrier que le **résultat final**.
- [ ] `approx_count_distinct` = rapide (~2 % d'erreur) → OK pour du profiling DQ.
- [ ] `countDistinct` = exact mais **beaucoup plus lourd** sur colonnes larges.
- [ ] Retourner un **Spark DataFrame** (output natif Foundry), pas un `pd.DataFrame`.
> [!tip] Réflexe DQ
> Si le résultat qui remonte au driver **grandit avec la taille des données**, il y a un problème de design. Un bon job Spark remonte un résultat **de taille constante**.
