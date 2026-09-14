# Quality Plan — Backend Go API

## Vue d'ensemble

API REST en Go. Elle expose les endpoints consommés par l'application mobile et s'interface avec la base de données PostgreSQL/PostGIS ainsi qu'avec les services IA (identification d'espèces, recommandation de traces).

---

## Conventions

- **[Effective Go](https://go.dev/doc/effective_go)** et **[Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)**
- **[Conventional Commits](https://www.conventionalcommits.org/)** pour les messages de commit
- Formatage automatique via `gofmt` / `goimports` — aucun code non formaté ne doit être mergé
- Commentaires godoc obligatoires sur les fonctions et types exportés

---

## Structure des packages

```
internal/
  auth/           # Authentification et autorisation
  user/           # Domaine utilisateur
  trail/          # Domaine trail
  species/        # Domaine espèces
  score/          # Gamification et leaderboards
  team/           # Équipes et clans
  moderation/     # Modération des contributions
  api/            # Handlers HTTP, routing, middlewares
  config/         # Configuration de l'application
  store/          # Accès base de données (repositories)
  pkg/
    validator/    # Validation commune
    errors/       # Types d'erreurs métier
    logger/       # Wrapper de logging
cmd/
  api/            # Point d'entrée principal
```

- `api/` : uniquement du code HTTP, pas de logique métier
- Les structs de requête/réponse HTTP sont séparées des structs de domaine
- Pas de dépendances circulaires entre packages

---

## API REST

- Routes en `kebab-case`, substantifs au pluriel : `/users`, `/trail-sessions`
- Versioning obligatoire : `/api/v1/...`
- Verbes HTTP sémantiques : `GET`, `POST`, `PUT`/`PATCH`, `DELETE`
- Codes HTTP cohérents (`200`, `201`, `204`, `400`, `401`, `403`, `404`, `409`, `500`)

```json
// Succès
{ "data": { ... } }

// Erreur
{ "error": { "code": "VALIDATION_ERROR", "message": "email is required" } }
```

- Dates en ISO 8601 UTC : `"2025-01-15T10:30:00Z"`
- IDs en UUID v4 (string)

---

## Tests

- **Cible : 60–70% de couverture globale**
- Tests unitaires : logique métier, validations, transformations
- Tests d'intégration : handlers HTTP via `httptest`, base de données de test réelle (pas de mocks DB)
- Mocks acceptés pour les services externes (services IA, S3)

Nommage : `TestFunctionName_Scenario_ExpectedBehavior`

```bash
go test ./...
go test -cover ./...
```

---

## Sécurité

- Tokens JWT jamais loggés
- Mots de passe hashés avec bcrypt (coût minimum 12)
- `.env` dans `.gitignore`, un `.env.example` documenté est maintenu
- Requêtes SQL avec prepared statements — pas d'interpolation de strings
- Rate limiting sur les endpoints d'authentification

---

## CI — checks bloquants sur chaque PR

| Check | Outil |
|---|---|
| Formatage | `gofmt` / `goimports` |
| Lint | `golangci-lint` |
| Tests | `go test ./...` |
| Build | `go build ./...` |
| Vulnérabilités | `govulncheck` |
| Couverture < 60% | `go test -cover` (warning) |

---

## Workflow de développement

- Branches : `feat/`, `fix/`, `chore/`
- Pas de merge sans PR revue par au moins un autre développeur
- Une PR = une fonctionnalité ou un fix
- Jamais de commit direct sur `main`
