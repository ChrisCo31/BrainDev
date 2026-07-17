---
type: concept
tags: [gcp, cloud, iam, resource-manager, gouvernance]
created: 2026-07-17
updated: 2026-07-17
sources: ["[[Cloud Platform Resource Hierarchy (GCP)]]"]
---

# Hiérarchie des ressources GCP

Structure arborescente qui organise toutes les ressources Google Cloud. Toute ressource, sauf la racine, a exactement un parent. Sert de point d'ancrage pour l'héritage des policies IAM et des organization policies.

## Les 3 niveaux

1. **Organization** (racine) — représente une entité (ex. une entreprise). Nécessite un compte Google Workspace ou Cloud Identity (association 1:1). Créée automatiquement quand un utilisateur Workspace/Cloud Identity crée un premier project. Attributs : ID unique, display name (dérivé du domaine), owner (Workspace customer ID, immuable).
2. **Folder** (optionnel) — regroupement entre organization et projects, nécessite une organization. Modélise départements/équipes/applications, peut s'imbriquer (folders dans folders). Sert de frontière d'isolation et de point d'héritage.
3. **Project** — entité fondamentale, obligatoire pour utiliser Google Cloud (APIs, billing, IAM, ressources de service comme VM Compute Engine ou buckets de stockage). Identifié par un `projectId` (choisi) et un `projectNumber` (auto-généré, read-only).

Un project peut ne pas avoir d'organization au-dessus (cas des comptes free trial/free tier) et être migré plus tard dans une organization.

## Héritage des policies (le point clé)

- Les IAM roles et organization policies appliqués à un niveau (organization ou folder) sont **hérités par toutes les ressources enfants**.
- Exemple : accorder le rôle **Network Admin** une seule fois au niveau organization plutôt que projet par projet.
- La policy effective d'une ressource = policies appliquées directement + policies héritées de tous ses ancêtres (allow, deny, organization policies).
- Le project créé reçoit par défaut le rôle **Owner** pour son créateur ; l'organization reçoit par défaut les rôles **Project Creator** et **Billing Account Creator** pour tout le domaine.

## Pourquoi structurer ainsi (Ownership)

Lier le cycle de vie d'une ressource à son parent immédiat évite les "projets fantômes" (shadow projects) : un project appartient à l'organization, pas à l'employé qui l'a créé. Si l'employé part, le project reste actif et sous contrôle. Les administrateurs d'organization gèrent centralement toutes les ressources.

## Règles de création de projects (utilisateurs managés)

Une fois une organization créée pour un domaine : les utilisateurs managés (membres du domaine) doivent créer leurs projects **dans** l'organization — impossible d'en créer en dehors. Le Google Workspace super admin a pour rôle principal d'assigner le rôle **Organization Administrator** aux bonnes personnes (séparation admin Workspace / admin Google Cloud).

## Liens

- [[Cloud (AWS & GCP)]] — page overview cloud
- [[Sécurité et Gouvernance Foundry]] — comparer avec le modèle de gouvernance Palantir Foundry (purpose-based controls, data markings) : GCP hérite des rôles top-down par arborescence de ressources, Foundry ajoute des contrôles purpose-based par-dessus les permissions.
