---
type: concept
tags: [palantir, ontologie, primitives, foundry]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "note utilisateur — définition primitive Palantir"
---

# Primitives Ontologie Palantir

Les **primitives** sont les blocs de construction fondamentaux et indivisibles de l'Ontologie Palantir. Les "briques Lego" à partir desquelles on modélise toute la représentation métier de l'entreprise.

## Les 5 (+1) primitives officielles

| Primitive | Nature | Rôle |
|---|---|---|
| **Object Type** | "Nom" | Type d'entité métier réelle (Aircraft, Employee, Order) |
| **Property** | Attribut | Caractérise un objet (Aircraft.serialNumber, Employee.email) |
| **Link Type** | Relation | Relation dirigée entre 2 object types (Order → contains → LineItem) |
| **Action Type** | "Verbe" | Transaction gouvernée qui modifie l'Ontologie (Approve PO, Dispatch Technician) |
| **Function** | Logique | Code serveur réutilisable opérant sur les objets |
| **Interface** *(récent)* | Abstraction | Structure polymorphe : plusieurs Object Types partagent un comportement commun |

## Ce qui caractérise une primitive

1. **Élémentaire** — ne se décompose pas en éléments plus petits de l'Ontologie
2. **Standardisée** — schéma défini, exploitable par tous les outils (Workshop, AIP Logic, OSDK)
3. **Gouvernée** — soumise aux règles de sécurité, permissions et audit de la plateforme
4. **Composable** — combinable avec d'autres primitives pour construire workflows, apps, agents IA
5. **Sémantiquement porteuse** — chaque primitive porte du sens métier, pas seulement de la donnée technique

## Analogie : les primitives comme grammaire

Si l'Ontologie était une **langue**, les primitives seraient sa **grammaire de base** :

- Object Type = les *noms*
- Property = les *adjectifs*
- Link Type = les *prépositions / conjonctions*
- Action Type = les *verbes*
- Function = les *règles grammaticales*

Ensemble, elles permettent de "raconter" l'entreprise dans un langage compréhensible par les humains, les applications **et** les agents IA.

## Liens

- [[Ontologie Palantir]] — cadre général : Data + Logic + Action
- [[Architecture Foundry — Couches]] — où les primitives s'inscrivent dans la stack
