---
type: code
tags: [code, pyspark, data-quality, foundry, transforms]
created: 2026-07-01
updated: 2026-07-08
sources:
  - "repo: ChrisCo31/Data_scripts — pyspark_data_quality/"
---

# Code — DQ PySpark

Contrôles qualité en **PySpark natif** pour Foundry — pas de `toPandas()`, tout reste distribué. Structuré en `logic/` (fonctions pures) + `transforms/` (wrappers `@transform` Foundry).

## Architecture

```
logic/          → fonctions de calcul pures, testables indépendamment
transforms/     → @transform Foundry qui appellent logic/ et écrivent le résultat
```

## Profilage structurel

| Logic | Transform | Ce qu'il produit |
|---|---|---|
| `profile_logic.py` — `compute_profile_rows(df)` | `tr_profile.py` | dtype, n_distinct, pct_distinct, n_null, pct_null, pattern par colonne |

Même heuristique de pattern que la version pandas (clé primaire, catégoriel, FK, mono-valeur...), réécrite pour les types Spark (`StringType`, `timestamp`...).

## Détection de nulls

| Logic | Transform | Ce qu'il produit |
|---|---|---|
| `null_logic.py` — `compute_null_rows(df)` | `tr_null_dataset.py` | (column_name, n_null, pct_null) pour chaque colonne avec NULL > 0 |
| `full_null_logic.py` — `compute_full_null_columns(df, excluded_columns)` | `tr_full_null_columns.py` | Colonnes 100% NULL candidates à la suppression |

## Détection de sentinelles

| Logic | Transform | Ce qu'il produit |
|---|---|---|
| `sentinel_logic.py` — `compute_sentinel_rows(df)` | `tr_sentinels_dataset.py` | Par colonne string : count de `--`, `""`, espaces, `NULL/NA/N/A` |

Patterns détectés : `dash_--`, `empty_string`, `whitespace_only`, `NULL_string`.

## Doublons

| Logic | Transform | Ce qu'il produit |
|---|---|---|
| `duplicate_logic.py` — `compute_duplicate_values(df, col_name)` | `tr_duplicate_sapmat.py` | (value, occurrences) triées desc pour une colonne cible |

## Schémas Spark (définis dans les transforms)

Chaque transform définit un `StructType` explicite pour garantir le typage en sortie :
- `NULL_SCHEMA` — StringType, LongType, DoubleType
- `FULL_NULL_SCHEMA` — StringType, LongType, LongType
- `SENTINEL_SCHEMA` — StringType + 5 LongType
- `PROFILE_SCHEMA` — StringType × 3, LongType × 2, DoubleType × 2, StringType (pattern)

## Liens

- [[Code — DQ pandas]] — version pandas / toPandas() pour les mêmes contrôles
- [[Architecture Foundry — Couches]] — contexte transforms Foundry (@transform, Input, Output)
- [[Data Engineering]] — contexte qualité de données
- [[DAG (dépendances datasets & pipelines)]] — chaque `@transform` est un nœud du DAG, avec ses `Input`/`Output` comme dépendances
