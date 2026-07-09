---
type: concept
tags: [data-engineering, spark, pandas, palantir-foundry, performance, dq]
created: 2026-07-09
updated: 2026-07-09
sources:
  - "note utilisateur: Pandas vs PySpark — le piège du toPandas()"
---

# Pandas vs PySpark — performance Foundry

Dans un workbook Palantir, appeler `.toPandas()` sur un gros dataset **rapatrie toutes les données sur le driver** Spark → risque d'erreur `Serialized results too large` (dépassement de `spark.driver.maxResultSize`, ex. 1024 MiB).

## Pourquoi

Spark = un **driver** (1 seule machine) + des **executors** (distribués). Pandas ne s'exécute que sur le driver : tout doit tenir en RAM locale. `toPandas()` sérialise donc l'intégralité du dataset distribué vers cette seule machine.

| | Pandas | PySpark |
|---|---|---|
| Exécution | 1 seule machine (le driver) | Distribuée (N executors) |
| Mémoire | Tout tient en RAM du driver | Réparti + spill sur disque |
| `toPandas()` | — | Sérialise tout vers le driver ⚠️ |
| Bon usage | Petits résultats (< quelques 100k lignes) | Gros volumes, agrégations |

## La règle

Ne jamais matérialiser le dataset entier sur le driver. Faire les agrégations **en Spark natif** (`.agg()`, `F.approx_count_distinct`, `F.count(F.when(...))`...) et ne `collect()` qu'une **ligne de résultat par colonne analysée**, pas les données brutes. Un bon job Spark remonte un résultat de **taille constante**, indépendant du volume de données en entrée.

- `approx_count_distinct` : rapide (~2% d'erreur), suffisant pour du profiling DQ.
- `countDistinct` : exact mais beaucoup plus lourd sur des colonnes à forte cardinalité.
- Retourner un **Spark DataFrame** (output natif Foundry), pas un `pd.DataFrame`, dès que le volume peut être important.

## ⚠️ Tension avec [[Code — DQ pandas]]

Les scripts `sap/*.py` de `Code — DQ pandas` reposent systématiquement sur la conversion `hasattr(df, 'toPandas')` → `df.toPandas()` pour analyser une table SAP/Foundry, y compris pour des tables potentiellement volumineuses (ex. `SAP_MASTER`). C'est exactement le pattern que cette note identifie comme risqué. Ces scripts pandas restent adaptés à des tables de taille raisonnable (jusqu'à quelques 100k lignes) ou à un usage exploratoire ponctuel, mais ne sont pas la bonne approche pour un contrôle DQ industrialisé sur un gros dataset — préférer dans ce cas l'équivalent [[Code — DQ PySpark]] natif (`logic/` + `transforms/`), qui suit déjà cette règle (pas de `toPandas()`, agrégations distribuées).

## Liens

- [[Code — DQ pandas]] — scripts qui utilisent `toPandas()`, à surveiller sur les gros volumes
- [[Code — DQ PySpark]] — approche native conforme à cette règle
- [[Data Engineering]] — contexte qualité de données et outils
