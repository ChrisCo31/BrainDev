---
type: concept
tags: [agent, pattern, réflexion, LLM]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[Reflection Pattern_ When Agents think twice]]"
---

# Reflection Pattern

Premier pattern agentic (Lesson 1 de la série). Le plus simple, mais avec des gains de performance surprenants.

## Principe

L'[[Agent IA]] évalue et critique ses propres outputs pour les améliorer de façon itérative.

## Boucle de réflexion (4 étapes)

1. **Génération** — le LLM produit un premier output
2. **Réflexion** — le LLM critique cet output (via un second prompt/rôle)
3. **Amélioration** — le LLM intègre les critiques et produit une version améliorée
4. **Itération** — recommence jusqu'au critère d'arrêt

## Critères d'arrêt

- Nombre fixe d'itérations
- Le LLM génère une séquence stop ("OK", "Correct", etc.)

## Implémentation

Deux historiques de conversation séparés :
- `generation_chat_history` — le "générateur" (ex: développeur Python)
- `reflection_chat_history` — le "critique" (ex: Andrej Karpathy)

Le critique reçoit l'output du générateur → génère du feedback → ce feedback est réinjecté dans l'historique du générateur.

## Librairie

```bash
pip install -U agentic-patterns
```

Classe `ReflectionAgent` disponible dans [agentic_patterns](https://github.com/neural-maze/agentic_patterns).
