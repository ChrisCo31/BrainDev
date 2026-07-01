---
type: concept
tags: [agent, planification, raisonnement, LLM]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[What is an Agent_]]"
---

# Planification (agents IA)

Capacité d'un [[Agent IA]] à décomposer des tâches complexes en sous-tâches, gérer des sous-objectifs et s'auto-critiquer.

## Techniques principales

| Technique | Description |
|---|---|
| **Chain of Thought (CoT)** | Raisonnement étape par étape explicite |
| **CoT-SC** | CoT avec self-consistency (plusieurs chemins, vote majoritaire) |
| **Tree of Thoughts (ToT)** | Exploration arborescente de plusieurs raisonnements en parallèle |
| **ReAct** | Alternance Raisonnement + Action (voir Lesson 3 de la série) |

ReAct est considéré comme l'une des techniques les plus importantes — il combine raisonnement et action en boucle.
