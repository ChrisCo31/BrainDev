---
type: concept
tags: [agent, mémoire, vector-database, LLM]
created: 2026-07-01
updated: 2026-07-01
sources:
  - "[[What is an Agent_]]"
---

# Mémoire (agents IA)

Composant clé d'un [[Agent IA]] — permet de "se souvenir" des interactions passées pour planifier et agir.

## Court terme

Le contexte window du LLM. Limité en taille, perdu à la fin de la session.

## Long terme

Mécanisme pour retenir de l'information sur une durée potentiellement infinie, et accéder à des infos absentes des poids du modèle.

**Solutions courantes — bases vectorielles :**
- Qdrant
- Weaviate
- Pinecone

Les bases vectorielles stockent des embeddings et permettent une recherche sémantique rapide.
