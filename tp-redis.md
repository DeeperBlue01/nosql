# Rapport Technique : Découverte et Utilisation de Redis

## Introduction

Les bases de données **NoSQL** (Not Only SQL) sont des solutions alternatives aux bases relationnelles, adaptées aux besoins de performance et de scalabilité. Parmi celles-ci, Redis (Remote Dictionary Server) se distingue en tant que base **clé-valeur en mémoire** offrant des performances élevées. Redis est utilisé pour la mise en cache, la gestion de sessions, les classements et la messagerie en temps réel.

Ce rapport explore les concepts de base de Redis, son installation, ses principales commandes et ses fonctionnalités avancées.

---

## 1. Installation et Configuration

### Installation de Redis (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install redis-server
```

### Démarrer le serveur Redis
```bash
redis-server
```

### Connexion au client Redis (CLI)
```bash
redis-cli
```
Par défaut, Redis fonctionne sur `127.0.0.1:6379`.

---

## 2. Manipulations et Structures de Données

Redis prend en charge différentes structures de données adaptées à divers cas d’usage.

### 2.1 Clés et opérations de base

#### Définir une clé
```bash
SET utilisateur "Alice"
```

#### Lire une clé
```bash
GET utilisateur
```

#### Supprimer une clé
```bash
DEL utilisateur
```

#### Vérifier l’existence d’une clé
```bash
EXISTS utilisateur
```

### 2.2 Listes (List)
Les listes permettent de stocker des séquences ordonnées d’éléments.

#### Ajouter des éléments
```bash
LPUSH ma_liste "A"
RPUSH ma_liste "B"
```

#### Lire une liste
```bash
LRANGE ma_liste 0 -1
```

#### Supprimer des éléments
```bash
LPOP ma_liste  # Retire l’élément à gauche
RPOP ma_liste  # Retire l’élément à droite
```

### 2.3 Ensembles (Set)
Les ensembles stockent des éléments uniques, sans ordre.

#### Ajouter des éléments
```bash
SADD mon_set "Alice" "Bob" "Charlie"
```

#### Vérifier la présence d’un élément
```bash
SISMEMBER mon_set "Alice"
```

#### Supprimer un élément
```bash
SREM mon_set "Bob"
```

### 2.4 Ensembles Ordonnés (Sorted Set)
Les **Sorted Sets** permettent d’associer des éléments à des scores.

#### Ajouter des éléments avec score
```bash
ZADD classement 100 "Alice" 200 "Bob"
```

#### Récupérer les éléments triés
```bash
ZRANGE classement 0 -1 WITHSCORES
```

#### Récupérer les éléments dans l’ordre inverse
```bash
ZREVRANGE classement 0 -1 WITHSCORES
```

### 2.5 Hashes
Les **hashes** permettent de stocker des paires clé-valeur organisées.

#### Ajouter des champs
```bash
HSET utilisateur:1 nom "Alice" age 30 email "alice@example.com"
```

#### Lire les champs d’un hash
```bash
HGETALL utilisateur:1
```

#### Incrémenter une valeur numérique
```bash
HINCRBY utilisateur:1 age 1
```

---

## 3. Fonctionnalités Avancées

### 3.1 Publication et Abonnement (Pub/Sub)
Redis permet la communication temps réel via un modèle **Publish/Subscribe**.

#### Souscrire à un canal
```bash
SUBSCRIBE canal1
```

#### Publier un message
```bash
PUBLISH canal1 "Nouveau message"
```

### 3.2 Gestion des Bases Multiples
Redis prend en charge plusieurs bases (jusqu’à 16 par défaut).

#### Changer de base
```bash
SELECT 1
```

#### Lister les clés d’une base
```bash
KEYS *
```

### 3.3 Persistance des Données
Redis offre plusieurs options de persistance :

#### Snapshots (RDB)
Sauvegarde périodique de l’état de la base.
```bash
SAVE  # Sauvegarde immédiate
BGSAVE  # Sauvegarde en arrière-plan
```

#### Append-Only File (AOF)
Journalisation des opérations pour une récupération précise.
```bash
CONFIG SET appendonly yes
```

---

## 4. Considérations de Performance
- Redis fonctionne principalement **en mémoire**, ce qui garantit une latence très faible.
- Les **snapshots** RDB minimisent l’utilisation du CPU mais peuvent entraîner une perte de données récente.
- L’**AOF** offre une meilleure durabilité mais peut ralentir les performances.

---

## Conclusion

Redis est un outil puissant pour des applications nécessitant une gestion rapide des données en temps réel. Grâce à ses structures variées et ses fonctionnalités avancées, il trouve des applications dans la mise en cache, la messagerie et le traitement des sessions. Sa gestion efficace de la persistance et de la réplication en fait une solution robuste, bien que la mémoire soit une contrainte importante à prendre en compte.
