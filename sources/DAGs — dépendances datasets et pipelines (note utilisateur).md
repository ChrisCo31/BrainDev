La phrase :
"The system explicitly manages dependencies between datasets and pipelines (DAGs)"
signifie que :
Le système connaît et gère explicitement les relations de dépendance entre les jeux de données (datasets) et les traitements (pipelines). Cela est généralement représenté sous la forme d'un DAG (Directed Acyclic Graph), c'est-à-dire un graphe orienté sans cycle. (source : Viva Learning — "Create Com...he Airflow", https://learning.cloud.microsoft/detail/d6423504-6578-4da9-8886-d2290d1a1aa9)

## Décomposition

### Dataset
Un dataset est une donnée produite ou consommée par un traitement.
Exemple :
- Dataset A = données SAP brutes
- Dataset B = données nettoyées
- Dataset C = indicateurs qualité

### Pipeline
Un pipeline est une suite d'étapes de transformation :
```
SAP Extract
      ↓
Nettoyage
      ↓
Enrichissement
      ↓
Calcul KPI
```

### Dépendance
Une dépendance signifie qu'une étape ne peut être exécutée que si les données dont elle dépend sont disponibles.
Par exemple :
```
Dataset A
    ↓
Pipeline 1
    ↓
Dataset B
    ↓
Pipeline 2
    ↓
Dataset C
```
Ici :
- Pipeline 2 dépend de Dataset B.
- Dataset B dépend de Pipeline 1.
- Si Pipeline 1 échoue, Pipeline 2 ne pourra pas démarrer.

## Qu'est-ce qu'un DAG ?

Le DAG représente ces dépendances sous forme de graphe :
```
Raw SAP Data
      ↓
Ingestion
      ↓
Standardized Data
     ↙      ↘
Quality      Reporting
Checks       Metrics
     ↓           ↓
DQ Dashboard  KPI Dashboard
```

Le système maintient automatiquement ce graphe et sait :
- quel dataset dépend de quel autre ;
- quels pipelines doivent être recalculés lorsqu'une donnée change ;
- dans quel ordre exécuter les traitements ;
- quels impacts aura la suppression ou la modification d'un dataset.

## Pourquoi est-ce important dans un Data Product ?

Dans un environnement de Data Product (Foundry, Databricks, Data Fabric, etc.) :
- traçabilité (lineage) : on sait d'où provient chaque donnée ;
- automatisation : un changement en amont déclenche les recalculs nécessaires ;
- analyse d'impact : avant de modifier un dataset, on connaît tous les consommateurs affectés ;
- fiabilité : les traitements sont exécutés dans le bon ordre.

## Version courte (type certification)

"The system explicitly manages dependencies between datasets and pipelines (DAGs)" signifie que la plateforme maintient automatiquement un graphe de dépendances entre les données et les traitements afin de garantir l'ordre d'exécution, la traçabilité et l'analyse d'impact des changements.

Dans Palantir Foundry, cette notion est très proche du data lineage, où chaque dataset connaît ses producteurs, ses consommateurs et les transformations qui le relient au reste du paysage de données.

## Exemple concret : Data Product Qualité des données fournisseurs Airbus

### Contexte métier

L'objectif est de construire un Data Product permettant de suivre :
- le taux de complétude des fournisseurs ;
- les anomalies détectées ;
- les KPI qualité par site.

Les données proviennent de SAP.

### DAG simplifié

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

### Lecture du DAG

**Étape 1 — Ingestion** : `SAP → Raw Suppliers`. Le pipeline récupère les données SAP et crée le dataset brut.

| Supplier ID | Name       | Country |
| ----------- | ---------- | ------- |
| S001        | Airbus SAS | FR      |
| S002        | XYZ Ltd    | UK      |

**Étape 2 — Nettoyage** : `Raw Suppliers → Clean Suppliers`. Le pipeline supprime les doublons, standardise les pays, corrige les formats (`France` / `FRANCE` / `FRA` → `FR`).

**Étape 3 — Enrichissement** : `Clean Suppliers + Supplier Addresses → Golden Supplier`. On combine les informations fournisseurs et adresses pour obtenir une vue consolidée.

**Étape 4 — Contrôles qualité** : `Golden Supplier → DQ Checks`. Calcul des règles (nom renseigné, adresse renseignée, code postal valide, pays conforme).

| Supplier | Status |
| -------- | ------ |
| S001     | OK     |
| S002     | NOK    |

**Étape 5 — Calcul des KPI** : `Golden Supplier → Supplier KPIs`. Fill Rate = 96 %, nombre d'anomalies = 128, taux de doublons = 0,8 %.

**Étape 6 — Dashboard** : `DQ Checks + Supplier KPIs → Dashboard`. Affiche les KPI, les anomalies, les tendances.

### Pourquoi un DAG est utile ?

Imaginons qu'un nouveau fichier SAP arrive. Le système sait automatiquement que :
```text
Raw Suppliers a changé
       ↓
Clean Suppliers doit être recalculé
       ↓
Golden Supplier doit être recalculé
       ↓
DQ Checks doit être recalculé
       ↓
KPIs doivent être recalculés
       ↓
Dashboard mis à jour
```

Le DAG permet donc : l'exécution automatique dans le bon ordre, la traçabilité (lineage), l'analyse d'impact, l'orchestration des pipelines.

### Vision Foundry / Certification

Dans Palantir Foundry, chaque bloc du DAG est généralement un **Dataset** (Raw Suppliers, Golden Supplier, KPIs...) relié par une **Transformation Pipeline** (Code Repository, Pipeline Builder ou Contour).

La plateforme construit automatiquement ce graphe de dépendances et peut répondre à la question : « Si je modifie le dataset Supplier Master, quels datasets, KPI et dashboards seront impactés ? » C'est exactement la puissance du lineage basé sur un DAG.
