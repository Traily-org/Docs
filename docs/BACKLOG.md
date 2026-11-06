# Backlog — Traily

> **Statut : cadrage initial, figé.** Le suivi vivant (périmètre par FU, avancement, découpage en tâches) est dans Linear, projet Traily. Ce document n'est plus mis à jour.

> Backlog produit construit par croisement des trois référentiels de cadrage : **WBS** ([WBS.md](./WBS.md), le _quoi_ — livrables et tâches), **PBS** ([PBS.md](./PBS.md), le _où_ — décomposition produit), **OBS** ([OBS.md](./OBS.md), le _qui_ — équipe et ownership). Suit les conventions [LINEAR_GUIDE.md](./LINEAR_GUIDE.md) pour l'import direct dans Linear.

---

## 1. Méthode de construction

Chaque ligne du backlog est une **Story** (= future Epic Linear regroupant un ou plusieurs tickets), obtenue en croisant :

1. **WBS** → le découpage Livrable/Tâches (Partie 3 de [WBS.md](./WBS.md)) donne le contenu de la Story et sa liste de tâches à transformer en tickets Linear.
2. **PBS** → la branche produit ([PBS.md](./PBS.md)) donne le **Domaine** (Frontend mobile / Backend & IA / Infrastructure / Communauté & Modération / Cadre éthique) et vérifie qu'aucun nœud du produit n'est orphelin de backlog.
3. **OBS** → la table _Ownership des modules produit_ et les fiches de rôle ([OBS.md](./OBS.md)) donnent l'**Assignee** par défaut (UI / Backend / IA) et signalent les Stories dépendantes d'un poste encore ouvert.

```
WBS (livrable + tâches)  ──┐
PBS (domaine produit)      ├──►  Story de backlog  ──►  Tickets Linear (1 tâche = 1 ticket)
OBS (owner / rôle)        ──┘
```

Chaque Story ci-dessous est formulée en **user story** (« En tant que ..., je peux ..., afin de ... ») et embarque directement ses **critères d'acceptation**, au format checklist [LINEAR_GUIDE.md](./LINEAR_GUIDE.md) — copiables tels quels dans un ticket Linear. Ces critères sont repris de la Partie 4 de [WBS.md](./WBS.md), y compris pour l'Epic 11 (bonus). Le détail tâche par tâche (Partie 3 de [WBS.md](./WBS.md)) reste la référence pour le découpage en tickets unitaires et n'est pas dupliqué ici.

---

## 2. Légende

| Champ        | Valeurs                                                                                                                                |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Type**     | `Feature` · `Chore` · `Test` · `Docs` (voir [LINEAR_GUIDE.md](./LINEAR_GUIDE.md))                                                      |
| **Domaine**  | `Frontend` · `Backend` · `IA` · `Infra` · `DB`                                                                                         |
| **Priorité** | `Must` (bloquant MVP) · `Should` (attendu MVP) · `Could` (si le temps le permet) · `Bonus` (hors scope MVP, à arbitrer post-lancement) |
| **Owner**    | Défaut issu de la table _Ownership des modules produit_ d'[OBS.md](./OBS.md) — réassignable en Sprint Planning                         |

> Les seuils de priorité sont indicatifs (cohérents avec la note de fin de [WBS.md](./WBS.md)) : à valider collectivement en Sprint Planning, le coordinateur (Florian) arbitrant en dernier ressort ([DEVELOPMENT_PROCESS.md](./DEVELOPMENT_PROCESS.md)).

---

## 3. Vue d'ensemble des Epics

| # WBS | Epic                             | Branche PBS                                 | Owner principal (OBS)                                   | Priorité dominante |
| ----- | -------------------------------- | ------------------------------------------- | ------------------------------------------------------- | ------------------ |
| 1     | Socle randonnée                  | 📱 Application Mobile (GEO, CAM)            | Maxim (backend/natif) · Lucas (UI)                      | Must               |
| 2     | Système d'espèces                | ⚙️ Backend & IA (IAS) + 📱 UI4              | Maxim/Yohan · Arthur J. (IA)                            | Must → Should      |
| 3     | Collections personnelles         | 📱 Application Mobile (COL)                 | Lucas (UI) · Maxim/Yohan (BE)                           | Must               |
| 4     | Gamification                     | 📱 Application Mobile (COL4, COL5)          | Lucas (UI) · Maxim/Yohan (BE)                           | Should             |
| 5     | Communauté & Équipes             | 👥 Communauté & Modération (TEAM, FEED)     | 🔎 Recrue 1 _(poste ouvert)_                            | Should             |
| 6     | Modération                       | 👥 Communauté & Modération (MOD)            | 🔎 Recrue 2 _(poste ouvert)_ · Arthur J. (IA)           | Should             |
| 7     | Cadre éthique & sécurité         | 🛡️ Cadre éthique & nature (ZON, ALERT, ADV) | Lucas (UI) · Arthur B. (BE)                             | Could              |
| 8     | Backend & IA                     | ⚙️ Backend & IA (API, IAS)                  | Maxim/Yohan (API) · Arthur J. (IA)                      | Must → Should      |
| 9     | Infrastructure                   | 🏗️ Infrastructure (STO, CI, MON, SEC)       | Arthur B. · Florian                                     | Must               |
| 10    | Gestion de projet & qualité      | — (transversal)                             | Florian · Yohan (E2E) · toute l'équipe                  | Must               |
| 11    | Fonctionnalités bonus (hors MVP) | _hors PBS actuel — à intégrer si retenues_  | Lucas · Maxim/Yohan · Recrue 1 · à définir (SmartWatch) | Bonus              |

---

## 4. Détail du backlog par Epic

> Chaque Story suit le gabarit : métadonnées (`Type` · `Domaine` · `Priorité` · `Owner`), user story, puis critères d'acceptation au format checklist Linear.

### Epic 1 — Socle randonnée

_(PBS : Application Mobile → GEO, CAM)_

#### 1.1 · Navigation GPS temps réel (foreground/background, autonomie)

`Feature` · `Frontend` · **Must** · Owner : Maxim (natif) · Lucas (UI carte)

> **En tant que** randonneur, **je peux** suivre ma position GPS en temps réel pendant ma randonnée — en foreground comme en arrière-plan — avec une autonomie batterie optimisée, **afin de** ne pas perdre ma trace ni mon téléphone en cours de route.

**Critères d'acceptation**

- [ ] Position affichée avec un délai < 3s ; distance calculée à ±5% d'une mesure de référence
- [ ] Perte de points GPS en arrière-plan < 5% sur une session de 3h, testée sur au moins 3 constructeurs Android
- [ ] Consommation batterie sous le seuil cible défini avec l'équipe, sur une randonnée de test
- [ ] Aucun crash constaté sur une session de 2h+, testé sur iOS et Android
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 1.2 · Enregistrement, visualisation et recherche de traces

`Feature` · `Frontend / Backend` · **Must** · Owner : Lucas (UI) · Maxim (BE)

> **En tant que** randonneur, **je peux** enregistrer ma randonnée, la consulter ensuite (carte, profil altimétrique, statistiques) et explorer les traces existantes autour de moi, **afin de** garder un historique de mes sorties et découvrir de nouveaux parcours.

**Critères d'acceptation**

- [ ] Trace visible dans l'historique < 5s après la fin de session ; aucune perte en cas de coupure réseau
- [ ] Écran détail chargé en < 2s ; profil altimétrique cohérent avec les données enregistrées
- [ ] Recherche géospatiale de traces en < 2s ; application des filtres en < 1s
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 1.3 · Estimation de difficulté (dénivelé, durée, classification)

`Feature` · `Backend` · **Must** · Owner : Maxim (calcul) · Lucas (affichage)

> **En tant que** randonneur, **je peux** voir le dénivelé, la durée estimée et le niveau de difficulté d'une trace avant de la démarrer, **afin de** choisir une randonnée adaptée à mon niveau.

**Critères d'acceptation**

- [ ] Dénivelé calculé à ±10% d'une source de référence
- [ ] Durée estimée cohérente à ±20% par rapport à des temps réels observés
- [ ] Classification de difficulté cohérente avec les règles définies, vérifiée sur un jeu de traces classées manuellement
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 1.4 · Photo géolocalisée (capture, épinglage, upload)

`Feature` · `Frontend / Infra` · **Must** · Owner : Maxim (capture) · Lucas (UI galerie) · Arthur B. (stockage)

> **En tant que** randonneur, **je peux** prendre une photo d'une espèce pendant ma randonnée — même hors-ligne — et la voir épinglée automatiquement sur ma trace, **afin de** garder une preuve géolocalisée de mes observations.

**Critères d'acceptation**

- [ ] 100% des photos prises in-app contiennent des métadonnées GPS valides (hors absence totale de signal)
- [ ] Photos prises hors-ligne envoyées automatiquement au retour du réseau, sans perte sur un test de 50 prises
- [ ] Position du pin photo sur la carte à ±20m de la prise réelle
- [ ] Taux de succès d'upload > 98% en conditions de réseau instable simulé
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 2 — Système d'espèces

_(PBS : Backend & IA → IAS ; Application Mobile → UI4)_

#### 2.1 · Base de données d'espèces (référentiel + admin)

`Feature` · `DB` · **Must** · Owner : Maxim (import) · Arthur B. (PostGIS)

> **En tant qu'** administrateur du référentiel, **je peux** gérer et corriger les fiches espèces (flore, faune, insectes) depuis une interface d'administration, **afin que** l'app dispose de données naturalistes fiables et à jour.

**Critères d'acceptation**

- [ ] Le référentiel contient un nombre minimum d'espèces couvrant la zone de lancement (seuil à définir avec l'équipe produit)
- [ ] 100% des espèces ont au moins : nom, catégorie, zone de répartition, période d'observation
- [ ] Une modification via l'admin est répercutée côté app en < 5 minutes
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 2.2 · Génération de listes d'espèces observables

`Feature` · `Backend / IA` · **Must** · Owner : Maxim (BE) · Arthur J. (scoring) · Lucas (UI)

> **En tant que** randonneur, **je peux** consulter la liste des espèces observables sur une trace donnée, calculée selon la localisation, la saison et les données communautaires, **afin de** savoir quoi chercher pendant ma randonnée.

**Critères d'acceptation**

- [ ] Génération d'une liste en < 2s avec cache, < 5s sans cache
- [ ] Liste jugée pertinente par un expert naturaliste sur un échantillon de traces test
- [ ] Régénération correcte de la liste au changement de saison
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 2.3 · Identification d'espèce par photo (IA)

`Feature` · `IA` · **Should** · Owner : Arthur J. (modèle + API FastAPI) · Maxim/Yohan (intégration) · Lucas (UI)

> **En tant que** randonneur, **je peux** envoyer une photo d'une espèce et recevoir une proposition d'identification avec un score de confiance, **afin de** savoir ce que j'ai observé sans devoir le rechercher moi-même.

**Critères d'acceptation**

- [ ] Taux de bonne identification (top-1) mesuré et documenté sur un jeu de test (seuil cible à définir)
- [ ] Temps de réponse < 5s pour 95% des requêtes
- [ ] Fallback manuel systématiquement proposé sous le seuil de confiance défini
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 2.4 · Pipeline de validation des contributions

`Feature` · `Backend / IA` · **Should** · Owner : 🔎 Recrue 2 (workflow) · Arthur J. (pré-validation IA)

> **En tant que** randonneur contributeur, **je peux** soumettre une observation et être notifié de son statut de validation (IA puis, si besoin, modération humaine), **afin de** savoir si ma contribution est acceptée et me rapporte des points.

**Critères d'acceptation**

- [ ] 100% des contributions reçoivent un statut final sous un délai maximum défini (ex : 48h)
- [ ] Taux de contributions nécessitant une modération humaine sous un seuil cible une fois l'IA calibrée
- [ ] Notification reçue dans les 5 minutes suivant la décision
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 3 — Collections personnelles

_(PBS : Application Mobile → COL1-COL3)_

#### 3.1 · Herbier (collection flore)

`Feature` · `Frontend` · **Must** · Owner : Lucas (UI) · Maxim/Yohan (BE) · Arthur J. (data)

> **En tant que** randonneur, **je peux** consulter ma collection d'espèces de flore découvertes façon "Pokédex", **afin de** suivre ma progression et retrouver mes observations passées.

**Critères d'acceptation**

- [ ] Contribution flore validée visible dans l'herbier < 1 minute
- [ ] Pourcentage de complétion exact par rapport au référentiel local
- [ ] Filtres/tri fonctionnels sur une collection de test de 100+ espèces
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 3.2 · Bestiaire (collection faune/insectes)

`Feature` · `Frontend` · **Must** · Owner : Lucas (UI) · Maxim/Yohan (BE)

> **En tant que** randonneur, **je peux** consulter ma collection d'espèces de faune/insectes découvertes, distincte visuellement de l'herbier, **afin de** suivre ma progression sur ce second type de collection.

**Critères d'acceptation**

- [ ] Mêmes critères que 3.1, appliqués à la faune/insectes
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 3.3 · Fiche espèce détaillée (commune herbier/bestiaire)

`Feature` · `Frontend` · **Should** · Owner : Lucas (UI) · Arthur J. (data naturaliste)

> **En tant que** randonneur, **je peux** consulter la fiche détaillée d'une espèce (infos naturalistes, statut de protection, conseils éthiques, mes photos personnelles, rareté communautaire) depuis l'herbier, le bestiaire ou une trace, **afin de** en apprendre davantage sur ce que j'ai observé.

**Critères d'acceptation**

- [ ] Fiche chargée en < 2s
- [ ] 100% des fiches affichent au minimum nom, description, statut de protection
- [ ] Aucune fuite de données entre utilisateurs sur les photos personnelles affichées
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 3.4 · Profil utilisateur (stats, historique)

`Feature` · `Frontend / Backend` · **Must** · Owner : Lucas (UI) · Yohan (BE)

> **En tant que** randonneur, **je peux** consulter mon profil avec mes statistiques de collection et l'historique de mes randonnées, et modifier mes informations, **afin de** avoir une vue d'ensemble de ma progression.

**Critères d'acceptation**

- [ ] Statistiques exactement conformes aux données réelles de la base
- [ ] Modification du profil persistée et reflétée immédiatement
- [ ] Historique trié par date décroissante par défaut
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 4 — Gamification

_(PBS : Application Mobile → COL4, COL5)_

#### 4.1 · Système de points

`Feature` · `Backend` · **Should** · Owner : Maxim/Yohan (BE partagé) · Lucas (UI)

> **En tant que** randonneur, **je peux** gagner des points automatiquement pour chaque contribution validée et consulter mon historique de gains, **afin de** suivre ma progression et me comparer aux autres.

**Critères d'acceptation**

- [ ] Points crédités en < 1 minute après validation d'une contribution
- [ ] Barème appliqué de façon cohérente sur 100% des cas testés
- [ ] Historique fidèle à chaque gain de points avec sa raison
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 4.2 · Badges et progression

`Feature` · `Frontend / Backend` · **Should** · Owner : Lucas (UI) · Maxim/Yohan (BE)

> **En tant que** randonneur, **je peux** débloquer des badges en atteignant certains jalons et être notifié immédiatement, **afin de** avoir des objectifs de progression supplémentaires.

**Critères d'acceptation**

- [ ] Badge débloqué au plus tard 1 minute après que la condition est remplie
- [ ] 100% des conditions de déblocage testées et validées avant mise en production
- [ ] Notification de déblocage fiable sur iOS et Android
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 4.3 · Leaderboards (trace/région/équipe)

`Feature` · `Frontend / Backend` · **Should** · Owner : Lucas (UI) · Maxim/Yohan (BE)

> **En tant que** randonneur, **je peux** consulter les classements par trace, région et équipe et voir ma position, **afin de** me situer face aux autres joueurs.

**Critères d'acceptation**

- [ ] Classement cohérent avec les scores réels en base
- [ ] Temps de chargement d'un classement < 2s
- [ ] Rafraîchissement conforme à la fréquence cible définie
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 5 — Communauté & Équipes

_(PBS : Communauté & Modération → TEAM, FEED)_

> ⚠️ Epic bloqué tant que le poste **Recrue 1 — Fullstack Communauté** n'est pas pourvu (voir [OBS.md](./OBS.md)). À défaut de recrutement, arbitrage du coordinateur pour réaffecter tout ou partie à Maxim/Yohan en réduisant le périmètre.

#### 5.1 · Gestion des équipes / clans

`Feature` · `Frontend / Backend` · **Should** · Owner : 🔎 Recrue 1 _(poste ouvert)_

> **En tant que** randonneur, **je peux** créer une équipe, en devenir admin, inviter des membres et gérer leurs rôles, **afin de** jouer collectivement avec mes amis.

**Critères d'acceptation**

- [ ] Création d'équipe et ajout de membre effectifs immédiatement après action
- [ ] Membre non-admin ne pouvant pas accéder aux fonctions de gestion réservées à l'admin
- [ ] Mécanisme d'invitation fiable sur un échantillon testé
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 5.2 · Classement d'équipe

`Feature` · `Backend` · **Should** · Owner : 🔎 Recrue 1 _(poste ouvert)_

> **En tant que** randonneur, **je peux** consulter le classement de mon équipe agrégeant les scores de ses membres, **afin de** suivre la performance collective.

**Critères d'acceptation**

- [ ] Score d'équipe conforme exactement à l'agrégation définie
- [ ] Classement mis à jour après toute contribution d'un membre en < 5 minutes
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 5.3 · Fil communautaire

`Feature` · `Frontend / Backend` · **Could** · Owner : 🔎 Recrue 1 _(poste ouvert)_

> **En tant que** randonneur, **je peux** voir les contributions récentes des autres utilisateurs sur une trace, **afin de** me tenir informé de l'activité communautaire avant/pendant ma sortie.

**Critères d'acceptation**

- [ ] Nouvelle contribution validée visible dans le fil < 1 minute
- [ ] Fil trié par ordre chronologique décroissant sans erreur
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 6 — Modération

_(PBS : Communauté & Modération → MOD)_

> ⚠️ Même dépendance que l'Epic 5 pour le poste **Recrue 2 — Fullstack Modération**.

#### 6.1 · Dashboard de modération

`Feature` · `Frontend / Backend` · **Should** · Owner : 🔎 Recrue 2 _(poste ouvert)_ · Arthur J. (file alimentée par l'IA)

> **En tant que** modérateur, **je peux** consulter la file des contributions soumises et les valider ou rejeter en un clic, **afin de** garantir la qualité du contenu communautaire.

**Critères d'acceptation**

- [ ] Utilisateur sans rôle modérateur ne pouvant pas accéder au dashboard
- [ ] Décision de modération appliquée immédiatement (statut mis à jour < 5s)
- [ ] File de modération correctement triée par priorité définie
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 6.2 · Signalement utilisateur

`Feature` · `Frontend / Backend` · **Should** · Owner : 🔎 Recrue 2 _(poste ouvert)_

> **En tant que** randonneur, **je peux** signaler une photo, une contribution ou un comportement problématique, **afin de** contribuer à un contenu communautaire sain.

**Critères d'acceptation**

- [ ] Signalement visible dans le dashboard < 1 minute
- [ ] 100% des signalements traçables (auteur, cible, motif, date)
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 6.3 · Historique de modération

`Feature` · `Backend` · **Could** · Owner : 🔎 Recrue 2 _(poste ouvert)_

> **En tant que** modérateur, **je peux** consulter et exporter l'historique de toutes les décisions de modération, **afin de** garantir la traçabilité et l'auditabilité des actions.

**Critères d'acceptation**

- [ ] 100% des décisions journalisées sans exception
- [ ] Export disponible dans un format exploitable (CSV/JSON)
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 7 — Cadre éthique & sécurité

_(PBS : Cadre éthique & nature → ZON, ALERT, ADV)_

#### 7.1 · Zones protégées (carte)

`Feature` · `Frontend / DB` · **Could** · Owner : Lucas (UI) · Arthur B. (données géo)

> **En tant que** randonneur, **je peux** voir les zones naturelles sensibles ou protégées affichées sur la carte, **afin de** adapter mon comportement pendant ma randonnée.

**Critères d'acceptation**

- [ ] Zones affichées conformes aux données officielles à la date de mise à jour
- [ ] Couche de zones sans impact perceptible sur la performance de la carte (< 2s)
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 7.2 · Alertes comportementales

`Feature` · `Frontend` · **Could** · Owner : Lucas (UI) · Maxim (détection GPS)

> **En tant que** randonneur, **je peux** recevoir une alerte non intrusive quand j'approche d'une zone sensible ou d'une espèce protégée, **afin de** respecter les distances recommandées.

**Critères d'acceptation**

- [ ] Alerte déclenchée dans un rayon cohérent avec la règle définie
- [ ] Aucune alerte en double/spam pour une même situation
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 7.3 · Contenu pédagogique

`Feature / Docs` · `Frontend` · **Could** · Owner : Lucas (intégration fiches)

> **En tant que** randonneur, **je peux** lire des conseils éthiques sur chaque espèce sensible et découvrir le cadre éthique de l'app dès mon premier lancement, **afin de** adopter un comportement respectueux de la nature.

**Critères d'acceptation**

- [ ] 100% des espèces protégées du référentiel avec un conseil éthique associé
- [ ] Onboarding éthique affiché une fois au premier lancement, accessible depuis les paramètres
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 8 — Backend & IA

_(PBS : Backend & IA → API, IAS)_

#### 8.1 · API cœur (Trails/Users/Sessions/Espèces/Scores)

`Feature` · `Backend` · **Must** · Owner : Maxim · Yohan · supervision Arthur B.

> **En tant que** développeur (mobile ou backend), **je peux** m'appuyer sur des APIs Trails/Users/Sessions/Espèces/Scores documentées et sécurisées, **afin de** construire les fonctionnalités produit sans dupliquer la logique métier côté client.

**Critères d'acceptation**

- [ ] 100% des endpoints prévus documentés (OpenAPI/Swagger) et testés
- [ ] Endpoints protégés rejetant les requêtes non authentifiées
- [ ] Temps de réponse moyen < 300ms en conditions normales de charge
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 8.2 · Micro-service identification d'espèces

`Feature` · `IA` · **Should** · Owner : Arthur J.

> **En tant que** backend applicatif, **je peux** appeler un micro-service d'identification qui retourne une ou plusieurs espèces probables à partir d'une photo, **afin de** alimenter la fonctionnalité d'identification côté mobile (Story 2.3).

**Critères d'acceptation**

- [ ] Réponse < 5s pour 95% des requêtes sous charge nominale
- [ ] Disponibilité > 99% sur la période de test définie
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 8.3 · Micro-service recommandation de traces

`Feature` · `IA` · **Could** · Owner : Arthur J.

> **En tant que** randonneur, **je peux** recevoir des suggestions de traces personnalisées selon mon profil, mon historique et mon score, **afin de** découvrir de nouvelles randonnées pertinentes.

**Critères d'acceptation**

- [ ] Recommandations jugées pertinentes sur un échantillon de test (validation qualitative)
- [ ] Temps de réponse de l'API < 1s
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 8.4 · Agrégation de données communautaires

`Feature` · `IA / Backend` · **Should** · Owner : Arthur J. · Maxim

> **En tant que** backend applicatif, **je peux** enrichir périodiquement les probabilités d'observation par espèce/zone à partir des contributions validées, **afin de** améliorer la pertinence des listes d'espèces observables (Story 2.2).

**Critères d'acceptation**

- [ ] Pipeline exécuté sans erreur sur le planning défini et monitoré
- [ ] Données enrichies visibles dans les listes d'espèces observables sous un délai défini (ex : 24h max)
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 9 — Infrastructure

_(PBS : Infrastructure → STO, CI, MON, SEC)_

#### 9.1 · Stockage (S3/MinIO)

`Chore` · `Infra` · **Must** · Owner : Arthur B.

> **En tant qu'**équipe technique, **je peux** m'appuyer sur un stockage objet (S3/MinIO) avec upload direct via URL présignée et politique de rétention, **afin de** servir les photos géolocalisées de façon fiable et scalable.

**Critères d'acceptation**

- [ ] Upload via URL présignée fonctionnel de bout en bout sans passer par le backend applicatif
- [ ] Accès non autorisés aux buckets refusés
- [ ] Procédure de sauvegarde/restauration testée et documentée
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 9.2 · CI/CD

`Chore` · `Infra` · **Must** · Owner : Florian

> **En tant que** développeur, **je peux** voir chaque push/merge déclencher automatiquement build, tests et déploiement en staging, **afin de** livrer en confiance sans étape manuelle.

**Critères d'acceptation**

- [ ] Déploiement complet (build → tests → staging) sans intervention manuelle
- [ ] Pipeline bloquant le déploiement si les tests ne passent pas
- [ ] Aucun secret visible en clair dans les logs ou le code source
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 9.3 · Monitoring

`Chore` · `Infra` · **Should** · Owner : Arthur B. · Florian

> **En tant qu'**équipe technique, **je peux** consulter un dashboard centralisé de l'état de santé des services et être alerté automatiquement en cas d'anomalie, **afin de** réagir rapidement à un incident.

**Critères d'acceptation**

- [ ] Dashboard centralisé affichant l'état de santé de tous les services en temps réel
- [ ] Alerte test déclenchant une notification à l'équipe technique en < 5 min
- [ ] Métriques conservées sur une durée définie pour analyse historique
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 9.4 · Environnements (dev/staging/prod)

`Chore` · `Infra` · **Must** · Owner : Florian

> **En tant que** développeur, **je peux** travailler dans un environnement dev isolé de staging/production, avec des migrations de base de données versionnées et réversibles, **afin de** ne jamais impacter les utilisateurs finaux par erreur.

**Critères d'acceptation**

- [ ] Changement en dev sans impact sur staging/production
- [ ] Migration applicable et annulable (rollback) sans perte de données sur un environnement de test
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

### Epic 10 — Gestion de projet & qualité

_(transversal, non porté par une branche PBS unique)_

#### 10.1 · Pilotage projet (planning, budget, rituels)

`Chore` · **Must** · Owner : Florian (coordinateur)

> **En tant que** coordinateur, **je peux** disposer d'un planning partagé en sprints/releases avec un suivi budgétaire et des rituels réguliers, **afin de** garder l'équipe alignée sur les priorités et les délais.

**Critères d'acceptation**

- [ ] Planning partagé et accessible à toute l'équipe
- [ ] Écarts entre planning prévisionnel et réel identifiés et documentés à chaque revue
- [ ] PR reviewée par 1 autre membre (si livrable documentaire versionné)

#### 10.2 · Tests (unitaires, intégration, beta)

`Test` · **Must** · Owner : Yohan (E2E) · chaque owner sur son périmètre

> **En tant qu'**équipe technique, **je peux** m'appuyer sur une stratégie de tests (unitaires, intégration, beta fermée) avec une couverture mesurable, **afin de** garantir la qualité et la stabilité de l'app avant chaque déploiement.

**Critères d'acceptation**

- [ ] Couverture de tests unitaires atteignant un seuil cible défini (ex : >70% sur le code critique)
- [ ] Aucune régression critique détectée en beta laissée sans correction avant lancement
- [ ] Tests d'intégration passant systématiquement avant tout déploiement en production
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 10.3 · Déploiement & lancement (stores, doc, com.)

`Chore / Docs` · **Must** · Owner : Florian · toute l'équipe

> **En tant qu'**utilisateur final, **je peux** télécharger Traily sur l'App Store / Google Play avec une fiche complète et une documentation utilisateur claire, **afin de** démarrer facilement mes premières randonnées.

**Critères d'acceptation**

- [ ] App validée par les processus de review Apple/Google sans rejet bloquant
- [ ] Documentation utilisateur couvrant 100% des fonctionnalités principales du MVP
- [ ] Lancement accompagné d'au moins un canal de communication actif
- [ ] PR reviewée par 1 autre membre

---

### Epic 11 — Fonctionnalités bonus (hors MVP)

_(détaillé en Parties 3 et 4 de [WBS.md](./WBS.md) ; hors PBS actuel — ce nœud n'existe pas encore dans [PBS.md](./PBS.md) ; à y intégrer formellement si retenu avant la fin du MVP)_

> Ces trois Stories ne sont **pas priorisées pour le MVP v1**. Elles restent en `Backlog` Linear, non affectées à un cycle, tant que le coordinateur ne les fait pas entrer en planning.

#### 11.1 · Point d'intérêt communautaire (type "pokestop")

`Feature` · `Frontend / Backend` · **Bonus** · Owner : Lucas (UI carte) · Maxim/Yohan (backend détection & scoring) · Recrue 1 (agrégation activité communautaire)

> **En tant que** randonneur, **je peux** découvrir des points d'intérêt communautaires — créés automatiquement par forte activité photo ou ajoutés manuellement — et gagner des points bonus en y prenant une photo, **afin de** être incité à explorer des zones populaires ou remarquables.

**Critères d'acceptation**

- [ ] Un point est créé automatiquement lorsqu'un seuil d'activité photo (nombre de contributions dans un rayon donné) est dépassé — seuil à définir avec l'équipe produit
- [ ] Un utilisateur (ou modérateur) peut créer manuellement un point d'intérêt, soumis à validation
- [ ] Une photo prise à proximité d'un point d'intérêt actif (rayon à définir) rapporte un bonus de points, cumulable une seule fois par utilisateur et par point (règle à définir)
- [ ] Les points d'intérêt sont visibles sur la carte, visuellement distincts des pins de photo classiques
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 11.2 · Avatar personnalisable + compagnon animal

`Feature` · `Frontend / Backend` · **Bonus** · Owner : Lucas/Arthur J. (UI/UX avatar, carte, logique d'obtention)

> **En tant que** randonneur, **je peux** personnaliser mon avatar affiché sur la carte GPS et débloquer un compagnon animal en progressant dans mon herbier ou en complétant des missions, **afin de** renforcer mon attachement à ma progression — avec la possibilité de le désactiver dans les options si je le souhaite.

**Critères d'acceptation**

- [ ] L'avatar utilisateur est personnalisable (a minima : apparence de base) et visible en temps réel sur l'écran carte pendant la randonnée
- [ ] Le compagnon animal se débloque selon des conditions définies (progression herbier/bestiaire et/ou missions), de façon cohérente sur 100% des cas testés
- [ ] Le compagnon suit visuellement la position de l'utilisateur sur la carte pendant la session GPS
- [ ] Un réglage dans les Paramètres permet d'activer/désactiver l'affichage du compagnon, persistant entre les sessions
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

#### 11.3 · Portage SmartWatch

`Feature` · `Frontend (nouvelle plateforme)` · **Bonus** · Owner : à définir — compétence wearOS/watchOS non couverte actuellement dans l'OBS ; à arbitrer selon capacité de l'équipe

> **En tant que** randonneur, **je peux** consulter mes métriques GPS live (distance, vitesse, altitude) directement sur ma montre connectée pendant ma randonnée, **afin de** garder mon téléphone rangé tout en suivant ma progression.

**Critères d'acceptation**

- [ ] Spike technique réalisé (faisabilité wearOS/watchOS, choix d'architecture) avant tout développement
- [ ] Les métriques live (distance, vitesse, altitude, durée) sont synchronisées entre le téléphone et la montre avec un délai sous un seuil à définir
- [ ] Le pilotage de session (pause/reprise/arrêt) est possible depuis la montre
- [ ] Autonomie de la montre mesurée sur une randonnée de test longue durée, sous un seuil acceptable à définir
- [ ] Tests ajoutés si nécessaire
- [ ] CI au vert
- [ ] PR reviewée par 1 autre membre

---

## 5. Passage en Linear

1. Une **Story** ci-dessus = un ticket Linear (ou un ticket parent avec sous-issues si l'équipe préfère découper directement au niveau des tâches WBS).
2. Le contenu de la Story se copie tel quel dans le ticket : titre = titre de la Story, description = la phrase user story, checklist = critères d'acceptation.
3. Chaque **tâche** listée dans la Partie 3 de [WBS.md](./WBS.md) peut devenir un sous-ticket dédié (règle « une tâche = un ticket = une branche = une PR », [DEVELOPMENT_PROCESS.md](./DEVELOPMENT_PROCESS.md)) si la Story est trop grosse pour un seul ticket.
4. Sur chaque ticket : Assignee = Owner par défaut de la Story (réassignable), Labels = Type + Domaine, Priorité = reprise de la Story (jamais "No priority").
5. Les Epics 5 et 6 restent en `Backlog` (non planifiés en Cycle) tant que les postes ouverts d'[OBS.md](./OBS.md) ne sont pas pourvus, sauf arbitrage du coordinateur pour réaffectation temporaire.
6. L'Epic 11 (bonus) reste en `Backlog`, hors cycle, tant qu'il n'est pas formellement intégré à la PBS et arbitré par le coordinateur.
