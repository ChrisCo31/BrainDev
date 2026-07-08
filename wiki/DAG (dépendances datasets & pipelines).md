---
type: concept
tags: [dag, pipelines, lineage, data-engineering, orchestration, foundry, palantir]
created: 2026-07-08
updated: 2026-07-08
sources:
  - "[[DAGs — dépendances datasets et pipelines (note utilisateur)]]"
---

# DAG (dépendances datasets & pipelines)

> "The system explicitly manages dependencies between datasets and pipelines (DAGs)" : le système connaît et gère explicitement les relations de dépendance entre les jeux de données (datasets) et les traitements (pipelines), représentées sous forme de graphe orienté sans cycle (Directed Acyclic Graph).

## Décomposition

- **Dataset** — une donnée produite ou consommée par un traitement (ex : SAP brut, données nettoyées, indicateurs qualité).
- **Pipeline** — une suite d'étapes de transformation (Extract → Nettoyage → Enrichissement → Calcul KPI).
- **Dépendance** — une étape ne peut s'exécuter que si les données dont elle dépend sont disponibles. Si un pipeline amont échoue, le pipeline aval ne peut pas démarrer.

## Ce que le graphe permet au système de savoir

- quel dataset dépend de quel autre ;
- quels pipelines doivent être recalculés lorsqu'une donnée change ;
- dans quel ordre exécuter les traitements ;
- quels impacts aura la suppression ou la modification d'un dataset.

## Pourquoi c'est important dans un Data Product

| Bénéfice | Explication |
|---|---|
| **Traçabilité (lineage)** | on sait d'où provient chaque donnée |
| **Automatisation** | un changement en amont déclenche les recalculs nécessaires |
| **Analyse d'impact** | avant de modifier un dataset, on connaît tous les consommateurs affectés |
| **Fiabilité** | les traitements sont exécutés dans le bon ordre |

## Rapport avec le lineage Foundry

Dans [[Ontologie Palantir|Palantir Foundry]], cette notion est très proche du **data lineage** : chaque dataset connaît ses producteurs, ses consommateurs et les transformations qui le relient au reste du paysage de données. Chaque bloc du DAG est un **Dataset**, relié par une **Transformation Pipeline** (Code Repository, Pipeline Builder ou Contour) — voir [[Architecture Foundry — Couches]] (2.1 Data Sources / 2.2 Logic Sources). La plateforme construit ce graphe automatiquement et peut répondre à : « si je modifie le dataset Supplier Master, quels datasets, KPI et dashboards seront impactés ? »

## Exemple concret : Data Product Qualité fournisseurs Airbus

Contexte : suivre le taux de complétude des fournisseurs, les anomalies détectées et les KPI qualité par site, à partir de données SAP.

```text
                SAP Supplier Master
                         │
                         ▼
              Dataset 1 : Raw Suppliers
                         │
          ┌──────────────┴──────────────┐
          ▼                             ▼
Dataset 2 :                 Dataset 3 :
Clean Suppliers             Supplier Addresses
          │                             │
          └──────────────┬──────────────┘
                         ▼
             Dataset 4 : Golden Supplier
                         │
          ┌──────────────┴──────────────┐
          ▼                             ▼
Dataset 5 :                 Dataset 6 :
DQ Checks                  Supplier KPIs
          │                             │
          └──────────────┬──────────────┘
                         ▼
              Data Product Dashboard
```

1. **Ingestion** — `SAP → Raw Suppliers`
2. **Nettoyage** — `Raw Suppliers → Clean Suppliers` (doublons, standardisation pays/formats)
3. **Enrichissement** — `Clean Suppliers + Supplier Addresses → Golden Supplier`
4. **Contrôles qualité** — `Golden Supplier → DQ Checks` (nom, adresse, code postal, pays)
5. **Calcul KPI** — `Golden Supplier → Supplier KPIs` (Fill Rate 96 %, 128 anomalies, 0,8 % doublons)
6. **Dashboard** — `DQ Checks + Supplier KPIs → Dashboard`

Quand un nouveau fichier SAP arrive, le système sait automatiquement que le changement de `Raw Suppliers` doit se propager en cascade : `Clean Suppliers → Golden Supplier → DQ Checks → KPIs → Dashboard`, recalculés dans le bon ordre.

Cet exemple (contrôles qualité + KPI + dashboard) recoupe directement les scripts pandas/PySpark déjà documentés dans [[Code — DQ pandas]] et [[Code — DQ PySpark]] : chaque fonction de contrôle qualité (complétude, unicité, conformité, fraîcheur) correspond à un nœud du DAG (`DQ Checks`) alimenté par un dataset amont nettoyé/enrichi.

## Liens

- [[Ontologie Palantir]] — le DAG/lineage s'inscrit dans le pilier Data de l'Ontologie
- [[Architecture Foundry — Couches]] — Data Sources / Logic Sources comme couches sous-jacentes aux pipelines
- [[Data Engineering]] — orchestration, pipelines, outils (Spark, Airflow, dbt)
- [[Code — DQ pandas]] / [[Code — DQ PySpark]] — implémentation des contrôles qualité qu'un DAG orchestrerait
