# Index

Catalogue des pages du wiki. Mis à jour à chaque ingest.

## Entities

## Concepts

- [[Agent IA]] — LLM + planification + mémoire + outils = agent. (créé 2026-07-01)
- [[Planification (agents IA)]] — CoT, CoT-SC, Tree of Thoughts, ReAct. (créé 2026-07-01)
- [[Mémoire (agents IA)]] — court terme (contexte) vs long terme (bases vectorielles : Qdrant, Weaviate, Pinecone). (créé 2026-07-01)
- [[Outils (agents IA)]] — APIs, bases de données, recherche web — ce qui rend un agent actif. (créé 2026-07-01)
- [[Reflection Pattern]] — boucle génération/critique/amélioration pour raffiner les outputs LLM. (créé 2026-07-01)
- [[Tool Pattern]] — function calling via signatures XML dans le system prompt, décorateur @tool. (créé 2026-07-01)
- [[ReAct]] — Reason+Act : boucle Thought/Action/Observation pour planification multi-étapes. (créé 2026-07-01)
- [[Multiagent Pattern]] — agents spécialisés orchestrés en DAG, inspiré CrewAI + Airflow. (créé 2026-07-01)
- [[Ontologie de données]] — modèle métier formel générique : concepts, propriétés, relations, RDF/OWL, gouvernance. (créé 2026-07-08)
- [[DAG (dépendances datasets & pipelines)]] — graphe de dépendances datasets/pipelines, lineage, analyse d'impact, exemple Data Product Airbus. (créé 2026-07-08)
- [[Pandas vs PySpark — performance Foundry]] — pourquoi `toPandas()` sur un gros dataset risque un `Serialized results too large`, agréger en Spark natif. (créé 2026-07-09)

## Summaries

- [[Palantir Foundry & AIP — Vue d'ensemble]] — architecture globale, vision enterprise autonomy, AI Infrastructure. (créé 2026-07-01)

## Overviews

- [[Data Engineering]] — pipelines, orchestration, qualité de données, outils. (créé 2026-06-30)
- [[Cloud (AWS & GCP)]] — services managés, architecture, IAM, comparatif AWS/GCP. (créé 2026-06-30)
- [[Palantir]] — Foundry, ontologie, pipelines, cas d'usage. (créé 2026-06-30)
- [[IA]] — LLMs, agents, patterns agentiques (Reflection, Tool, ReAct, Multiagent). (créé 2026-07-01)

## Code

- [[Code — DQ pandas]] — scripts qualité de données pandas : profilage, nulls, complétude, unicité, conformité, cohérence, fraîcheur, dashboard. (créé 2026-07-01)
- [[Code — DQ PySpark]] — scripts qualité de données PySpark natif Foundry : logic/ + transforms/, profilage, nulls, sentinelles, doublons. (créé 2026-07-01)

## Palantir — Concepts détaillés

- [[Ontologie Palantir]] — couche sémantique centrale : Data + Logic + Action, monde partagé humains + IA. (créé 2026-07-01)
- [[AI Labor et Progressive Automation]] — 3ème catégorie d'effort, phases d'autonomie, AIP Evals. (créé 2026-07-01)
- [[Architecture Foundry — Couches]] — Data Sources (MMDP), Logic, Systems of Action, Analytics, OSDK. (créé 2026-07-01)
- [[Sécurité et Gouvernance Foundry]] — purpose-based controls, data markings, audit trails, branching. (créé 2026-07-01)
- [[Apollo Palantir]] — déploiement cloud/on-prem/edge/air-gapped. (créé 2026-07-01)
- [[Primitives Ontologie Palantir]] — Object Type, Property, Link Type, Action Type, Function, Interface — briques de l'Ontologie. (créé 2026-07-01)

## GCP — Concepts détaillés

- [[Hiérarchie des ressources GCP]] — organization → folders → projects, héritage des policies IAM top-down. (créé 2026-07-17)
