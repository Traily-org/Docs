# Hardware Requirements — Traily

## Vue d'ensemble

Ce document recense les exigences matérielles pour chaque composant du système Traily : appareils mobiles des utilisateurs, serveurs de déploiement, et postes de développement.

**Conclusion principale : aucun matériel spécialisé n'est requis.** Les micro-services IA (sentence-transformers, FAISS) tournent sur CPU standard — pas de GPU nécessaire pour les volumes de données attendus.

---

## 1. Appareils mobiles (utilisateurs finaux)

L'application cible iOS et Android via Expo (React Native).

### Exigences matérielles obligatoires

| Matériel | Usage | Obligatoire |
|---|---|---|
| **GPS** | Tracking de randonnée en temps réel (foreground + background) | Oui |
| **Caméra** | Prise de photos sur le terrain | Non — fonctionnalité dégradée sans |
| **Stockage local** | Cache SQLite (trails, waypoints, photos en attente de sync) | Oui — minimum 500 MB libres recommandés |
| **Connectivité réseau** | Sync avec l'API REST | Non — mode offline supporté |

### Versions minimales des OS

| Plateforme | Version minimale | Raison |
|---|---|---|
| iOS | 16.0 | Support `expo-location` background tasks, `expo-sqlite` |
| Android | 8.0 (API 26) | Support background location updates |

### Ce qui n'est pas requis

- Aucune puce spéciale (NFC, baromètre, boussole) — l'altitude est fournie par le GPS ou saisie manuellement.
- Aucun capteur biométrique.

---

## 2. Serveurs de déploiement

### Backend principal (API Go)

Go est extrêmement léger. Aucune contrainte matérielle particulière.

| Ressource | Minimum | Recommandé |
|---|---|---|
| CPU | 1 vCPU | 2 vCPU |
| RAM | 256 MB | 512 MB |
| Stockage | — | — |
| Réseau | Accès HTTPS entrant | — |

### Base de données (PostgreSQL + PostGIS)

PostGIS ajoute des opérations géographiques (ST_DWithin, ST_Intersects) qui sont CPU-intensives sur de grandes tables. Dimensionner en fonction du volume de trails.

| Ressource | Minimum | Recommandé |
|---|---|---|
| CPU | 2 vCPU | 4 vCPU |
| RAM | 1 GB | 4 GB (shared_buffers + work_mem) |
| Stockage | SSD recommandé — I/O critiques pour les requêtes géographiques | 20 GB+ selon volume |

### Stockage objet (S3 / MinIO)

Photos des randonnées. Aucune contrainte de compute — dimensionner uniquement en espace disque.

| Ressource | Dimensionnement |
|---|---|
| Stockage | Variable selon volume d'utilisation — prévoir ~2 MB par photo |
| Réseau | Bande passante upload depuis les appareils mobiles |

### Micro-services IA Go (`trail-analyzer`, `gps-processor`)

Calculs algorithmiques purs (scoring, Douglas-Peucker, Kalman). Très légers.

| Ressource | Minimum | Recommandé |
|---|---|---|
| CPU | 1 vCPU | 1 vCPU |
| RAM | 128 MB | 256 MB |

### Micro-service IA Python (`recommender`)

C'est le seul composant avec des exigences mémoire notables : le modèle sentence-transformers et l'index FAISS sont chargés **une seule fois au démarrage** et maintenus en RAM.

| Ressource | Minimum | Recommandé | Raison |
|---|---|---|---|
| CPU | 1 vCPU | 2 vCPU | Inférence sentence-transformers sur CPU |
| RAM | **1 GB** | **2 GB** | Modèle (~80–400 MB selon le modèle choisi) + index FAISS + overhead Python |
| GPU | **Non requis** | — | FAISS CPU suffit pour les volumes de trails attendus ; sentence-transformers tourne sur CPU |
| Stockage | 1 GB | 2 GB | Modèle local + index FAISS persisté |

> **Note GPU** : un GPU deviendrait utile uniquement si le volume de requêtes de recommandation dépasse plusieurs centaines par seconde en simultané, ou si un modèle d'embedding beaucoup plus lourd est adopté. Ce n'est pas le cas pour Traily.

---

## 3. Poste de développement

Aucune exigence spécifique. Une machine de développement standard suffit.

| Outil | Exigence |
|---|---|
| Docker | Pour lancer l'ensemble de la stack en local (API, micro-services, PostgreSQL, MinIO) |
| RAM | 8 GB minimum pour faire tourner tous les conteneurs en parallèle |
| Expo Go ou simulateur iOS/Android | Pour tester l'application mobile |
| Accès GPS simulé | Fourni par les simulateurs Xcode / Android Studio |

---

## 4. Ce qui n'est pas requis

Pour éviter toute ambiguïté :

- **Pas de GPU** — ni en développement, ni en production pour les volumes actuels.
- **Pas de matériel réseau spécial** — l'architecture est standard HTTP/HTTPS.
- **Pas de hardware IoT ou embarqué** — l'application s'appuie exclusivement sur le GPS natif de l'appareil mobile.
- **Pas de cluster Kubernetes requis** — un `docker-compose` suffit en staging, et des VMs simples en production initiale.
