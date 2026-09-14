# Quality Plan — Frontend Expo / React Native

## Vue d'ensemble

Application mobile construite avec **Expo** (React Native) en **TypeScript**. Elle consomme l'API REST Go, accède aux fonctionnalités natives (GPS, caméra) et supporte un mode hors connexion pour les fonctions critiques (enregistrement et consultation de randonnées).

---

## Conventions

- **[TypeScript strict](https://www.typescriptlang.org/tsconfig#strict)** — `strict: true` dans `tsconfig.json`
- **[Expo ESLint config](https://docs.expo.dev/guides/using-eslint/)** + `@typescript-eslint/recommended`
- **[Prettier](https://prettier.io/)** pour le formatage
- **[Conventional Commits](https://www.conventionalcommits.org/)** pour les messages de commit
- Pas de `any` — utiliser `unknown` et des type guards si le type est inconnu
- `interface` pour les contrats publics (props, services), `type` pour les unions et aliases

---

## Structure des dossiers

```
app/                    # Expo Router — routes (file-based routing)
  (auth)/               # Routes authentifiées
  (public)/             # Routes publiques (login, register)
  _layout.tsx

src/
  components/           # Composants UI réutilisables (présentationnels)
    common/
    trail/
    map/
    species/
    gamification/

  hooks/                # Hooks custom (logique réutilisable)

  services/             # Logique métier et appels API
    trailService.ts
    authService.ts
    locationService.ts
    photoService.ts
    offlineService.ts
    speciesService.ts

  store/                # État global (Zustand)

  types/                # Types et interfaces TypeScript
    trail.ts
    user.ts
    species.ts
    api.ts              # Types des réponses API

  constants/
    theme.ts            # Couleurs, typographies, espacements
    config.ts           # URLs, timeouts
    errors.ts           # Messages d'erreur centralisés

  utils/                # Fonctions utilitaires pures

  lib/
    api.ts              # Client HTTP configuré
    storage.ts          # Wrapper AsyncStorage / SQLite
```

### Séparation des responsabilités

- **Composants** : affichage uniquement — pas d'appels API directs
- **Hooks** : orchestration état local + appels services — pas de JSX
- **Services** : logique métier, appels API, transformations — pas de state React
- **Store** : état global partagé entre écrans — pas de logique métier

---

## Fonctions natives

- **GPS** : toute la logique dans `locationService.ts`. Permission explicite demandée avant accès. Tracking background via `expo-location`.
- **Caméra** : toute la logique dans `photoService.ts`. Photos compressées avant upload (qualité max 0.7, résolution max 1920px).
- **Offline** : détection réseau via `expo-network`, persistance des données critiques en SQLite via `expo-sqlite`, synchronisation à la reconnexion.

---

## Tests

- **Cible : 60–70% de couverture globale**
- Les fonctions de mapping DTO↔domaine et les utils visent 100% (fonctions pures)
- Les appels API sont mockés dans les tests — pas de dépendance à un serveur
- Les modules natifs (GPS, caméra) sont mockés via `jest.mock`
- Pas de tests de snapshot

Nommage : `describe` par unité testée, `it` en langage naturel

```bash
npx jest
npx jest --coverage
```

---

## Sécurité

- Tokens JWT stockés dans `expo-secure-store` — jamais dans AsyncStorage
- URLs d'API dans les variables d'environnement (`app.config.ts` + `.env`)
- Aucune donnée sensible (token, coordonnées précises) loggée

---

## CI — checks bloquants sur chaque PR

| Check | Outil |
|---|---|
| Typage | `tsc --noEmit` |
| Lint | ESLint (config Expo) |
| Formatage | Prettier |
| Tests | `jest --ci` |
| Couverture < 60% | `jest --coverage` (warning) |
| Build EAS | `eas build --profile preview` (sur `main` uniquement) |

---

## Workflow de développement

- Branches : `feat/`, `fix/`, `chore/`
- Pas de merge sans PR revue
- Les PR incluent une capture d'écran ou une courte vidéo pour les changements UI
- Jamais de commit direct sur `main`
