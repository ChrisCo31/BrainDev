---
type: summary
tags: [palantir, foundry, AIP, architecture, overview]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[Course Overview]]"
  - "[[High-Level Architecture of the Platform]]"
  - "[[The Enterprise Autonomy Vision]]"
---

# Palantir Foundry & AIP — Vue d'ensemble

## Vision

Foundry et AIP ne sont pas un data lake ou une ML platform — c'est un **système d'exploitation décisionnel** : data → décisions → actions → feedback. Des milliers d'agents IA maniant des milliers d'outils, dans les contextes les plus exigeants (hôpitaux, chantiers navals, défense).

## Architecture centrale

L'**[[Ontologie Palantir|Ontologie]]** est au cœur de tout. Autour d'elle :

```
Sources (Data + Logic + Systems of Action)
        ↓
   [ ONTOLOGIE ]
        ↓
Capabilities (Analytics, Automations, Products)
        ↓
Couches transversales : Sécurité & Gouvernance | Apollo | Branching
```

## Concept clé : AI Infrastructure

Le fossé entre un modèle frontier (GPT, Claude...) et un résultat enterprise est énorme. Foundry/AIP comble ce fossé :
- Le modèle = le **cerveau** de l'agent
- L'Ontologie = le **corps** (contexte métier, données, actions gouvernées)

> "If a frontier model is the brain of your Enterprise Agents, Ontology is its body."

## Liens

- [[Ontologie Palantir]] — concept central
- [[AI Labor et Progressive Automation]] — stratégie d'automatisation
- [[Sécurité et Gouvernance Foundry]] — contrôles purpose-based, audit trails
- [[Apollo Palantir]] — couche de déploiement
