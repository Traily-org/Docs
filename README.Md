# Traily

Traily est une application d'exploration territoriale combinant une carte communautaire et une caméra IA géolocalisée pour aider l'utilisateur à observer et interpréter ce qu'il voit.

## Stack

- _Mobile_ : Expo (React Native), TypeScript
- _Backend_ : Go (API REST)
- _Services IA_ : Go (trail-analyzer, gps-processor), Python + FastAPI (recommender)
- _Base de données_ : PostgreSQL + PostGIS
- _Stockage_ : S3-compatible (MinIO)
- _Auth_ : JWT (access + refresh tokens)
- _Infrastructure_ : Docker

## Installation locale

### Prérequis

- Node.js 18+
- Go 1.24+
- Task (`go install github.com/go-task/task/v3/cmd/task@latest`)

### Variables d'environnement

Copier `.env.example` en `.env` dans `src/app/` et `src/backend/`, puis renseigner les valeurs.

### Démarrage

#### Mobile

```sh
cd src/app && \
npm install && \
npx expo start
```

#### Backend

```sh
cd src/backend && \
task dev
```

#### Docker _(TO DO)_

```sh
docker compose up --build
```

## Structure du projet

```sh
traily/
├── src/
│   ├── app/                    # Application Expo / React Native
│   │   ├── app/                # Routes Expo Router
│   │   │   ├── (auth)/         # Connexion, inscription
│   │   │   └── (tabs)/         # Onglets principaux (accueil, carte, profil)
│   │   └── src/
│   │       ├── components/     # Composants UI réutilisables
│   │       ├── constants/      # Thème, config, messages d'erreur
│   │       ├── hooks/          # Hooks custom
│   │       ├── lib/            # Clients configurés (API)
│   │       ├── services/       # Logique métier, appels API
│   │       ├── store/          # État global (Zustand)
│   │       ├── types/          # Types TypeScript
│   │       └── utils/          # Fonctions utilitaires pures
│   └── backend/                # API REST Go
│       └── cmd/                # Point d'entrée
└── README.md
```

## Conventions

### Branches

```sh
main                : Production — merge par PR uniquement
feat/TRA-XX-nom     : Nouvelle fonctionnalité
fix/TRA-XX-nom      : Correction de bug
hotfix/TRA-XX-nom   : Correction urgente en production
chore/TRA-XX-nom    : Config, setup, refactor
docs/TRA-XX-nom     : Documentation
test/TRA-XX-nom     : Tests
```

### Commits

```sh
type(scope): description courte
```

| Type    | Usage                         |
| ------- | ----------------------------- |
| `feat`  | Nouvelle fonctionnalité       |
| `fix`   | Correction de bug             |
| `chore` | Config, setup, refactor       |
| `docs`  | Documentation                 |
| `test`  | Ajout ou mise à jour de tests |

### Tickets

Toutes les tâches sont suivies dans [Linear](https://linear.app/traily/team/TRA/active). Chaque branche correspond à un ticket Linear.
