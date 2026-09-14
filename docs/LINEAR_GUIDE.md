# LINEAR_GUIDE

> Référence et guide d'utilisation Linear pour l'équipe Traily

---

## Structure

```sh
Workspace : traily
└── Team : TRA
    ├── Cycles (sprints de 4 semaines)
    ├── Labels
    └── Projet : Traily
        └── Milestones (défenses / follow-ups)
```

---

## Labels

Chaque issue doit avoir **2 labels** : un type + un domaine.

| Groupe      | Labels                                 |
| ----------- | -------------------------------------- |
| **Type**    | `Feature` `Bug` `Chore` `Docs` `Test`  |
| **Domaine** | `Frontend` `Backend` `IA` `Infra` `DB` |

Exemple : une nouvelle API → `Feature` + `Backend`

---

## Création d'une Issue/Ticket

```sh
Titre    → verbe + sujet  (ex: "Implémenter POST /auth/login")
Assignee → obligatoire
Priorité → jamais "No priority" (minimum Medium)
Labels   → Type + Domaine
Cycle    → le cycle en cours
```

**Template à utiliser :**

```sh
## Contexte
Pourquoi cette tâche existe ?

## Description
Ce qu'il faut faire concrètement.

## Critères d'acceptance
- [ ] ...
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre
```

---

## Workflow

```sh
Backlog → Todo → In Progress → QA → Done
```

- **QA** = PR ouverte, en attente de review
- **Done** = PR mergée + tous les critères d'acceptance cochés

---

## Cycles

| Cycle   | Dates              | Objectif                              |
| ------- | ------------------ | ------------------------------------- |
| Cycle 1 | 11 mai → 7 juin    | Finalisations et validations des docs |
| Cycle 2 | 8 juin → 5 juillet | Init Code & Présentation du projet    |

Un point d'équipe à la fin de chaque cycle.

---

## Milestones

Les milestones = deadlines fixes imposées par l'école.

| Milestone               | Date         |
| ----------------------- | ------------ |
| Competencies evaluation | 29 juin 2026 |
| Sprint review           | 22 juin 2026 |
