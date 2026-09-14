# Architecture Technique — Traily

## 1. Vue d'ensemble

Traily est une application de référencement de chemins de randonnées. Elle permet aux utilisateurs d'enregistrer, consulter et partager des itinéraires, avec accès GPS en temps réel, prise de photos et support hors connexion.

### Composants du système

```
┌─────────────────────────────────────────────────────────┐
│                    Application Mobile                    │
│                  Expo / React Native                     │
│              (iOS & Android, TypeScript)                 │
└──────────────────────┬──────────────────────────────────┘
                       │ HTTPS / REST API
                       │
┌──────────────────────▼──────────────────────────────────┐
│                    Backend principal                     │
│                      API REST Go                         │
│              (cœur métier, auth, données)               │
└──────┬──────────────────────────────┬───────────────────┘
       │ HTTP interne                 │
       │                              │
┌──────▼───────────────────┐  ┌───────▼──────────┐
│    Micro-services IA     │  │  Base de données  │
│                          │  │  PostgreSQL        │
│  trail-analyzer  (Go)    │  │  + PostGIS         │
│  gps-processor   (Go)    │  └───────────────────┘
│  recommender  (Python)   │
│                          │
│  [modèles locaux]        │
│  sentence-transformers   │
│  FAISS                   │
└──────────────────────────┘
```

---

## 2. Stack technique

| Couche | Technologie | Justification |
|---|---|---|
| Application mobile | Expo (React Native) + TypeScript | Support natif GPS/caméra, offline, écosystème mature |
| Backend API | Go | Performances, simplicité, typage fort, excellent pour les APIs REST |
| Micro-services IA (algorithmes) | Go | Calculs déterministes purs, performances, cohérence de stack |
| Micro-services IA (modèles locaux) | Python + FastAPI | Seul écosystème viable pour PyTorch, HuggingFace, FAISS |
| Base de données | PostgreSQL | Données relationnelles, support PostGIS pour les coordonnées géographiques |
| Stockage fichiers | S3-compatible (ex: MinIO en self-hosted) | Photos des randonnées, tuiles de cartes offline |
| Auth | JWT (access + refresh tokens) | Stateless, compatible mobile |
| Communication inter-services | HTTP/REST ou gRPC | Simple, debuggable |

---

## 3. Application mobile (Expo / React Native)

### Responsabilités

- Interface utilisateur (consultation, création, enregistrement de randonnées)
- Accès aux fonctionnalités natives : GPS, caméra, stockage local
- Mode hors connexion : persistance locale + synchronisation différée
- Consommation de l'API REST du backend

### Fonctionnalités natives clés

| Fonctionnalité | Module Expo | Usage |
|---|---|---|
| GPS foreground | `expo-location` | Position courante, carte en temps réel |
| GPS background | `expo-location` + task manager | Tracking actif pendant une rando |
| Caméra | `expo-camera` | Prise de photo sur le terrain |
| Galerie | `expo-image-picker` | Sélection de photos existantes |
| Stockage local | `expo-sqlite` | Données offline (trails, waypoints) |
| Tokens sécurisés | `expo-secure-store` | Stockage JWT |
| Réseau | `expo-network` | Détection online/offline |

### Mode hors connexion

Le mode offline est critique pour une app de randonnée (zones sans réseau).

```
Utilisateur hors ligne
       │
       ▼
  SQLite local       ◄──── Sync au retour en ligne
  (trails, points,          (file d'actions en attente)
   photos en attente)
       │
       ▼
  UI identique,
  bannière "offline"
```

- Les trails consultés récemment sont mis en cache automatiquement
- Les actions offline (ajout de waypoint, photo) sont enfilées dans une queue persistante
- La synchronisation est automatique à la reconnexion, avec gestion des conflits

### Structure des dossiers

```
app/                    # Routes (Expo Router, file-based)
src/
  components/           # Composants UI réutilisables
  hooks/                # Hooks custom (logique réutilisable)
  services/             # Logique métier, appels API, natif
  store/                # État global (Zustand)
  types/                # Types TypeScript
  constants/            # Thème, config, messages d'erreur
  utils/                # Fonctions pures utilitaires
  lib/                  # Clients configurés (API, storage)
```

---

## 4. Backend API (Go)

### Responsabilités

- Exposition de l'API REST consommée par l'application mobile
- Logique métier principale (trails, utilisateurs, sessions)
- Authentification et autorisation
- Orchestration des appels vers les micro-services IA
- Accès et gestion de la base de données

### Structure des packages

```
cmd/
  api/                  # Point d'entrée, initialisation serveur
internal/
  api/                  # Handlers HTTP, routing, middlewares
  auth/                 # JWT, sessions, permissions
  user/                 # Domaine utilisateur
  trail/                # Domaine trail (cœur métier)
  ai/                   # Client(s) micro-services IA
  config/               # Configuration (env vars)
  store/                # Repositories (accès DB)
  pkg/
    validator/          # Validation commune
    errors/             # Types d'erreurs métier
    logger/             # Logging structuré
```

### Flux d'une requête HTTP

```
Requête mobile
     │
     ▼
Middleware (auth, logging, rate limit, recovery)
     │
     ▼
Handler (api/)          ← parse et valide la requête
     │
     ▼
Service (trail/, user/) ← logique métier
     │
     ├──► Repository (store/) ◄──► PostgreSQL
     │
     └──► Client IA (ai/)    ◄──► Micro-service IA
     │
     ▼
Réponse JSON structurée
```

### API REST — conventions

- Versioning via path : `/api/v1/`
- Format de réponse uniforme :
```json
{ "data": { ... } }         // succès
{ "error": { "code": "...", "message": "..." } }  // erreur
```
- Authentification : `Authorization: Bearer <jwt>`
- Dates : ISO 8601 UTC
- IDs : UUID v4

### Endpoints principaux (exemples)

```
POST   /api/v1/auth/register
POST   /api/v1/auth/login
POST   /api/v1/auth/refresh

GET    /api/v1/trails
POST   /api/v1/trails
GET    /api/v1/trails/:id
PUT    /api/v1/trails/:id
DELETE /api/v1/trails/:id

POST   /api/v1/trails/:id/photos
GET    /api/v1/trails/:id/waypoints
POST   /api/v1/trails/:id/waypoints

GET    /api/v1/users/me
PUT    /api/v1/users/me
```

---

## 5. Micro-services IA

Les micro-services IA utilisent **deux langages selon la nature de leur traitement**. Le contrat HTTP est identique dans les deux cas — le backend Go ne voit pas la différence.

| Service | Langage | Raison |
|---|---|---|
| `trail-analyzer` | Go | Algorithmes déterministes purs (scoring, durée, dénivelé) |
| `gps-processor` | Go | Douglas-Peucker, lissage Kalman, calculs géographiques |
| `recommender` | Python + FastAPI | Embeddings locaux (sentence-transformers), recherche vectorielle (FAISS) |

### Règle de décision

- Calcul algorithmique sans ML → **Go**
- Inférence de modèle local (PyTorch, HuggingFace, embeddings) → **Python + FastAPI**
- Appel à une API IA externe (OpenAI, etc.) → **Go** (client HTTP)

### Communication avec le backend

- Appelés par le backend via HTTP interne — jamais directement par le mobile
- Interface uniforme : `POST /analyze` → réponse JSON, quel que soit le langage
- Timeout configuré sur chaque appel depuis le backend
- Le backend fonctionne en mode dégradé si un micro-service est indisponible — les données IA sont toujours optionnelles

---

## 6. Base de données (PostgreSQL)

### Choix structurants

- **PostGIS** : extension PostgreSQL pour le stockage et les requêtes géographiques (proximité, intersections de trails, bounding box)
- Les coordonnées GPS sont stockées en type `GEOMETRY(Point, 4326)` — pas en floats séparés
- Les traces de randonnée (séquence de points) sont stockées en `GEOMETRY(LineString, 4326)`

### Modèles principaux

```
users
  id, email, password_hash, created_at, updated_at

trails
  id, user_id, name, description, difficulty,
  distance_meters, elevation_gain_meters,
  path (GEOMETRY LineString), created_at, updated_at

waypoints
  id, trail_id, label, photo_url,
  location (GEOMETRY Point), order_index, created_at

trail_sessions          # Enregistrements de randonnées effectuées
  id, user_id, trail_id, started_at, ended_at,
  recorded_path (GEOMETRY LineString)

photos
  id, trail_id, waypoint_id, storage_key, taken_at,
  location (GEOMETRY Point)
```

---

## 7. Authentification

### Flux

```
1. Login (email + password)
        │
        ▼
   Backend valide les credentials
        │
        ▼
   Retourne access_token (15min) + refresh_token (30j)
        │
        ▼
   Mobile stocke dans expo-secure-store
        │
        ▼
   Chaque requête API : Authorization: Bearer <access_token>
        │
        ▼
   À expiration : POST /auth/refresh avec refresh_token
```

- Les refresh tokens sont stockés en base (révocables)
- Un refresh token compromis peut être invalidé sans impacter les autres sessions
- Pas de session côté serveur — architecture stateless

---

## 8. Stockage des fichiers

- Les photos sont uploadées directement depuis le mobile vers le stockage objet (S3/MinIO) via une URL présignée fournie par le backend
- Le backend ne sert pas de proxy pour les fichiers — il fournit uniquement l'URL présignée
- Les métadonnées de photo (URL, coordonnées GPS) sont stockées en base

```
Mobile ──► POST /api/v1/trails/:id/photos/upload-url ──► Backend
Mobile ◄── { presigned_url, photo_id } ◄────────────────── Backend
Mobile ──► PUT <presigned_url> (binaire photo) ──────────► S3/MinIO
Mobile ──► PATCH /api/v1/photos/:id/confirm ────────────► Backend
```

---

## 9. Infrastructure et déploiement

### Environnements

| Environnement | Usage |
|---|---|
| `development` | Local, `.env` local, DB locale |
| `staging` | Tests d'intégration, builds preview EAS |
| `production` | Utilisateurs finaux |

### Conteneurisation

- Chaque service backend est conteneurisé (Docker)
- `docker-compose.yml` pour le développement local (API, micro-services, PostgreSQL, MinIO)
- Les images de production sont buildées en multi-stage pour minimiser la taille

### Variables d'environnement critiques

```bash
# Backend
DATABASE_URL=
JWT_SECRET=
JWT_REFRESH_SECRET=
AI_SERVICE_URL=
S3_BUCKET=
S3_ENDPOINT=

# Mobile (app.config.ts)
EXPO_PUBLIC_API_URL=
```

---

## 10. Flux métier principaux

### Enregistrement d'une randonnée

```
1. Utilisateur démarre une session → activation GPS background
2. Waypoints enregistrés périodiquement en SQLite local
3. Photos prises et stockées localement (queue upload)
4. À la fin : sync vers le backend
   - POST /trail-sessions avec le LineString complet
   - Upload des photos en parallèle (URLs présignées)
5. Si hors connexion pendant la rando → sync différée à la reconnexion
```

### Consultation d'un trail

```
1. GET /api/v1/trails?near=<lat,lng>&radius=<km>
   (requête PostGIS ST_DWithin)
2. Affichage sur carte (MapLibre)
3. Mise en cache SQLite local pour accès offline
```

---

## 11. Décisions d'architecture (ADR résumés)

| Décision | Choix | Raison |
|---|---|---|
| Framework mobile | Expo (React Native) | GPS background natif, offline, écosystème mature vs Tauri mobile trop immature |
| Langage backend | Go | Performances, simplicité, excellent pour REST |
| Langage micro-services IA (algo) | Go | Cohérence, performances, pas de dépendance ML |
| Langage micro-services IA (ML) | Python + FastAPI | Seul écosystème viable pour modèles locaux |
| DB géographique | PostgreSQL + PostGIS | Requêtes géographiques natives, pas de DB spécialisée supplémentaire |
| Auth | JWT stateless | Compatible mobile, scalable, pas de session serveur |
| Upload photos | Présignées S3 | Le backend ne sert pas de proxy, meilleure performance |
| Offline | SQLite local + sync queue | Critique pour les zones sans réseau pendant les randonnées |
| État global mobile | Zustand | Simple, léger, pas de boilerplate Redux |
