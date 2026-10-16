# Development Process — Traily

## Méthodologie

Traily suit un **Scrum adapté** : les principes agiles sont appliqués avec pragmatisme, sans cérémonies superflues. L'équipe privilégie l'autonomie, l'automatisation et des cycles longs compatibles avec un projet académique.

---

## 1. Cycle de développement

Un **sprint = 4 semaines** (un cycle Linear, du lundi au dimanche). L'équipe a un vendredi d'école par semaine : le travail se fait ce jour-là et sur le temps libre. Les follow-ups (FU) de l'école servent de démonstration et sont suivis par les milestones Linear (voir [LINEAR_GUIDE.md](./LINEAR_GUIDE.md)).

```
Sprint (4 semaines)
│
├── Vendredis 1 à 3 — Développement
│   ├── Travail autonome par domaine (frontend / backend / IA / devops)
│   └── Point Linear de 10 min (board du cycle + % du FU en cours)
│
└── Vendredi 4 — Sprint Review + Rétrospective + Sprint Planning
    ├── Démonstration des fonctionnalités livrées
    ├── Identification des points d'amélioration
    └── Découpage et attribution des tâches du sprint suivant
```

---

## 2. Cérémonies

| Cérémonie | Fréquence | Objectif |
|---|---|---|
| **Sprint Planning** | Dernier vendredi du sprint précédent | Prioriser et découper le backlog, distribuer les tâches |
| **Point Linear** | Chaque vendredi | 10 min sur le board du cycle et le % du FU en cours |
| **Sprint Review** | Fin de chaque sprint | Valider ce qui a été livré, ajuster les priorités du prochain sprint |
| **Rétrospective** | Fin de chaque sprint | Identifier ce qui a bien fonctionné et ce qui doit changer dans le process |
| **Syncs ad hoc** | À la demande | Déblocage ponctuel, décision d'architecture impactant plusieurs membres |

Pas de daily standup — la communication passe par les PRs, les commentaires de code et les syncs à la demande.

---

## 3. Gestion du backlog

- Le backlog est géré dans **Linear** (référence dans la Definition of Done).
- Pas d'estimation en story points — les tâches sont découpées suffisamment petites pour être réalisables en quelques jours au maximum.
- La priorisation est assurée par le **coordinateur** en amont du Sprint Planning, avec revue collective lors de la cérémonie.
- Une tâche = un ticket Linear = une branche = une PR.

---

## 4. Workflow de développement

### Branches

Deux branches permanentes (backend et Client) :

| Branche | Rôle |
|---|---|
| `develop` | Intégration : toutes les PR de travail y arrivent |
| `main` | Production : ne reçoit que des PR depuis `develop` (bloqué par la CI sinon) |

Branches de travail, créées depuis `develop` :

| Préfixe | Usage |
|---|---|
| `feat/` | Nouvelle fonctionnalité |
| `fix/` | Correction de bug non critique |
| `hotfix/` | Correction urgente en production (voir section 5) |
| `chore/` | Maintenance, dépendances, config |
| `docs/` | Documentation uniquement |
| `test/` | Ajout ou correction de tests |

Le nom de branche suit le ticket Linear : `feat/TRA-42-add-waypoint-endpoint`.

### Cycle d'une tâche

```
Ticket Linear
      │
      ▼
Branche feat/fix/chore depuis develop
      │
      ▼
Développement local + tests
      │
      ▼
Pull Request vers develop → CI automatique (voir section 6)
      │
      ▼
Revue par au moins 1 autre membre
      │
      ▼
Merge sur develop
      │
      ▼
PR develop → main (release) → merge sur main
```

### Règles de merge

- **Aucun commit direct sur `main` ni sur `develop`** — toujours passer par une PR.
- `main` n'accepte que des PR dont la source est `develop` (vérifié par la CI backend et Client).
- Toute PR doit passer les checks CI **et** être approuvée par au moins un autre développeur.
- Une PR = une fonctionnalité ou un fix — pas de PRs fourre-tout.

### Commits

Convention [Conventional Commits](https://www.conventionalcommits.org/) obligatoire :

```
feat: add waypoint creation endpoint
fix: correct GPS coordinate parsing for negative longitudes
chore: update golangci-lint to v1.57
docs: add hardware requirements
test: add regression tests for trail difficulty scoring
```

---

## 5. Gestion des hotfixes

Un hotfix traite une anomalie critique en production (service down, faille de sécurité, corruption de données).

```
Anomalie critique détectée en production
      │
      ▼
Branche hotfix/TRA-XX-<description> depuis develop
      │
      ▼
Correction + test ciblé
      │
      ▼
PR vers develop → CI + revue accélérée (1 approbation)
      │
      ▼
PR develop → main immédiate
```

La CI refuse toute PR vers `main` qui ne vient pas de `develop` : un hotfix passe donc lui aussi par `develop`.

Les hotfixes contournent la priorisation du backlog — ils sont traités en priorité absolue par le membre le plus disponible, validés par le coordinateur.

---

## 6. CI/CD — Déploiement automatisé

### Principe

Tout merge sur `main` déclenche automatiquement le déploiement. Pas d'action manuelle.

### Checks CI par composant (bloquants)

**Backend Go & Micro-services Go :**

| Check | Outil |
|---|---|
| Formatage | `gofmt` / `goimports` |
| Lint | `golangci-lint` |
| Tests | `go test ./...` |
| Build | `go build ./...` |
| Vulnérabilités | `govulncheck` |

**Frontend Expo :**

| Check | Outil |
|---|---|
| Typage | `tsc --noEmit` |
| Lint | ESLint (config Expo) |
| Formatage | Prettier |
| Tests | `jest --ci` |
| Build preview | `eas build --profile preview` (sur `main` uniquement) |

**Micro-service Python :**

| Check | Outil |
|---|---|
| Formatage | `black` + `isort` |
| Lint | `ruff` |
| Typage | `mypy --strict` |
| Tests | `pytest --ci` |

### Environnements

| Environnement | Déclencheur | Usage |
|---|---|---|
| `development` | Local | Dev quotidien, `.env` local |
| `staging` | Merge sur `main` | Tests d'intégration, validation avant prod |
| `production` | Manuel depuis `staging` validé | Utilisateurs finaux |

Le passage `staging → production` est le seul déploiement qui reste manuel — pour éviter une mise en production accidentelle d'un état non validé.

---

## 7. Qualité et Definition of Done

Une tâche est **DONE** uniquement si les critères de la [Definition of Done](./DEFINITION_OF_DONE.md) sont tous remplis :

- Code développé et conforme aux Quality Plans ([Backend](./QUALITY_PLAN_BACKEND.md), [Frontend](./QUALITY_PLAN_FRONTEND.md)) ; services IA : checks CI de la section 6
- Tests ajoutés si nécessaire, tous au vert
- Pipeline CI au vert
- PR revue et approuvée par au moins un autre membre
- Documentation mise à jour si l'interface ou le comportement change

La couverture de tests est **un warning en dessous de 60%**, pas un bloquant — mais elle est visible dans chaque PR.
