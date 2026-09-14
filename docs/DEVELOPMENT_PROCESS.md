# Development Process — Traily

## Méthodologie

Traily suit un **Scrum adapté** : les principes agiles sont appliqués avec pragmatisme, sans cérémonies superflues. L'équipe privilégie l'autonomie, l'automatisation et des cycles longs compatibles avec un projet académique.

---

## 1. Cycle de développement

Un **sprint = 2 mois**. Chaque sprint correspond à un ensemble cohérent de fonctionnalités livrables.

```
Sprint (2 mois)
│
├── Semaine 1 — Sprint Planning
│   └── Découpage du backlog, attribution des tâches
│
├── Semaines 2 à 7 — Développement
│   └── Travail autonome par domaine (frontend / backend / IA / devops)
│
└── Semaine 8 — Sprint Review + Rétrospective
    ├── Démonstration des fonctionnalités livrées
    └── Identification des points d'amélioration
```

---

## 2. Cérémonies

| Cérémonie | Fréquence | Objectif |
|---|---|---|
| **Sprint Planning** | Début de chaque sprint | Prioriser et découper le backlog, distribuer les tâches |
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

| Préfixe | Usage |
|---|---|
| `feat/` | Nouvelle fonctionnalité |
| `fix/` | Correction de bug non critique |
| `hotfix/` | Correction urgente en production (voir section 5) |
| `chore/` | Maintenance, dépendances, config |
| `docs/` | Documentation uniquement |
| `test/` | Ajout ou correction de tests |

Le nom de branche suit le ticket Linear : `feat/TRAIL-42-add-waypoint-endpoint`.

### Cycle d'une tâche

```
Ticket Linear
      │
      ▼
Branche feat/fix/chore depuis main
      │
      ▼
Développement local + tests
      │
      ▼
Pull Request → CI automatique (voir section 6)
      │
      ▼
Revue par au moins 1 autre membre
      │
      ▼
Merge sur main → déploiement automatique
```

### Règles de merge

- **Aucun commit direct sur `main`** — toujours passer par une PR.
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
Branche hotfix/<description> depuis main
      │
      ▼
Correction + test ciblé
      │
      ▼
PR → CI + revue accélérée (1 approbation)
      │
      ▼
Merge sur main → déploiement automatique immédiat
```

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

- Code développé et conforme aux Quality Plans ([Backend](./QUALITY_PLAN_BACKEND.md), [Frontend](./QUALITY_PLAN_FRONTEND.md), [IA](./QUALITY_PLAN_AI_SERVICES.md))
- Tests ajoutés si nécessaire, tous au vert
- Pipeline CI au vert
- PR revue et approuvée par au moins un autre membre
- Documentation mise à jour si l'interface ou le comportement change

La couverture de tests est **un warning en dessous de 60%**, pas un bloquant — mais elle est visible dans chaque PR.
