---
type: concept
tags: [agent, multiagent, orchestration, CrewAI, LLM]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[What is a Multiagent system_]]"
---

# Multiagent Pattern

Quatrième et dernier pattern agentic (Lesson 4). Une tâche complexe est découpée en sous-tâches, chacune confiée à un [[Agent IA]] spécialisé.

## Principe

Plusieurs agents collaborent, chacun avec un rôle défini (ex: Poet Agent, Translator Agent, Writer Agent). L'output d'un agent devient le contexte d'entrée du suivant.

## Concepts clés (inspirés de CrewAI + Airflow)

| Concept | Analogie Airflow | Description |
|---|---|---|
| **Agent** | Task | Unité autonome avec un rôle et des outils. Utilise [[ReAct]] en interne. |
| **Crew** | DAG | Orchestre les agents dans le bon ordre |

## Dépendances entre agents

```python
agent_1 >> agent_2  # agent_2 dépend de agent_1
```

L'output de `agent_1` est automatiquement injecté dans le contexte de `agent_2`.

## Orchestration

La `Crew` utilise un **tri topologique** pour exécuter les agents dans le bon ordre en respectant les dépendances. `crew.plot()` génère un graphe de dépendances.

```python
crew.run()  # exécute tous les agents dans l'ordre correct
```

## Frameworks équivalents

- **CrewAI** — Crew + Agents avec rôles
- **AutoGen** (Microsoft) — agents conversationnels
- **OpenAI Swarm** — orchestration légère
- **Apache Airflow** — DAG d'orchestration (inspiration architecturale)

## Librairie

Classe `Agent` et `Crew` dans [agentic_patterns](https://github.com/neural-maze/agentic_patterns).
