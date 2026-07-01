---
type: concept
tags: [palantir, foundry, architecture, data-sources, MMDP, OSDK]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[2.1 Data Sources]]"
  - "[[2.2 Logic Sources]]"
  - "[[2.3 Systems of Action]]"
  - "[[3.1 Analytics & Workflows]]"
  - "[[3.2 Automations & Progressive Automation]]"
  - "[[3.3 Products & SDKs]]"
---

# Architecture Foundry — Couches

Voir [[Palantir Foundry & AIP — Vue d'ensemble]] pour le schéma global.

## Ce qui ALIMENTE l'Ontologie (dessous)

### 2.1 Data Sources
- **+300 connecteurs natifs** (SAP, Salesforce, ServiceNow, legacy...)
- **MMDP (Multimodal Data Plane)** : "any storage" — Virtual Tables pour accéder aux données dans Snowflake, Databricks, BigQuery **sans les copier** (zero-copy federation)
- **Pushdown compute** : le calcul va là où les données sont, pas l'inverse
- Standards ouverts : Apache Iceberg, Parquet, CSV, REST, JDBC, S3

### 2.2 Logic Sources
- Des règles simples (spreadsheet, SOPs) aux modèles ML sophistiqués
- Containerisation Docker pour intégrer du code externe
- MMDP pour le compute : frameworks de build agnostiques (mix de runtimes tiers)

### 2.3 Systems of Action
- **Écrire les décisions dans les systèmes opérationnels** (ERP, SCM...)
- Actions gouvernées (permissions) et auditables (traçabilité complète)

## Ce qui est CONSTRUIT sur l'Ontologie (dessus)

### 3.1 Analytics & Workflows
- Dashboards temps réel sur les business objects vivants (pas des rapports statiques)
- Workflows cross-fonctionnels : une décision peut traverser supply chain, production, livraison
- Low-code / no-code / pro-code

### 3.2 Automations
Voir [[AI Labor et Progressive Automation]]

### 3.3 Products & SDKs — OSDK
- **Ontology SDK (OSDK)** : SDK généré automatiquement depuis l'Ontologie
- Accès programmatique en React, Python, Java, OpenAPI depuis n'importe quel environnement externe
- L'Ontologie devient le "SDK de ton business" — tes outils existants ne sont pas remplacés, ils sont enrichis
- Webhooks, streaming/batch connectors, external transforms
