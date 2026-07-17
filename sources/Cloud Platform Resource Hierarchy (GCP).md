---
source_url: https://docs.cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy?hl=fr
type: source
added: 2026-07-17
---

# Cloud Platform Resource Hierarchy (Google Cloud, doc officielle)

Contenu collé par l'utilisateur depuis la page Google Cloud (accès réseau direct bloqué par la politique de la session).

The Google Cloud resource hierarchy provides a structured way to organize your
cloud resources. All resources except for the highest resource in a hierarchy
have exactly one parent. The hierarchy consists of the *organization* (root) at
the top, followed by *folders* (optional) for grouping, and then *projects*, which
contain the actual service resources like Compute Engine virtual machines and
storage buckets.

Using a structured hierarchy offers the following advantages:

- **Ownership**: It binds the lifecycle of a resource to its immediate parent. Projects belong to the organization, not the individual employee who created them. If an employee leaves, the project remains active and secure.
- **Inheritance**: It provides attachment points for access control and organization policies, which flow down the hierarchy. You can grant roles at a high level (like the organization or a folder). These roles are inherited by all child resources, reducing the need to manually configure permissions for every individual project.

## The organization resource

The organization resource represents an entity (such as a company) and serves as the root node of the
Google Cloud resource hierarchy. It provides the following key functions:

- The organization acts as the parent to all folder and project resources.
- Access control policies (such as IAM roles) and organization policies applied at this level are inherited by every resource in the organization.
- While not strictly required for all Google Cloud users, an organization resource is necessary to use specific Resource Manager features.

### Association with Google Workspace or Cloud Identity accounts

A Google Workspace or Cloud Identity account is a prerequisite to have access to the organization resource.

- A Google Workspace or Cloud Identity account can be associated with exactly one organization resource.
- When a user with a Google Workspace or Cloud Identity account creates a Google Cloud project resource, an organization resource is automatically provisioned for them.

The Google Workspace super admin is the individual responsible for domain
ownership verification and the contact in cases of recovery. For this reason,
the Google Workspace super admin is granted the ability to assign
IAM roles by default. The Google Workspace super admin's
main duty with respect to Google Cloud is to assign the Organization
Administrator IAM role to appropriate users in their domain. This
creates the separation between Google Workspace and Google Cloud
administration responsibilities that users typically seek.

**Project creation rules for managed users**

- Managed users (members of the account domain) must create projects within an organization. It's not possible for accounts associated with an organization resource to create project resources that aren't associated with an organization resource.
- By default, new projects belong to the organization associated with the user.
- If a user has the appropriate permissions, they can specify a different organization resource during project creation; otherwise, it defaults to their home organization.

### Benefits of the organization resource

With an organization resource, project resources belong to the organization, not to the employee who created them — the organization retains project resources when an employee leaves. Organization administrators control all resources centrally, preventing shadow projects or rogue administrators. Roles granted at organization level are inherited by all project and folder resources under it (ex : rôle Network Admin donné une fois au niveau org plutôt que projet par projet).

Attributs d'un organization resource : ID unique, display name (dérivé du domaine primaire Workspace/Cloud Identity), creation time, last modified time, owner (Google Workspace customer ID via Directory API, non modifiable après création).

Exemple JSON :
```json
{
  "creationTime": "2020-01-07T21:59:43.314Z",
  "displayName": "my-organization",
  "lifecycleState": "ACTIVE",
  "name": "organizations/34739118321",
  "owner": {
    "directoryCustomerId": "C012ba234"
  }
}
```

La allow policy initiale d'un nouvel organization resource accorde les rôles Project Creator et Billing Account Creator à tout le domaine Workspace. Allow, deny et organization policies sont hérités à travers la hiérarchie ; la politique effective de chaque ressource résulte des politiques appliquées directement + héritées des ancêtres.

## The folder resource

Les folders sont un mécanisme de regroupement optionnel entre l'organization et les projects (nécessite une organization). Ils fournissent des frontières d'isolation entre projets, fonctionnent comme des sous-organisations (départements, équipes, applications), peuvent contenir d'autres folders (hiérarchie imbriquée), et servent de point d'héritage des policies (IAM roles accordés sur un folder héritent à tous les projects/folders enfants).

Exemple JSON :
```json
{
  "createTime": "2030-01-07T21:59:43.314Z",
  "displayName": "Engineering",
  "lifecycleState": "ACTIVE",
  "name": "folders/634792535758",
  "parent": "organizations/34739118321"
}
```

## The project resource

Le project est l'entité organisationnelle fondamentale, nécessaire pour tout usage de Google Cloud (activer des APIs, billing, ajouter/retirer des collaborateurs, permissions).

Attributs : Project ID (unique, choisi à la création) + Project Number (assigné automatiquement, read-only), display name mutable, lifecycle state (ex. ACTIVE, DELETE_REQUESTED), labels (filtrage), creation time.

Exemple JSON :
```json
{
  "createTime": "2020-01-07T21:59:43.314Z",
  "lifecycleState": "ACTIVE",
  "name": "my-project",
  "parent": {
    "id": "634792535758",
    "type": "folder"
  },
  "projectId": "my-project",
  "labels": {
     "my-label": "prod"
  },
  "projectNumber": "464036093014"
}
```

La policy IAM initiale d'un nouveau project accorde le rôle Owner à son créateur.

Tous les utilisateurs (free trial, free tier, Workspace/Cloud Identity) peuvent créer des projects. Les utilisateurs Free Program ne peuvent créer que des projects (et resources de service dedans) — un project peut être au sommet de sa propre hiérarchie s'il est créé par un utilisateur free trial/tier, sans organization au-dessus. Les comptes Workspace/Cloud Identity ont accès aux fonctionnalités supplémentaires (organization, folders). Un project sans organization peut être migré dans une organization ultérieurement.
</content>
