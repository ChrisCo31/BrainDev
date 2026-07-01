---
type: concept
tags: [palantir, AIP, automation, agents, AI-labor]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[AI Labor_ A New Category]]"
  - "[[3.2 Automations & Progressive Automation]]"
  - "[[The Role of Evals in Progressive Automation]]"
  - "[[Common Foundation for Human + AI Collaboration]]"
  - "[[The Evolution from Linear to Decision-Centric Systems]]"
---

# AI Labor et Progressive Automation

## AI Labor : une 3ème catégorie

Traditionnellement, deux types d'effort en enterprise :
1. **Effort humain** — jugement, décision, adaptation
2. **Computation traditionnelle** — databases, rule engines, jobs schedulés

L'IA introduit une **3ème catégorie** : l'**AI Labor** — ni simple compute, ni remplacement du jugement humain. Un pont conceptuel entre les deux.

En pratique, chaque workflow est un **mix dynamique** des trois. Ce mix évolue dans le temps.

## Progressive Automation

L'automatisation ne se fait pas d'un coup. Elle progresse par phases :

```
Human-led → AI-assisted → AI-led (supervised) → Autonomous
```

Chaque transition de phase requiert des **preuves** via [[AIP Evals]].

## AIP Evals

Mécanisme structurel pour valider les transitions d'autonomie :
- Suites d'évaluation complètes avant chaque montée en autonomie
- Mesure les performances IA vs décisions humaines de qualité équivalente
- Monitoring continu pour détecter les dégradations

> "You can't move from human-led to AI-led on faith — you need evidence."

## Automations dans Foundry

Les agents AIP ne sont **pas des outils autonomes externes** — ils sont **embarqués dans les workflows métier**, avec accès à la même logique et aux mêmes canaux d'action gouvernés qu'un opérateur humain.

Pattern puissant : LLM + modèle déterministe en chaîne (ex : LLM parse un rapport maintenance → appelle un modèle de prédiction de panne → propose une action).

## Lien

- [[Ontologie Palantir]] — fondation partagée humains + IA
- [[Sécurité et Gouvernance Foundry]] — audit trails = confiance pour augmenter l'autonomie
