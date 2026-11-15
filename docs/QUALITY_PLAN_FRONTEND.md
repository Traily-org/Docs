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

Le code est rangé **par feature d'abord** : un dossier par zone du schéma de navigation (carte, rando, capture, espèces…). Décision : TRA-120.

```
app/                    # Routes Expo Router (file-based), fines : elles assemblent les features
  (app)/                # App connectée : carte (index), paramètres, profil, rando…
  (public)/             # Routes publiques (login, register)
  _layout.tsx           # Racine : thème + navigation

src/
  features/<feature>/   # Tout le code d'une zone de l'app (map, trails, hike-session, settings…)
    index.ts            # API publique de la feature
    components/         # Sous-dossiers créés à la demande, jamais vides
    hooks/
    services/
  components/           # UI générique sans métier (lignes de liste, boutons…)
  hooks/                # Hooks partagés (thème…)
  constants/
    theme.ts            # Typographies, espacements
    config.ts           # Réglages (URLs, timeouts)
  store/  lib/  types/  utils/   # Partagé, créés à la demande
```

### Règles de rangement

1. **Les routes `app/` restent fines** : elles importent un écran depuis `@/features/<x>` et le rendent, sans logique métier.
2. **Une feature s'importe uniquement par son `index.ts`** : `@/features/species`, jamais `@/features/species/components/...`. À l'intérieur d'une feature, imports relatifs.
3. **Le partagé n'importe jamais une feature** (`components`, `hooks`, `lib`, `store`, `constants`, `types`, `utils`).
4. **Pas de cycle entre features** : les imports vont dans un seul sens.
5. **Naviguer n'est pas importer** : on ouvre l'écran d'une autre feature avec `router.push(...)`, sans importer cet écran.
6. **Un élément utilisé par 2 features reste dans la feature à laquelle il appartient**, exporté par son `index.ts`. Il ne monte dans le partagé que s'il n'a aucun métier (un bouton, un format de distance).

Les règles 2, 3 et 4 sont vérifiées par ESLint, bloquant en CI.

### Séparation des responsabilités

Valable à l'intérieur de chaque feature :

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
