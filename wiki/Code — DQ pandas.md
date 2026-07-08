---
type: code
tags: [code, pandas, data-quality, foundry]
created: 2026-07-01
updated: 2026-07-08
sources:
  - "repo: ChrisCo31/Data_scripts — pandas_data_quality/"
---

# Code — DQ pandas

Fonctions de contrôle qualité en pandas, compatibles `toPandas()` (Foundry). Organisées par type de contrôle.

> Toutes les fonctions SAP acceptent indifféremment un DataFrame pandas ou un Spark DataFrame (détection via `hasattr(df, 'toPandas')`).

## Profilage structurel

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/profiling_stucture.py` | `Profiling_Structure(df)` | dtype, % distinct, % null + pattern heuristique par colonne |
| `niEO/02_profilage_structurel_V2.py` | `profile_structure(df)` | Même logique, version exploratory avec seuils configurables |

**Patterns détectés :** clé primaire candidate, catégoriel, FK candidate, champ creux, mono-valeur, dtype suspect, horodatage.

## Détection de nulls

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/detection_sentinels.py` | `detection_sentinels(df)` | Vrais NULL + faux NULL textuels (`--`, `""`, `N/A`...) par colonne |
| `niEO/03_2_nulls_absolus.py` | — | NULL vrais triés par % décroissant |
| `niEO/03_3_nulls_conditionnels.py` | — | NULL selon valeur d'une colonne pivot, export anomalies Excel |

## Complétude (fill rate)

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/sap_master_fill_rate.py` | `sap_master_fill_rate(df)` | % remplissage par colonne, bucket 6 niveaux, top 5 valeurs |

**Buckets :** 100% / 80-99% / 50-79% / 25-49% / 1-24% / 0%.

## Unicité & doublons

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/duplicate_values.py` | `duplicate_values(df)` | Valeurs en doublon sur une colonne cible, triées par occurrences |
| `sap/unique_values_count.py` | `unique_values_count(df)` | n_unique et % unique par colonne |
| `niEO/04_1_unicite_cles.py` | `check_unicity(df, name, keys)` | OK/KO sur clé candidate composite |

## Conformité de format

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/sap_master_conformity.py` | `sap_master_conformity(df)` | Format dominant par colonne (numeric, date_iso, alphanumeric...), % conformité, bucket |

## Cohérence intra-table

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/incoherence_par_cle.py` | `incoherence_par_cle(df)` | Clés avec plusieurs valeurs distinctes sur un champ attendu unique |
| `sap/compare_columns.py` | `compare_columns(df)` | Comparaison ligne à ligne + analyse ensembliste entre 2 colonnes |
| `niEO/04_2_coherence_intra_groupe.py` | — | Cohérence bi-directionnelle triplet ↔ ID |

## Fraîcheur temporelle

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/temporal_freshness.py` | `temporal_freshness(df)` | Colonnes temporelles : oldest/newest, stale > 1 an, dates futures, incohérences chronologiques |

## Nettoyage & utilitaires

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/sap_master_cleaning_column_name.py` | `sap_master_cleaning_column_name(df)` | Supprime les colonnes dont le nom commence par "REACH" |
| `sap/drop_column.py` | `find_full_null_columns(df)` | Détecte les colonnes 100% NULL (compatible pandas + Spark) |
| `sap/rename_table.py` | CLI `rename_table.py <old> <new> *.py` | Renomme le nom de table dans tous les scripts ciblés |

## Dashboard global

| Script | Fonction | Ce qu'il produit |
|---|---|---|
| `sap/dq_dashboard_summary.py` | `dq_dashboard_summary(df)` | Score global DQ (completeness + conformity + uniqueness) + détail par colonne |
| `niEO/run_all_reports.py` | — | Excel multi-onglets : accueil, nulls, unicité |

## Liens

- [[Code — DQ PySpark]] — version native PySpark pour Foundry (sans toPandas)
- [[Data Engineering]] — contexte qualité de données
- [[DAG (dépendances datasets & pipelines)]] — ces contrôles correspondent au nœud "DQ Checks" d'un DAG orchestré
