# LINEAR_GUIDE

> Référence et guide d'utilisation Linear pour l'équipe Traily

---

## Structure

```sh
Workspace : traily
└── Team : TRA
    ├── Cycles (sprints de 4 semaines, lundi → dimanche)
    ├── Labels (Type · Domaine · Module)
    ├── Projet : Traily
    │   ├── Milestones (follow-ups FU1 → FU3, défense)
    │   └── Stories (issues parent) → tâches (sub-issues)
    └── Projet : Fonctionnalités bonus (hors MVP)
```

---

## Labels

Chaque issue doit avoir **3 labels** : un type + un domaine + un module.

Un seul label par groupe. Une story qui touche plusieurs domaines est découpée en sub-issues, et chaque sub-issue porte son propre domaine.

| Groupe      | Labels                                                                                  |
| ----------- | --------------------------------------------------------------------------------------- |
| **Type**    | `Feature` `Bug` `Chore` `Docs` `Test`                                                   |
| **Domaine** | `Frontend` `Backend` `IA` `Infra` `DB`                                                  |
| **Module**  | `1 · Socle randonnée` … `10 · Gestion de projet & qualité` `11 · Bonus` (= Epics du [BACKLOG](./BACKLOG.md)) |

Exemple : une nouvelle API de traces → `Feature` + `Backend` + `1 · Socle randonnée`

---

## Création d'une Issue/Ticket

```sh
Titre    → verbe + sujet  (ex: "Implémenter POST /auth/login")
Assignee → obligatoire
Priorité  → reprend le MoSCoW de la story : Urgent = Must · High = Should · Medium = Could · Low = Bonus
Labels    → Type + Domaine + Module
Parent    → la story concernée (X.Y)
Milestone → le FU où la tâche doit être finie
Cycle     → le cycle en cours si on l'attaque maintenant, sinon vide (Backlog)
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

| Cycle    | Dates               | Objectif                        |
| -------- | ------------------- | ------------------------------- |
| Cycle 6  | 28 sept. → 25 oct.  | Migration Linear, découpage FU2 |
| Cycle 7  | 26 oct. → 22 nov.   | Contient le FU2 (13/11)         |
| Cycle 8  | 23 nov. → 20 déc.   | Contient le FU3 (18/12)         |
| Cycle 9  | 21 déc. → 17 janv.  | Vacances de Noël                |
| Cycle 10 | 18 janv. → 14 févr. | Contient la défense (05/02)     |

L'équipe a un vendredi d'école par semaine :

- **Chaque vendredi** : point de 10 min sur la vue « Sprint » et le % du FU en cours.
- **Dernier vendredi du cycle** : bilan + rétro + planning du cycle suivant.

---

## Milestones

Les milestones = deadlines fixes imposées par l'école (follow-ups et défense).

| Milestone | Date             |
| --------- | ---------------- |
| FU1       | 9 octobre 2026   |
| FU2       | 13 novembre 2026 |
| FU3       | 18 décembre 2026 |
| Défense   | 5 février 2027   |

Milestone = ce qui doit être prêt à une date (porté par les stories et leurs sub-issues). Cycle = ce qu'on fait maintenant (porté par les sub-issues).

---

## Vues

Les vues sont créées dans le projet Traily (onglet Issues → filtre → Save view). L'avancement par milestone se lit sur la page Overview du projet.

| Vue             | Quand                        | Réglages                                                                                   |
| --------------- | ---------------------------- | ------------------------------------------------------------------------------------------ |
| **Sprint**      | Point du vendredi            | Cycle = en cours · Board · colonnes Status · lignes Assignee · sub-issues visibles           |
| **Prochain FU** | Préparer le FU               | Milestone = FU en cours · Board · colonnes Status · lignes Parent issue (une par story)      |
| **Stories**     | Vue d'ensemble, arbitrages   | List · groupée par Milestone · sub-issues masquées · tri Priority                          |
| **Charge**      | Planning du dernier vendredi | Cycle = suivant · statut non terminé · List · groupée par Assignee · groupes vides visibles |
