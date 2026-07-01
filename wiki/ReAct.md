---
type: concept
tags: [agent, planification, ReAct, raisonnement, LLM]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[Building a ReAct Agent from Scratch]]"
---

# ReAct (Reason + Act)

Technique de [[Planification (agents IA)|planification]] centrale du Planning Pattern (Lesson 3). Combine raisonnement et action en boucle.

## Boucle ReAct

Chaque itération contient 3 phases :

| Phase | Description |
|---|---|
| **Thought** | Le LLM réfléchit à l'action suivante |
| **Action** | Le LLM appelle un [[Outils (agents IA)|outil]] |
| **Observation** | Le LLM lit le résultat de l'outil et décide de la suite |

La boucle s'arrête quand le LLM génère une balise `<response>` (réponse finale sans appel d'outil).

## Implémentation

- System prompt spécifique décrivant les 3 opérations autorisées
- Balises XML pour structurer les messages : `<thought>`, `<tool_call>`, `<observation>`, `<response>`
- Les résultats d'outils sont ajoutés au `chat_history` comme `<observation>`

## Exemple

```
Question: "1234 + 5678, puis × 5, puis log ?"
→ Thought: utiliser add_tool
→ Action: add(1234, 5678) → 6912
→ Observation: 6912
→ Thought: utiliser multiply_tool
→ Action: multiply(6912, 5) → 34560
→ Observation: 34560
→ Thought: utiliser log_tool
→ Action: log(34560) → 10.45
→ Response: "Le résultat est 10.45"
```

## Différence avec le Tool Pattern

Le [[Tool Pattern]] fait un seul appel d'outil. ReAct enchaîne plusieurs outils en séquence, avec du raisonnement entre chaque étape.

## Librairie

Classe `ReactAgent` dans [agentic_patterns](https://github.com/neural-maze/agentic_patterns/blob/main/src/agentic_patterns/planning_pattern/react_agent.py).
