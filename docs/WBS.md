# Traily — Dossier de cadrage projet

## Sommaire

- **Partie 1** — Présentation du projet
- **Partie 2** — Schéma WBS (structure de découpage)
- **Partie 3** — Déroulé détaillé : livrables & tâches
- **Partie 4** — Déroulé niveau feature : résumé, exigences & critères d'acceptation

---

---

# PARTIE 1 — Présentation du projet

**Traily** est une application mobile de randonnée gamifiée, centrée sur la découverte naturaliste et la compétition communautaire.

## Concept

L'utilisateur choisit une randonnée, suit sa trace GPS en temps réel, et découvre les espèces (plantes, animaux, insectes) observables sur le parcours selon la localisation, la saison et les données communautaires. Il photographie les espèces rencontrées : chaque photo validée rapporte des points et alimente ses collections personnelles (herbier, bestiaire). Les photos sont géolocalisées et épinglées sur la trace. L'utilsateur peux aussi proposer ses propres traces.

Un système de leaderboards (par randonnée, région, équipe) et de clans permet la compétition entre randonneurs. Un module de modération communautaire valide les contributions. L'app intègre un cadre éthique sur le respect des habitats sauvages.

## Utilisateur cible

Randonneur occasionnel ou régulier, curieux de nature, qui veut une expérience plus riche qu'un simple tracker GPS — avec une dimension sociale et de progression.

## Stack technique

| Composant       | Technologie                                                     |
| --------------- | --------------------------------------------------------------- |
| Mobile          | React Native / Expo (iOS & Android)                             |
| Backend         | API REST en Go                                                  |
| Base de données | PostgreSQL + PostGIS                                            |
| Services IA     | À déterminer (identification d'espèces, prédiction faune/flore) |
| Infrastructure  | Docker, CI/CD, stockage objet S3/MinIO                          |

## Grands blocs fonctionnels

1. **Socle randonnée** — GPS, traces, difficulté, photo géolocalisée
2. **Système d'espèces** — référentiel, listes observables, identification IA, validation
3. **Collections personnelles** — herbier, bestiaire, profil
4. **Gamification** — points, badges, leaderboards
5. **Communauté & Équipes** — clans, classements, fil communautaire
6. **Modération** — dashboard, signalement, historique
7. **Cadre éthique & sécurité** — zones protégées, alertes, pédagogie
8. **Backend & IA** — APIs cœur, micro-services, agrégation de données
9. **Infrastructure** — stockage, CI/CD, monitoring, environnements
10. **Gestion de projet & qualité** — pilotage, tests, déploiement, documentation projet
11. **Fonctionnalités bonus (hors MVP)** — point d'intérêt communautaire, avatar & compagnon, portage SmartWatch _(non prioritaire pour le MVP v1 — voir [BACKLOG.md](./BACKLOG.md) Epic 11)_

---

---

# PARTIE 2 — Schéma WBS

```
Traily — Application mobile v1 (MVP)
│
├── 1. Socle randonnée
│   ├── Navigation GPS
│   ├── Traces
│   ├── Difficulté
│   └── Photo géolocalisée
│
├── 2. Système d'espèces
│   ├── Base de données
│   ├── Génération listes
│   ├── Identification photo
│   └── Pipeline validation
│
├── 3. Collections personnelles
│   ├── Herbier
│   ├── Bestiaire
│   └── Profil utilisateur
│
├── 4. Gamification
│   ├── Points
│   ├── Badges
│   └── Leaderboards
│
├── 5. Communauté & Équipes
│   ├── Gestion équipes
│   ├── Classement équipe
│   └── Fil communautaire
│
├── 6. Modération
│   ├── Dashboard modération
│   ├── Signalement
│   └── Historique
│
├── 7. Cadre éthique & sécurité
│   ├── Zones protégées
│   ├── Alertes comportementales
│   └── Contenu pédagogique
│
├── 8. Backend & IA
│   ├── API cœur
│   ├── Micro-service identification
│   ├── Micro-service recommandation
│   └── Agrégation données
│
├── 9. Infrastructure
│   ├── Stockage
│   ├── CI/CD
│   ├── Monitoring
│   └── Environnements
│
├── 10. Gestion projet & qualité
│   ├── Pilotage projet
│   ├── Tests
│   ├── Déploiement & lancement
│   └── Documentation projet
│
└── 11. Fonctionnalités bonus (hors MVP)
    ├── Point d'intérêt communautaire
    ├── Avatar personnalisable + compagnon
    └── Portage SmartWatch
```


---

---

# PARTIE 3 — Déroulé détaillé : livrables & tâches

## 1. Socle randonnée

### 1.1.1 Navigation GPS temps réel — mode foreground

**Livrable** : Écran de suivi GPS en temps réel opérationnel en foreground, affichant position, trace en cours et métriques live.

**Tâches :**

- Setup du module de géolocalisation (Expo Location) et permissions iOS/Android
- Écran carte avec affichage position utilisateur en temps réel
- Suivi de la trace pendant la randonnée (polyline dynamique sur carte)
- Gestion des états (pause / reprise / arrêt de la session GPS)
- Affichage des métriques live (vitesse, distance parcourue, altitude)
- Tests sur device réel (précision, dérive GPS, zones de mauvaise couverture)

### 1.1.2 Navigation GPS temps réel — mode background

**Livrable** : Tracking GPS fonctionnel en arrière-plan (app fermée/minimisée) avec notification persistante.

**Tâches :**

- Configuration du tracking en arrière-plan (Expo TaskManager / Background Location)
- Gestion du cycle de vie app (mise en veille, kill par l'OS, reprise)
- Notification persistante iOS/Android pendant l'enregistrement
- Synchronisation des points GPS collectés en arrière-plan avec la session active
- Tests spécifiques Android (Doze mode, économiseurs de batterie constructeurs)

### 1.1.3 Gestion autonomie batterie / optimisation GPS

**Livrable** : Mécanisme d'échantillonnage adaptatif et mode économie de batterie configurable.

**Tâches :**

- Stratégie d'échantillonnage adaptatif (fréquence GPS selon vitesse de déplacement)
- Filtrage des points aberrants (bruit GPS, sauts de position)
- Mode économie de batterie (réduction précision configurable par l'utilisateur)
- Tests de consommation batterie sur randonnée longue durée

### 1.2.1 Enregistrement de traces GPS

**Livrable** : Trace de randonnée sauvegardée en base de données, associée à l'utilisateur, consultable a posteriori.

**Tâches :**

- Modèle de données trace (table PostGIS : geometry, métadonnées, utilisateur)
- API Go — endpoint création/sauvegarde de trace (POST /trails)
- Compression/simplification du tracé GPS avant envoi (algorithme Douglas-Peucker)
- Upload et retry en cas de perte de connexion pendant la randonnée
- Écran de fin de randonnée (résumé, validation, sauvegarde)

### 1.2.2 Visualisation de traces (carte, profil altimétrique)

**Livrable** : Écran détail d'une trace avec carte, profil altimétrique et informations associées.

**Tâches :**

- Affichage du tracé complet sur carte (rendu polyline + points d'intérêt)
- Génération du profil altimétrique (graphique dénivelé/distance)
- Écran détail trace (infos générales, stats, photos épinglées)
- Mode replay de la randonnée (lecture animée du parcours) — à confirmer pour le MVP

### 1.2.3 Exploration et recherche de traces existantes

**Livrable** : Écran de découverte permettant de trouver des traces existantes autour de l'utilisateur.

**Tâches :**

- API Go — endpoint recherche géospatiale de traces (PostGIS, requêtes par bbox/rayon)
- Écran liste/carte des traces disponibles autour de l'utilisateur
- Pagination et chargement progressif des résultats
- Écran détail trace côté "découverte" (avant de la faire)

### 1.2.4 Filtres de recherche (durée, difficulté, région, espèces présentes)

**Livrable** : Panneau de filtres fonctionnel sur l'écran de recherche de traces.

**Tâches :**

- API Go — paramètres de filtrage sur l'endpoint de recherche
- UI des filtres (bottom sheet / panneau de filtres)
- Filtre par espèces observables (dépend du module Système d'espèces)
- Sauvegarde des filtres favoris de l'utilisateur

### 1.3.1 Calcul du dénivelé

**Livrable** : Dénivelé positif/négatif calculé et affiché pour chaque trace.

**Tâches :**

- Récupération des données d'altitude (API élévation ou données embarquées GPS)
- Algorithme de calcul dénivelé positif/négatif à partir du tracé
- Lissage des données pour éviter le bruit d'altitude GPS

### 1.3.2 Estimation de durée (règle de Naismith)

**Livrable** : Durée estimée affichée pour chaque trace avant de la démarrer.

**Tâches :**

- Implémentation de la règle de Naismith (+ ajustements type Tobler/Aitken si besoin)
- Calibrage avec facteur de forme utilisateur (niveau randonneur, rythme moyen)
- Affichage de la durée estimée avec fourchette (rapide/moyen/lent)

### 1.3.3 Classification du niveau de difficulté

**Livrable** : Badge de difficulté affiché sur chaque trace.

**Tâches :**

- Définition des règles de classification (facile/modéré/difficile/expert)
- API Go — calcul et stockage du niveau à la création de la trace
- Affichage visuel (badge couleur/icône) sur les traces

### 1.4.1 Prise de photo in-app avec métadonnées GPS

**Livrable** : Fonctionnalité de prise de photo intégrée, avec capture des métadonnées.

**Tâches :**

- Intégration caméra in-app (Expo Camera) avec permissions
- Capture des métadonnées au moment de la prise (GPS, timestamp, altitude)
- Prévisualisation et confirmation avant envoi
- Gestion mode hors-ligne (queue locale si pas de réseau)

### 1.4.2 Épinglage de la photo sur la trace

**Livrable** : Photos visibles et positionnées correctement sur la trace correspondante.

**Tâches :**

- API Go — endpoint association photo ↔ trace ↔ position sur le tracé
- Affichage des pins photo sur la carte de la trace
- Écran galerie photos de la trace (vue chronologique)

### 1.4.3 Gestion de la qualité / compression avant envoi

**Livrable** : Pipeline de compression et d'upload des photos vers le stockage objet.

**Tâches :**

- Compression/redimensionnement côté client avant upload
- Upload vers S3/MinIO (URL présignée depuis l'API Go)
- Génération de thumbnails côté backend
- Gestion des erreurs d'upload et retry

---

## 2. Système d'espèces

### 2.1 Base de données d'espèces

**Livrable** : Référentiel d'espèces peuplé et interrogeable en base de données.

**Tâches :**

- Modèle de données (flore, faune, insectes) avec attributs taxonomiques
- Import des données de référence (sources naturalistes type GBIF, INPN, iNaturalist)
- Structuration des données de localisation par espèce (aires de répartition)
- Structuration des données de saisonnalité par espèce (périodes d'observation)
- Enrichissement des fiches (photos de référence, descriptions, statut de protection)
- Interface d'administration pour gérer/corriger le référentiel d'espèces

### 2.2 Génération de listes d'espèces observables

**Livrable** : Service générant la liste d'espèces observables pour une trace donnée.

**Tâches :**

- Algorithme de croisement localisation (zone géographique de la trace) + saison
- API Go — endpoint génération de la liste d'espèces observables pour une trace
- Intégration des données communautaires dans le calcul de probabilité
- Pondération/scoring des espèces par probabilité d'observation
- Mise à jour dynamique des listes (nouvelle saison, nouvelles observations)
- Cache des listes générées pour limiter la charge de calcul

### 2.3 Identification d'espèce par photo

**Livrable** : Service d'identification fonctionnel, intégré à l'app, retournant une espèce probable à partir d'une photo.

**Tâches :**

- Étude comparative des approches (modèle embarqué vs service distant/API tierce)
- Choix et validation de l'architecture technique (spike technique)
- Constitution/sélection du jeu de données d'entraînement (ou choix d'un modèle pré-entraîné)
- Entraînement ou fine-tuning du modèle d'identification
- Déploiement du service d'inférence (micro-service dédié)
- Intégration côté mobile (envoi photo → résultat d'identification)
- Gestion des cas d'incertitude (scores de confiance bas, plusieurs candidats)
- Fallback manuel si l'IA ne peut pas identifier l'espèce

### 2.4 Pipeline de validation des contributions

**Livrable** : Workflow complet de validation d'une contribution, de la soumission à la décision finale.

**Tâches :**

- Définition du workflow de validation (étapes, statuts possibles)
- Pré-validation automatique par IA (score de confiance comme filtre)
- File d'attente de modération humaine pour les cas ambigus
- Règles de validation (seuils de confiance, double-validation communautaire, etc.)
- Système de réputation contributeur
- Notification utilisateur du statut de sa contribution

---

## 3. Collections personnelles

### 3.1 Herbier (collection flore)

**Livrable** : Écran herbier fonctionnel affichant la collection flore de l'utilisateur.

**Tâches :**

- Modèle de données collection utilisateur (espèces découvertes, dates, photos)
- Interface type "Pokédex" — grille des espèces avec état découvert/non découvert
- Logique d'ajout automatique à l'herbier lors d'une validation de contribution
- Filtres et tri de l'herbier (par famille, rareté, date de découverte)

### 3.2 Bestiaire (collection faune/insectes)

**Livrable** : Écran bestiaire fonctionnel, équivalent à l'herbier pour la faune/insectes.

**Tâches :**

- Réutilisation du modèle de collection pour faune/insectes
- Interface type "Pokédex" dédiée (différenciation visuelle flore/faune)
- Logique d'ajout automatique au bestiaire lors d'une validation

### 3.3 Fiche espèce détaillée (commune herbier/bestiaire)

**Livrable** : Écran fiche espèce complet, accessible depuis l'herbier, le bestiaire ou une trace.

**Tâches :**

- Écran fiche espèce (infos naturalistes, statut de protection, conseils éthiques)
- Affichage des photos personnelles de l'utilisateur pour cette espèce
- Affichage de la fréquence/rareté de l'espèce dans la communauté

### 3.4 Profil utilisateur

**Livrable** : Écran profil complet avec statistiques et historique.

**Tâches :**

- Écran profil avec statistiques globales de collection (% complété, nombre d'espèces)
- Historique des randonnées effectuées
- Historique des découvertes chronologique
- Paramètres du profil (photo, pseudo, préférences)

---

## 4. Gamification

### 4.1 Système de points

**Livrable** : Mécanisme d'attribution de points fonctionnel et traçable.

**Tâches :**

- Définition du barème de points (par espèce, par rareté, par première découverte)
- API Go — service de calcul et d'attribution des points
- Historique des points gagnés par action (traçabilité)
- Affichage du solde de points et de la progression sur le profil

### 4.2 Badges et progression

**Livrable** : Système de badges fonctionnel avec notifications de déblocage.

**Tâches :**

- Définition des badges et jalons (liste, conditions de déblocage)
- Logique d'attribution automatique (déclenchée par événements)
- Interface d'affichage des badges obtenus/à débloquer
- Notification de déblocage de badge

### 4.3 Leaderboards

**Livrable** : Classements fonctionnels par randonnée, région et équipe.

**Tâches :**

- Modèle de données classement (scores agrégés par période/périmètre)
- API Go — endpoint leaderboard par randonnée
- API Go — endpoint leaderboard par région
- API Go — endpoint leaderboard par équipe
- Interface classement (podium, position de l'utilisateur, pagination)
- Gestion du rafraîchissement/cache des classements

---

## 5. Communauté & Équipes

### 5.1 Gestion des équipes / clans

**Livrable** : Fonctionnalité complète de création et gestion d'équipe.

**Tâches :**

- Modèle de données équipe (nom, avatar, membres, rôles)
- API Go — création et paramétrage d'équipe
- API Go — invitation de membres
- Gestion des rôles au sein de l'équipe
- Interface de gestion d'équipe

### 5.2 Classement d'équipe

**Livrable** : Classement d'équipe fonctionnel et exact.

**Tâches :**

- Calcul du score agrégé d'équipe
- API Go — endpoint classement d'équipe
- Interface classement d'équipe

### 5.3 Fil communautaire

**Livrable** : Fil d'activité fonctionnel par trace.

**Tâches :**

- Modèle de données fil de contributions (par trace)
- API Go — endpoint fil de contributions par trace
- Interface fil d'activité
- Interactions sociales de base (likes) — à confirmer pour le MVP

---

## 6. Modération

### 6.1 Dashboard de modération

**Livrable** : Interface de modération opérationnelle pour l'équipe de modérateurs.

**Tâches :**

- Interface de revue des photos/contributions soumises
- Outils de validation / rejet en un clic
- Filtres et priorisation de la file de modération
- Gestion des rôles modérateur

### 6.2 Signalement utilisateur

**Livrable** : Fonctionnalité de signalement opérationnelle de bout en bout.

**Tâches :**

- Fonctionnalité de signalement in-app
- API Go — endpoint création de signalement
- Traitement des signalements dans le dashboard
- Notification de suivi à l'utilisateur signalant

### 6.3 Historique de modération

**Livrable** : Journal consultable de toutes les décisions de modération.

**Tâches :**

- Journal des décisions de modération
- Interface de consultation de l'historique
- Traçabilité et export pour audit

---

## 7. Cadre éthique & sécurité

### 7.1 Zones protégées

**Livrable** : Affichage des zones protégées sur la carte de l'app.

**Tâches :**

- Intégration des données de zones sensibles/protégées (sources officielles)
- Affichage des zones sur la carte (couche dédiée)
- Mise à jour périodique des données de zonage

### 7.2 Alertes comportementales

**Livrable** : Système de notifications contextuelles pendant la randonnée.

**Tâches :**

- Définition des règles de distance à respecter selon le type d'espèce
- Détection de proximité utilisateur/zone sensible ou espèce protégée
- Notifications contextuelles pendant la randonnée

### 7.3 Contenu pédagogique

**Livrable** : Conseils éthiques intégrés aux fiches espèces et à l'onboarding.

**Tâches :**

- Rédaction des conseils éthiques par espèce/catégorie
- Intégration des conseils dans les fiches espèces
- Écran/onboarding dédié au cadre éthique de l'app

---

## 8. Backend & IA

### 8.1 API cœur

**Livrable** : Ensemble des APIs Trails/Users/Sessions/Espèces/Scores documentées et déployées.

**Tâches :**

- API Trails (CRUD traces, recherche géospatiale)
- API Users (inscription, authentification, gestion de profil)
- API Sessions (gestion des sessions de randonnée en cours)
- API Espèces (référentiel, listes observables)
- API Scores (points, classements)
- Documentation API (OpenAPI/Swagger)
- Gestion des droits et authentification/autorisation

### 8.2 Micro-service identification d'espèces

**Livrable** : Micro-service déployé, exposant un endpoint d'inférence pour l'identification photo.

**Tâches :**

- Endpoint photo → espèce probable
- Gestion de la montée en charge (queue, scalabilité horizontale)
- Monitoring de la performance et de la précision du modèle en production

### 8.3 Micro-service recommandation de traces

**Livrable** : Service de recommandation exposé via API, utilisé par le mobile.

**Tâches :**

- Définition des critères de recommandation
- Algorithme de recommandation (scoring simple, évolutif vers ML)
- API de recommandation exposée au mobile

### 8.4 Agrégation de données communautaires

**Livrable** : Pipeline d'agrégation opérationnel, alimentant les listes d'espèces observables.

**Tâches :**

- Pipeline d'enrichissement des listes d'espèces à partir des observations validées
- Traitement batch (recalcul périodique des probabilités d'observation)
- Traitement temps réel/quasi temps réel pour les mises à jour ponctuelles

---

## 9. Infrastructure

### 9.1 Stockage

**Livrable** : Infrastructure de stockage objet opérationnelle (S3/MinIO).

**Tâches :**

- Configuration S3 / MinIO (buckets, politiques d'accès)
- Génération d'URLs présignées pour upload direct depuis le mobile
- Politique de rétention et sauvegarde des photos
- Stratégie de nommage et d'organisation des objets stockés

### 9.2 CI/CD

**Livrable** : Pipelines CI/CD opérationnels pour mobile, backend et services IA.

**Tâches :**

- Pipeline CI/CD mobile (build, tests, distribution beta iOS/Android)
- Pipeline CI/CD backend Go
- Pipeline CI/CD services IA
- Gestion des secrets et variables d'environnement par pipeline

### 9.3 Monitoring

**Livrable** : Système de monitoring et d'alerting opérationnel sur l'ensemble des services.

**Tâches :**

- Mise en place des outils de monitoring (logs, métriques applicatives)
- Dashboards de suivi
- Système d'alertes
- Monitoring spécifique du micro-service IA

### 9.4 Environnements

**Livrable** : Environnements dev/staging/production opérationnels et isolés.

**Tâches :**

- Environnement de développement (Docker Compose local)
- Environnement de staging
- Environnement de production
- Procédure de gestion des migrations de base de données

---

## 10. Gestion de projet & qualité

### 10.1 Pilotage projet

**Livrable** : Planning de projet établi avec jalons suivis.

**Tâches :**

- Définition du planning et des jalons (sprints, releases)
- Suivi budgétaire
- Rituels de suivi (points d'avancement, revues de sprint)

### 10.2 Tests

**Livrable** : Stratégie de tests mise en œuvre avec couverture mesurable.

**Tâches :**

- Stratégie de tests (unitaires, intégration, end-to-end)
- Mise en place des tests unitaires (mobile + backend)
- Tests d'intégration (API, base de données)
- Campagne de tests utilisateurs (beta fermée)
- Correction des retours de la beta

### 10.3 Déploiement & lancement

**Livrable** : Application publiée et disponible sur les stores.

**Tâches :**

- Préparation des fiches App Store / Google Play
- Publication en review sur les stores
- Rédaction de la documentation utilisateur
- Plan de communication de lancement

### 10.4 Documentation projet

**Livrable** : Documentation projet à jour, versionnée dans le dépôt `docs` et accessible à toute l'équipe.

**Tâches :**

- Rédaction des documents de cadrage (PBS, WBS, OBS, backlog initial)
- Rédaction des documents de process et de qualité (process de développement, Definition of Done, Quality Plans, guide Linear, outils)
- Rédaction de la documentation technique (architecture, exigences matérielles)
- Guide d'ordre de lecture des documents
- Mise à jour de la documentation concernée à chaque PR modifiant une interface ou un comportement
- Synchronisation du dépôt miroir de la documentation

---

## 11. Fonctionnalités bonus (hors MVP)

> Livrables non priorisés pour le MVP v1 ([BACKLOG.md](./BACKLOG.md) Epic 11) et n'existant pas encore dans la branche PBS actuelle ([PBS.md](./PBS.md)) — à y intégrer formellement si retenus, sous arbitrage du coordinateur.

### 11.1 Point d'intérêt communautaire (type "pokestop")

**Livrable** : Points d'intérêt visibles sur la carte, générés automatiquement par forte activité communautaire ou créés manuellement, avec bonus de points associé.

**Tâches :**

- Définition du seuil d'activité photo déclenchant la création automatique d'un point d'intérêt
- API Go — endpoint de création automatique et manuelle de point d'intérêt (avec validation pour la création manuelle)
- Algorithme de détection de forte activité photo dans un rayon donné
- Logique de bonus de points à la prise de photo à proximité d'un point d'intérêt (règle de cumul à définir)
- Affichage des points d'intérêt sur la carte, différenciés visuellement des pins photo classiques

### 11.2 Avatar personnalisable + compagnon animal

**Livrable** : Avatar personnalisable affiché sur la carte GPS, et compagnon animal déblocable selon la progression, avec option de désactivation.

**Tâches :**

- Interface de personnalisation d'avatar (apparence de base)
- Affichage temps réel de l'avatar sur l'écran carte pendant la session GPS
- Définition des conditions de déblocage du compagnon (progression herbier/bestiaire, missions)
- Logique d'attribution et de suivi visuel du compagnon sur la carte
- Réglage dans les Paramètres pour activer/désactiver l'affichage du compagnon (persistant)

### 11.3 Portage SmartWatch

**Livrable** : Application compagnon SmartWatch (wearOS/watchOS) affichant les métriques GPS live et permettant le pilotage de session.

**Tâches :**

- Spike technique de faisabilité (wearOS/watchOS, choix d'architecture)
- Synchronisation des métriques live (distance, vitesse, altitude, durée) téléphone ↔ montre
- Pilotage de session (pause/reprise/arrêt) depuis la montre
- Tests d'autonomie de la montre sur randonnée longue durée

---

---

# PARTIE 4 — Déroulé niveau feature : résumé, exigences & critères

> Regroupement des points ci-dessus au niveau feature (1.1, 1.2, 5.1, etc.). Pour la section 1, ce niveau regroupe plusieurs points détaillés (ex : 1.1 regroupe 1.1.1/1.1.2/1.1.3). Pour les sections 2 à 10, il n'y a pas de sous-niveau supplémentaire : le niveau feature correspond directement aux points de la Partie 1.

## 1. Socle randonnée

### 1.1 Module navigation GPS

**Résumé** : Suivi GPS en temps réel de l'utilisateur pendant sa randonnée, en foreground comme en background, avec une gestion optimisée de l'autonomie batterie.

**Exigences fonctionnelles**

- L'app affiche et enregistre la position GPS en continu, en foreground comme en background
- L'utilisateur peut piloter l'enregistrement (démarrage / pause / reprise / arrêt)
- Les métriques live (distance, vitesse, altitude) sont affichées et mises à jour en temps réel
- Le tracking résiste aux contraintes du système d'exploitation (mise en veille, kill applicatif, gestion batterie constructeur)
- La fréquence d'échantillonnage GPS s'adapte pour optimiser l'autonomie

**Critères d'acceptation**

- Position affichée avec un délai < 3s ; distance calculée à ±5% d'une mesure de référence
- Perte de points GPS en arrière-plan < 5% sur une session de 3h, testé sur au moins 3 constructeurs Android
- Consommation batterie sous le seuil cible défini avec l'équipe, sur une randonnée de test
- Aucun crash constaté sur une session de 2h+, testé sur iOS et Android

### 1.2 Module traces

**Résumé** : Enregistrement, visualisation et découverte de traces de randonnée, avec recherche filtrée.

**Exigences fonctionnelles**

- Chaque randonnée terminée est sauvegardée comme trace consultable, associée à l'utilisateur
- L'utilisateur peut visualiser une trace (carte, profil altimétrique, statistiques, photos)
- L'utilisateur peut explorer et rechercher des traces existantes autour de lui
- Des filtres (durée, difficulté, région, espèces observables) affinent la recherche

**Critères d'acceptation**

- Trace visible dans l'historique < 5s après la fin de session ; aucune perte en cas de coupure réseau
- Écran détail chargé en < 2s ; profil altimétrique cohérent avec les données enregistrées
- Recherche géospatiale de traces en < 2s ; application des filtres en < 1s

### 1.3 Estimation de difficulté

**Résumé** : Calcul automatique du dénivelé, de la durée estimée et du niveau de difficulté global de chaque trace.

**Exigences fonctionnelles**

- Le dénivelé positif/négatif est calculé et lissé à partir des données d'altitude
- La durée est estimée selon la règle de Naismith, avec une fourchette rapide/moyen/lent
- Un niveau de difficulté global (facile/modéré/difficile/expert) est calculé et affiché

**Critères d'acceptation**

- Dénivelé calculé à ±10% d'une source de référence
- Durée estimée cohérente à ±20% par rapport à des temps réels observés
- Classification de difficulté cohérente avec les règles définies, vérifiée sur un jeu de traces classées manuellement

### 1.4 Photo géolocalisée

**Résumé** : Prise de photo d'espèces pendant la randonnée, avec géolocalisation automatique, épinglage sur la trace et gestion de la qualité/upload.

**Exigences fonctionnelles**

- Prise de photo in-app avec capture des métadonnées (GPS, altitude, horodatage)
- Fonctionnement en mode hors-ligne avec file d'attente d'envoi différé
- Épinglage des photos sur la trace et consultation en galerie chronologique
- Compression et upload optimisés vers le stockage objet (S3/MinIO)

**Critères d'acceptation**

- 100% des photos prises in-app contiennent des métadonnées GPS valides (hors absence totale de signal)
- Photos prises hors-ligne envoyées automatiquement au retour du réseau, sans perte sur un test de 50 prises
- Position du pin photo sur la carte à ±20m de la prise réelle
- Taux de succès d'upload > 98% en conditions de réseau instable simulé

---

## 2. Système d'espèces

### 2.1 Base de données d'espèces

**Résumé** : Référentiel central d'espèces (flore, faune, insectes) avec données de localisation et saisonnalité.

**Exigences fonctionnelles**

- Le référentiel couvre flore, faune et insectes avec attributs taxonomiques standards
- Chaque espèce dispose de données de localisation (aire de répartition) et de saisonnalité
- Une interface d'administration permet de corriger/enrichir les fiches

**Critères d'acceptation**

- Le référentiel contient un nombre minimum d'espèces couvrant la zone de lancement (seuil à définir avec l'équipe produit)
- 100% des espèces ont au moins : nom, catégorie, zone de répartition, période d'observation
- Une modification via l'admin est répercutée côté app en < 5 minutes

### 2.2 Génération de listes d'espèces observables

**Résumé** : Génération dynamique de la liste d'espèces observables pour une trace, selon localisation, saison et données communautaires.

**Exigences fonctionnelles**

- La liste croise localisation de la trace, saison actuelle et données communautaires
- Chaque espèce est associée à un score de probabilité d'observation
- La liste se met à jour automatiquement dans le temps (changement de saison)

**Critères d'acceptation**

- Génération d'une liste en < 2s avec cache, < 5s sans cache
- Liste jugée pertinente par un expert naturaliste sur un échantillon de traces test
- Régénération correcte de la liste au changement de saison

### 2.3 Identification d'espèce par photo

**Résumé** : Service IA d'identification d'espèce à partir d'une photo prise par l'utilisateur.

**Exigences fonctionnelles**

- L'utilisateur envoie une photo et reçoit une ou plusieurs propositions d'espèces avec score de confiance
- En cas de confiance faible, un choix manuel parmi une liste de candidats est proposé
- Le service supporte un volume de requêtes concurrentes sans dégradation majeure

**Critères d'acceptation**

- Taux de bonne identification (top-1) mesuré et documenté sur un jeu de test (seuil cible à définir)
- Temps de réponse < 5s pour 95% des requêtes
- Fallback manuel systématiquement proposé sous le seuil de confiance défini

### 2.4 Pipeline de validation des contributions

**Résumé** : Workflow de validation des contributions, combinant pré-validation IA et modération humaine.

**Exigences fonctionnelles**

- Chaque contribution passe par une pré-validation IA puis, si nécessaire, une modération humaine
- L'utilisateur est notifié du statut final de sa contribution
- Le système prend en compte la réputation du contributeur

**Critères d'acceptation**

- 100% des contributions reçoivent un statut final sous un délai maximum défini (ex : 48h)
- Taux de contributions nécessitant une modération humaine sous un seuil cible une fois l'IA calibrée
- Notification reçue dans les 5 minutes suivant la décision

---

## 3. Collections personnelles

### 3.1 Herbier (collection flore)

**Résumé** : Collection personnelle des espèces de flore découvertes, présentée façon "Pokédex".

**Exigences fonctionnelles**

- Toutes les espèces de flore découvertes et validées apparaissent dans l'herbier
- Les espèces non découvertes sont visibles mais verrouillées
- L'utilisateur peut filtrer/trier sa collection

**Critères d'acceptation**

- Contribution flore validée visible dans l'herbier < 1 minute
- Pourcentage de complétion exact par rapport au référentiel local
- Filtres/tri fonctionnels sur une collection de test de 100+ espèces

### 3.2 Bestiaire (collection faune/insectes)

**Résumé** : Collection personnelle des espèces de faune/insectes découvertes, équivalent du herbier.

**Exigences fonctionnelles**

- Toutes les espèces de faune/insectes découvertes et validées apparaissent dans le bestiaire
- Différenciation visuelle claire avec l'herbier

**Critères d'acceptation**

- Mêmes critères que 3.1, appliqués à la faune/insectes

### 3.3 Fiche espèce détaillée

**Résumé** : Fiche détaillée d'une espèce, commune à l'herbier, au bestiaire et aux traces.

**Exigences fonctionnelles**

- La fiche affiche les infos naturalistes, le statut de protection et les conseils éthiques
- Les photos personnelles de l'utilisateur pour cette espèce sont affichées
- La rareté/fréquence communautaire de l'espèce est affichée

**Critères d'acceptation**

- Fiche chargée en < 2s
- 100% des fiches affichent au minimum nom, description, statut de protection
- Aucune fuite de données entre utilisateurs sur les photos personnelles affichées

### 3.4 Profil utilisateur

**Résumé** : Profil utilisateur centralisant statistiques de collection et historique de randonnées.

**Exigences fonctionnelles**

- Les statistiques de collection (%, nombre d'espèces) sont affichées et à jour
- L'historique des randonnées est consultable
- L'utilisateur peut modifier ses informations de profil

**Critères d'acceptation**

- Statistiques exactement conformes aux données réelles de la base
- Modification du profil persistée et reflétée immédiatement
- Historique trié par date décroissante par défaut

---

## 4. Gamification

### 4.1 Système de points

**Résumé** : Attribution automatique de points selon les contributions validées de l'utilisateur.

**Exigences fonctionnelles**

- Les points sont attribués automatiquement selon le barème défini
- Un historique des points gagnés est consultable
- Le solde de points est visible sur le profil

**Critères d'acceptation**

- Points crédités en < 1 minute après validation d'une contribution
- Barème appliqué de façon cohérente sur 100% des cas testés
- Historique fidèle à chaque gain de points avec sa raison

### 4.2 Badges et progression

**Résumé** : Système de badges et jalons de progression, avec notifications de déblocage.

**Exigences fonctionnelles**

- Les badges se débloquent automatiquement selon les conditions définies
- L'utilisateur est notifié immédiatement lors d'un déblocage
- Les badges obtenus et à obtenir sont visibles dans l'app

**Critères d'acceptation**

- Badge débloqué au plus tard 1 minute après que la condition est remplie
- 100% des conditions de déblocage testées et validées avant mise en production
- Notification de déblocage fiable sur iOS et Android

### 4.3 Leaderboards

**Résumé** : Classements compétitifs par randonnée, région et équipe.

**Exigences fonctionnelles**

- Chaque type de classement affiche un podium et la position de l'utilisateur
- Les classements se rafraîchissent à intervalle régulier ou en temps réel
- Les classements sont paginés pour les grands volumes d'utilisateurs

**Critères d'acceptation**

- Classement cohérent avec les scores réels en base
- Temps de chargement d'un classement < 2s
- Rafraîchissement conforme à la fréquence cible définie

---

## 5. Communauté & Équipes

### 5.1 Gestion des équipes / clans

**Résumé** : Création et gestion d'équipes/clans pour la compétition communautaire.

**Exigences fonctionnelles**

- Un utilisateur peut créer une équipe et devenir automatiquement admin
- Les membres peuvent être invités et rejoindre l'équipe
- Les rôles (admin/membre) déterminent les droits de gestion

**Critères d'acceptation**

- Création d'équipe et ajout de membre effectifs immédiatement après action
- Membre non-admin ne pouvant pas accéder aux fonctions de gestion réservées à l'admin
- Mécanisme d'invitation fiable sur un échantillon testé

### 5.2 Classement d'équipe

**Résumé** : Classement agrégé des équipes selon les scores de leurs membres.

**Exigences fonctionnelles**

- Le score d'équipe est calculé à partir des scores individuels des membres
- Le classement d'équipe est accessible depuis l'app

**Critères d'acceptation**

- Score d'équipe conforme exactement à l'agrégation définie
- Classement mis à jour après toute contribution d'un membre en < 5 minutes

### 5.3 Fil communautaire

**Résumé** : Fil d'activité affichant les contributions récentes sur une trace donnée.

**Exigences fonctionnelles**

- Les contributions récentes sur une trace sont visibles dans un fil chronologique
- Chaque entrée affiche le contributeur, la photo/découverte, la date

**Critères d'acceptation**

- Nouvelle contribution validée visible dans le fil < 1 minute
- Fil trié par ordre chronologique décroissant sans erreur

---

## 6. Modération

### 6.1 Dashboard de modération

**Résumé** : Interface dédiée permettant aux modérateurs de traiter les contributions soumises.

**Exigences fonctionnelles**

- Le dashboard liste les contributions en attente avec les infos nécessaires à la décision
- Le modérateur peut valider ou rejeter en un clic, avec motif optionnel
- L'accès est restreint aux comptes ayant le rôle modérateur

**Critères d'acceptation**

- Utilisateur sans rôle modérateur ne pouvant pas accéder au dashboard
- Décision de modération appliquée immédiatement (statut mis à jour < 5s)
- File de modération correctement triée par priorité définie

### 6.2 Signalement utilisateur

**Résumé** : Fonctionnalité permettant aux utilisateurs de signaler du contenu ou un comportement problématique.

**Exigences fonctionnelles**

- L'utilisateur peut signaler une photo, une contribution ou un comportement
- Le signalement est visible dans le dashboard de modération
- L'utilisateur signalant reçoit un accusé/suivi

**Critères d'acceptation**

- Signalement visible dans le dashboard < 1 minute
- 100% des signalements traçables (auteur, cible, motif, date)

### 6.3 Historique de modération

**Résumé** : Journal traçable de toutes les décisions prises par les modérateurs.

**Exigences fonctionnelles**

- Chaque décision (qui, quoi, quand, pourquoi) est enregistrée
- L'historique est consultable et exportable pour audit

**Critères d'acceptation**

- 100% des décisions journalisées sans exception
- Export disponible dans un format exploitable (CSV/JSON)

---

## 7. Cadre éthique & sécurité

### 7.1 Zones protégées

**Résumé** : Affichage cartographique des zones naturelles sensibles ou protégées.

**Exigences fonctionnelles**

- Les zones sensibles/protégées sont visibles sur une couche dédiée de la carte
- Les données de zonage sont mises à jour périodiquement depuis une source officielle

**Critères d'acceptation**

- Zones affichées conformes aux données officielles à la date de mise à jour
- Couche de zones sans impact perceptible sur la performance de la carte (< 2s)

### 7.2 Alertes comportementales

**Résumé** : Notifications contextuelles incitant au respect des espèces et zones sensibles.

**Exigences fonctionnelles**

- Une alerte est déclenchée quand l'utilisateur approche d'une zone sensible ou d'une espèce protégée
- Les alertes sont non intrusives

**Critères d'acceptation**

- Alerte déclenchée dans un rayon cohérent avec la règle définie
- Aucune alerte en double/spam pour une même situation

### 7.3 Contenu pédagogique

**Résumé** : Contenu éducatif sur le respect des habitats, intégré aux fiches espèces et à l'onboarding.

**Exigences fonctionnelles**

- Chaque fiche espèce sensible contient un conseil éthique adapté
- Un écran d'onboarding présente le cadre éthique de l'app au premier lancement

**Critères d'acceptation**

- 100% des espèces protégées du référentiel avec un conseil éthique associé
- Onboarding éthique affiché une fois au premier lancement, accessible depuis les paramètres

---

## 8. Backend & IA

### 8.1 API cœur

**Résumé** : Ensemble des APIs métier (traces, utilisateurs, sessions, espèces, scores) supportant l'ensemble de l'app.

**Exigences fonctionnelles**

- Chaque API expose les opérations CRUD nécessaires aux fonctionnalités du mobile
- L'authentification/autorisation protège les endpoints sensibles
- La documentation API est à jour et accessible à l'équipe

**Critères d'acceptation**

- 100% des endpoints prévus documentés (OpenAPI/Swagger) et testés
- Endpoints protégés rejetant les requêtes non authentifiées
- Temps de réponse moyen < 300ms en conditions normales de charge

### 8.2 Micro-service identification d'espèces

**Résumé** : Service d'inférence IA dédié à l'identification d'espèces à partir de photos.

**Exigences fonctionnelles**

- Le service reçoit une photo et retourne une ou plusieurs espèces probables avec score
- Le service supporte la montée en charge

**Critères d'acceptation**

- Réponse < 5s pour 95% des requêtes sous charge nominale
- Disponibilité > 99% sur la période de test définie

### 8.3 Micro-service recommandation de traces

**Résumé** : Service de recommandation de traces personnalisé selon le profil utilisateur.

**Exigences fonctionnelles**

- Les recommandations tiennent compte du profil, historique et score de l'utilisateur
- L'API retourne une liste de traces classées par pertinence

**Critères d'acceptation**

- Recommandations jugées pertinentes sur un échantillon de test (validation qualitative)
- Temps de réponse de l'API < 1s

### 8.4 Agrégation de données communautaires

**Résumé** : Pipeline enrichissant les données d'espèces observables à partir des contributions validées.

**Exigences fonctionnelles**

- Les observations validées enrichissent périodiquement les données de probabilité par espèce/zone
- Le pipeline tourne de façon automatisée (batch planifié)

**Critères d'acceptation**

- Pipeline exécuté sans erreur sur le planning défini et monitoré
- Données enrichies visibles dans les listes d'espèces observables sous un délai défini (ex : 24h max)

---

## 9. Infrastructure

### 9.1 Stockage

**Résumé** : Infrastructure de stockage objet pour les photos et médias de l'app.

**Exigences fonctionnelles**

- Les buckets sont configurés avec les politiques d'accès appropriées
- L'app peut uploader directement via URL présignée
- Une politique de rétention/sauvegarde est en place

**Critères d'acceptation**

- Upload via URL présignée fonctionnel de bout en bout sans passer par le backend applicatif
- Accès non autorisés aux buckets refusés
- Procédure de sauvegarde/restauration testée et documentée

### 9.2 CI/CD

**Résumé** : Automatisation du build, des tests et du déploiement pour tous les composants.

**Exigences fonctionnelles**

- Chaque push/merge déclenche automatiquement build et tests
- Le déploiement en staging/production peut être déclenché depuis le pipeline
- Les secrets sont gérés de façon sécurisée

**Critères d'acceptation**

- Déploiement complet (build → tests → staging) sans intervention manuelle
- Pipeline bloquant le déploiement si les tests ne passent pas
- Aucun secret visible en clair dans les logs ou le code source

### 9.3 Monitoring

**Résumé** : Supervision de la santé et de la performance de l'ensemble des services.

**Exigences fonctionnelles**

- Les métriques clés (latence, erreurs, disponibilité) sont collectées pour chaque service
- Des alertes sont déclenchées automatiquement en cas d'anomalie
- Le service IA est monitoré spécifiquement (dérive de précision)

**Critères d'acceptation**

- Dashboard centralisé affichant l'état de santé de tous les services en temps réel
- Alerte test déclenchant une notification à l'équipe technique en < 5 min
- Métriques conservées sur une durée définie pour analyse historique

### 9.4 Environnements

**Résumé** : Environnements dev/staging/production isolés et reproductibles.

**Exigences fonctionnelles**

- Chaque environnement dispose de sa propre configuration et ses propres données
- Les migrations de base de données sont gérées de façon versionnée et reproductible

**Critères d'acceptation**

- Changement en dev sans impact sur staging/production
- Migration applicable et annulable (rollback) sans perte de données sur un environnement de test

---

## 10. Gestion de projet & qualité

### 10.1 Pilotage projet

**Résumé** : Cadrage et suivi du projet dans le temps (planning, budget, rituels).

**Exigences fonctionnelles**

- Le planning découpe le projet en sprints/releases avec des jalons clairs
- Un suivi budgétaire est mis à jour régulièrement
- Des rituels de suivi sont en place

**Critères d'acceptation**

- Planning partagé et accessible à toute l'équipe
- Écarts entre planning prévisionnel et réel identifiés et documentés à chaque revue

### 10.2 Tests

**Résumé** : Stratégie de tests garantissant la qualité et la stabilité de l'app.

**Exigences fonctionnelles**

- Des tests unitaires couvrent les fonctions critiques (mobile et backend)
- Des tests d'intégration valident les échanges API/base de données
- Une beta fermée permet de collecter des retours utilisateurs réels

**Critères d'acceptation**

- Couverture de tests unitaires atteignant un seuil cible défini (ex : >70% sur le code critique)
- Aucune régression critique détectée en beta laissée sans correction avant lancement
- Tests d'intégration passant systématiquement avant tout déploiement en production

### 10.3 Déploiement & lancement

**Résumé** : Publication de l'app sur les stores et exécution du plan de lancement.

**Exigences fonctionnelles**

- Les fiches App Store/Google Play sont complètes (visuels, description, mots-clés)
- La documentation utilisateur est disponible
- Un plan de communication de lancement est exécuté

**Critères d'acceptation**

- App validée par les processus de review Apple/Google sans rejet bloquant
- Documentation utilisateur couvrant 100% des fonctionnalités principales du MVP
- Lancement accompagné d'au moins un canal de communication actif

### 10.4 Documentation projet

**Résumé** : Documentation interne du projet (cadrage, organisation, process, qualité, technique), tenue à jour tout au long du projet.

**Exigences fonctionnelles**

- Chaque document a un rôle clair et un owner identifié
- Un guide indique l'ordre de lecture et le contenu de chaque document
- La documentation reflète l'organisation et le fonctionnement réels de l'équipe

**Critères d'acceptation**

- OBS et process de développement alignés sur l'organisation réelle à chaque Sprint Review
- Toute PR modifiant une interface ou un comportement inclut la mise à jour du document concerné
- Dépôt miroir synchronisé avec le dépôt principal (aucun écart détecté par la vérification)

---

## 11. Fonctionnalités bonus (hors MVP)

> Regroupement établi à partir de l'Epic 11 du [BACKLOG.md](./BACKLOG.md). Hors scope MVP v1, à arbitrer par le coordinateur avant intégration formelle au planning et à la PBS.

### 11.1 Point d'intérêt communautaire

**Résumé** : Points d'intérêt créés par forte activité communautaire ou manuellement, rapportant un bonus de points aux randonneurs qui y prennent une photo.

**Exigences fonctionnelles**

- Un point d'intérêt est créé automatiquement lorsqu'un seuil d'activité photo est dépassé dans un rayon donné
- Un utilisateur ou modérateur peut créer manuellement un point d'intérêt, soumis à validation
- Une photo prise à proximité d'un point actif rapporte un bonus de points, cumulable selon une règle définie
- Les points d'intérêt sont visuellement distincts des pins de photo classiques sur la carte

**Critères d'acceptation**

- Un point est créé automatiquement lorsqu'un seuil d'activité photo (nombre de contributions dans un rayon donné) est dépassé — seuil à définir avec l'équipe produit
- Un utilisateur (ou modérateur) peut créer manuellement un point d'intérêt, soumis à validation
- Une photo prise à proximité d'un point d'intérêt actif (rayon à définir) rapporte un bonus de points, cumulable une seule fois par utilisateur et par point (règle à définir)
- Les points d'intérêt sont visibles sur la carte, visuellement distincts des pins de photo classiques

### 11.2 Avatar personnalisable + compagnon animal

**Résumé** : Avatar personnalisable affiché en temps réel sur la carte, et compagnon animal déblocable en progressant, désactivable dans les options.

**Exigences fonctionnelles**

- L'avatar utilisateur est personnalisable et visible en temps réel sur l'écran carte pendant la randonnée
- Le compagnon animal se débloque selon des conditions de progression (herbier/bestiaire et/ou missions)
- Le compagnon suit visuellement la position de l'utilisateur sur la carte pendant la session GPS
- L'affichage du compagnon peut être désactivé dans les Paramètres, de façon persistante

**Critères d'acceptation**

- L'avatar utilisateur est personnalisable (a minima : apparence de base) et visible en temps réel sur l'écran carte pendant la randonnée
- Le compagnon animal se débloque selon des conditions définies (progression herbier/bestiaire et/ou missions), de façon cohérente sur 100% des cas testés
- Le compagnon suit visuellement la position de l'utilisateur sur la carte pendant la session GPS
- Un réglage dans les Paramètres permet d'activer/désactiver l'affichage du compagnon, persistant entre les sessions

### 11.3 Portage SmartWatch

**Résumé** : Consultation des métriques GPS live et pilotage de session directement depuis une montre connectée (wearOS/watchOS).

**Exigences fonctionnelles**

- Les métriques live (distance, vitesse, altitude, durée) sont synchronisées entre le téléphone et la montre
- Le pilotage de session (pause/reprise/arrêt) est possible depuis la montre
- La faisabilité technique (wearOS/watchOS) est validée par un spike avant tout développement

**Critères d'acceptation**

- Spike technique réalisé (faisabilité wearOS/watchOS, choix d'architecture) avant tout développement
- Les métriques live (distance, vitesse, altitude, durée) sont synchronisées entre le téléphone et la montre avec un délai sous un seuil à définir
- Le pilotage de session (pause/reprise/arrêt) est possible depuis la montre
- Autonomie de la montre mesurée sur une randonnée de test longue durée, sous un seuil acceptable à définir

---

### Note sur les seuils chiffrés

Plusieurs critères d'acceptation contiennent des seuils indicatifs (%, secondes, délais) marqués "à définir avec l'équipe" quand ils dépendent de choix produit/business. À valider avant de les figer comme critères contractuels.
