# BrainDev — second brain

Système de wiki personnel inspiré de https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f

## Structure

- `sources/` — documents bruts (notes, articles, transcripts...). Jamais modifiés une fois ajoutés.
- `wiki/` — pages markdown maintenues par Claude (entities, concepts, summaries, overviews).
- `index.md` — catalogue de toutes les pages du wiki, avec lien, résumé d'une ligne, dates, sources.
- `log.md` — journal chronologique append-only de chaque action.

## Conventions

- Wikilinks Obsidian : `[[Nom de la page]]`
- Frontmatter YAML en tête de chaque page wiki : `type`, `tags`, `created`, `updated`, `sources`
- Types de pages : `entity`, `concept`, `summary`, `overview`
- Une entrée de log par action, format : `## [YYYY-MM-DD] <ingest|query|lint> | Titre`

## Workflows

### Ingest
Quand une nouvelle source arrive dans `sources/` :
1. Lire le document, extraire les infos clés
2. Créer ou mettre à jour les pages wiki concernées dans `wiki/`
3. Mettre à jour `index.md`
4. Ajouter une entrée dans `log.md`
5. Signaler toute contradiction détectée avec le wiki existant

### Query
Quand l'utilisateur pose une question :
1. Consulter `index.md` pour localiser les pages pertinentes
2. Synthétiser une réponse avec citations des pages wiki
3. Si l'exploration produit des infos nouvelles utiles, les archiver comme nouvelle page wiki
4. Logger la requête dans `log.md`

### Lint
Sur demande, vérifier : contradictions entre pages, infos obsolètes, pages orphelines (non liées depuis `index.md`), concepts mentionnés mais sans page dédiée.
