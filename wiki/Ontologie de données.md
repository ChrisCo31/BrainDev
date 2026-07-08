---
type: concept
tags: [ontologie, données, data-engineering, gouvernance, RDF, OWL]
created: 2026-07-08
updated: 2026-07-08
sources:
  - "[[Ontologie de données (note utilisateur)]]"
  - "Data_scripts (ChrisCo31/Data_scripts) — docs/Ontologie_donnees.md"
---

# Ontologie de données

Concept générique (indépendant de tout vendeur) : une ontologie est le **modèle métier** qui décrit les objets d'un domaine (Client, Produit, Commande, Capteur, Projet…), leurs attributs et leurs relations, de façon formelle et partageable par humains et machines.

## Définition

- Carte conceptuelle formelle d'un domaine : vocabulaire, définitions, liens, règles.
- Va au-delà d'un glossaire : structure concepts, propriétés et relations (`est_un`, `fait_partie_de`, etc.).
- Exploitable par systèmes d'IA, moteurs de requêtes sémantiques, graphes de connaissances.

## Composants

| Bloc | Contenu |
|---|---|
| **Concepts / classes** | entités métier (Client, Produit, Capteur, Projet, Usine…) |
| **Propriétés / attributs** | caractéristiques (nom, prix, date, statut, géolocalisation…) |
| **Relations** | liens entre concepts (Produit *vendu_à* Client, Capteur *installé_dans* Usine) |
| **Hiérarchies** | relations `est_un` (Client B2B *est_un* Client) |
| **Contraintes / règles** | cardinalités, domaines de valeurs, règles métier pour l'inférence |

Dépendances techniques typiques : RDF/OWL pour la représentation, triple stores / graph DB pour le stockage, mapping ontologie ↔ tables/vues du data lake (OneLake, BigQuery…).

## Construction → connexion → exploitation

1. **Construction** : objectifs (reporting, IA, intégration, gouvernance), collecte (modèles existants, MCD, experts métier), modélisation (RDF/OWL ou outil dédié).
2. **Connexion aux données** : mapper chaque concept à des sources réelles (tables, vues, objets, événements) via des *bindings*, définir comment les propriétés sont alimentées.
3. **Exploitation** : requêtes orientées concepts (plutôt que schémas physiques), interopérabilité entre systèmes, base de connaissances pour l'IA (inférence, recommandation, détection de fraude).

## Enjeux

- **Bénéfices** : gouvernance et qualité (vocabulaire maîtrisé), interopérabilité entre systèmes, FAIRification des données, contexte riche pour l'IA.
- **Risques** : complexité si trop ambitieuse, faible adoption sans implication métier, besoin de versioning à mesure que le business évolue.

## Rapport avec l'Ontologie Palantir

[[Ontologie Palantir]] est une **implémentation concrète** de ce concept générique : Foundry structure son Ontologie en 3 piliers (Data / Logic / Action) via des [[Primitives Ontologie Palantir|primitives]] (Object Type, Property, Link Type, Action Type, Function, Interface) — l'équivalent produit des blocs génériques ci-dessus (concepts, propriétés, relations, règles).

## Cas d'usage AWS/GCP/Foundry/Fabric

- Data lake/lakehouse : ontologie du domaine « Commercial » (Client, Contrat, Produit, Offre, Commande) mappée à BigQuery/Redshift/Foundry.
- Hub & Spoke : couche commune de modélisation métier entre domaines (spokes) pour aligner modèles et APIs.
- Catalog / gouvernance : exposer l'ontologie dans un catalogue (MyDataCatalogue, Fabric, Foundry) pour la découverte d'assets.
