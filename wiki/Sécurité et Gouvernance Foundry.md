---
type: concept
tags: [palantir, foundry, sécurité, gouvernance, audit]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[Security & Governance Layer]]"
  - "[[Branching_ Safe Change Management for Human and AI Builders]]"
  - "[[The Three Pillars of Decision-Centric Systems for Enterprise Automation]]"
---

# Sécurité et Gouvernance Foundry

Couche transversale qui s'applique à **toute** la plateforme — pas un add-on, tissé dans chaque interaction.

## Au-delà du RBAC classique

Foundry va plus loin que "user X a accès à ressource Y" :

| Mécanisme | Description |
|---|---|
| **Purpose-based controls** | L'accès dépend du *pourquoi* (compliance analyst ≠ marketing analyst sur les mêmes données clients) |
| **Data markings & classifications** | Les labels de sensibilité voyagent avec la donnée à travers chaque transformation, dashboard, automation — ils ne sont jamais perdus |
| **Active controls** | Politiques appliquées dynamiquement selon le contexte, pas juste des permissions statiques |

## Audit trails — critique pour l'IA

Dans un monde où des agents IA agissent aux côtés des opérateurs humains, les **logs complets** (qui a fait quoi, pourquoi) sont la fondation de la confiance.

> "You can't confidently expand AI autonomy if you can't see exactly what the AI did and why."

## Branching — gouvernance du changement

Quand humains ET agents IA construisent sur la même plateforme, le **branching global Foundry** protège la production :
- Aucun changement ne passe en prod sans review
- Contrôles runtime = ce qu'agents et users *peuvent faire* en prod
- Branching = ce qui *arrive* en prod

## Lien

- [[AI Labor et Progressive Automation]] — les evals + audit trails = chemin vers plus d'autonomie
- [[Apollo Palantir]] — déploiement sécurisé dans tout environnement
