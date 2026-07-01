---
type: concept
tags: [palantir, ontologie, foundry, AIP, données]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[What is the Ontology_]]"
  - "[[The Three Pillars of Decision-Centric Systems for Enterprise Automation]]"
  - "[[The Ontology as the Shared World for Human + AI Teams]]"
  - "[[High-Level Architecture of the Platform]]"
---

# Ontologie Palantir

> "Si tu ne retiens qu'une chose de ce cours : l'Ontologie est la pièce centrale de toute la plateforme."

## Définition

L'Ontologie = les **noms et verbes** qui composent ton business. Elle modélise le monde réel (pas les contraintes IT) :
- Manufacturing : plants, warehouses, suppliers, customers, shipments, orders
- Healthcare : patients, providers, appointments, prescriptions, facilities

C'est une **couche sémantique** qui traduit des données multi-systèmes brutes en langage métier compréhensible par tous.

## Les 3 piliers

| Pilier | Question | Contenu |
|---|---|---|
| **Data** | Qu'est-ce qui EST ? | Toutes les modalités (structured, unstructured, time series, geospatial) unifiées en business objects |
| **Logic** | Comment PENSER ? | Règles métier, ML models, optimizers, compute traditionnel |
| **Action** | Qu'est-ce qu'on peut FAIRE ? | Créer une commande SAP, ajuster un planning, signaler un incident — tout gouverné |

## Audiences simultanées

- **Humains** — langage métier familier (le manager voit "Warehouse X — 42 jours de stock", pas une table SAP)
- **Agents IA** — contexte métier pour raisonner (le LLM comprend qu'un warehouse est connecté à des plants, avec des lead times, et des actions disponibles)
- **Systèmes** — source de vérité unique, élimine la fragmentation
- **Gouvernance** — visibilité complète sur les données, accès, actions (humaines et IA)

## Lien avec les agents IA

Sans Ontologie, un LLM frontier n'a pas le contexte de ton business. L'Ontologie est ce qui transforme un modèle générique en agent capable d'opérer dans ton environnement spécifique. Voir [[AI Labor et Progressive Automation]].
