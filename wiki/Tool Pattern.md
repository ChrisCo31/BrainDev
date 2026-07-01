---
type: concept
tags: [agent, pattern, tools, function-calling, LLM]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[Agent Tools_ The bridge to the outside world]]"
---

# Tool Pattern

Deuxième pattern agentic (Lesson 2). Les [[Outils (agents IA)|outils]] permettent à l'agent d'interagir avec le monde extérieur.

## Principe

Un outil = une fonction Python que le LLM peut appeler. Le LLM ne "voit" pas le code — il reçoit la **signature de la fonction** dans son system prompt (nom, paramètres, description), et décide de l'appeler.

## Workflow

1. Les signatures des outils disponibles sont injectées dans le system prompt (dans des balises XML `<tools>`)
2. Le LLM génère un `<tool_call>` avec le nom de la fonction et les arguments
3. Le code parse le `<tool_call>`, exécute la fonction réelle
4. Le résultat est ajouté au `chat_history` comme contexte
5. Le LLM génère la réponse finale avec ce contexte

## Tool Decorator

Un décorateur Python `@tool` transforme automatiquement n'importe quelle fonction en objet `Tool` (avec signature auto-générée). Même approche que LangChain, CrewAI, LlamaIndex.

```python
@tool
def fetch_top_hacker_news_stories(n: int) -> str:
    """Fetches the top n stories from Hacker News."""
    ...
```

## Limitation du Reflection Pattern résolue

Le [[Reflection Pattern]] ne peut pas accéder à des infos fraîches (cours boursiers, météo temps réel). Le Tool Pattern comble ce manque.

## Librairie

Classe `ToolAgent` dans [agentic_patterns](https://github.com/neural-maze/agentic_patterns).
