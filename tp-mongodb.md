# Rapport Technique : Utilisation de MongoDB

## Introduction

MongoDB est une base de données **NoSQL orientée documents**, idéale pour stocker et manipuler des données non structurées sous format JSON. Elle est souvent utilisée pour des applications nécessitant une flexibilité dans la gestion des données.

Ce rapport couvre l'installation de MongoDB, l'importation de données et les principales requêtes permettant d'exploiter une base de données contenant une collection de films.

---

## 1. Installation et Configuration

### 1.1 Installation avec Docker
```bash
docker run --name mongodb -d -p 27017:27017 -v $(pwd)/data:/data/db mongo:latest
```
- `--name mongodb` : Nom du conteneur.
- `-d` : Démarrage en arrière-plan.
- `-p 27017:27017` : Exposition du port 27017.
- `-v $(pwd)/data:/data/db` : Persistance des données.

### 1.2 Vérification du fonctionnement
```bash
docker ps
```
La présence d’un conteneur MongoDB actif confirme le bon fonctionnement.

---

## 2. Importation des Données

### 2.1 Transfert du fichier dans le conteneur
```bash
docker cp films.json mongodb:/films.json
```

### 2.2 Connexion au conteneur MongoDB
```bash
docker exec -it mongodb bash
```

### 2.3 Importation des données dans MongoDB
```bash
mongoimport --db lesfilms --collection films --file /films.json --jsonArray
```
---

## 3. Requêtes MongoDB

### 3.1 Vérifier l’importation
```javascript
db.films.count()
```
Affiche le nombre total de films.

### 3.2 Examiner la structure d’un document
```javascript
db.films.findOne()
```
Affiche un document de la collection pour comprendre sa structure.

### 3.3 Requêtes de base

#### a. Afficher les films d’action
```javascript
db.films.find({ genre: "Action" })
```

#### b. Compter les films d’action
```javascript
db.films.count({ genre: "Action" })
```

#### c. Afficher les films d’action produits en France
```javascript
db.films.find({ genre: "Action", country: "FR" })
```

#### d. Afficher les films d’action produits en France en 1963
```javascript
db.films.find({ genre: "Action", country: "FR", year: 1963 })
```

#### e. Afficher les films sans certains attributs (ex : sans les grades)
```javascript
db.films.find({ genre: "Action", country: "FR" }, { grades: 0 })
```

#### f. Exclure l’identifiant `_id`
```javascript
db.films.find({ genre: "Action", country: "FR" }, { _id: 0 })
```

### 3.4 Requêtes avancées

#### a. Afficher les titres et grades des films d’action en France
```javascript
db.films.find({ genre: "Action", country: "FR" }, { _id: 0, title: 1, grades: 1 })
```

#### b. Afficher les films avec une note supérieure à 10
```javascript
db.films.find({ genre: "Action", country: "FR", "grades.note": { $gt: 10 } }, { _id: 0, title: 1, grades: 1 })
```

#### c. Afficher uniquement les films où **toutes** les notes sont supérieures à 10
```javascript
db.films.find({ genre: "Action", country: "FR", grades: { $not: { $elemMatch: { note: { $lte: 10 } } } } }, { _id: 0, title: 1, grades: 1 })
```

### 3.5 Lister les catégories de la base

#### a. Afficher les genres distincts
```javascript
db.films.distinct("genre")
```

#### b. Afficher les grades distincts
```javascript
db.films.distinct("grades.grade")
```

### 3.6 Requêtes sur les acteurs et années

#### a. Afficher les films contenant certains acteurs
```javascript
db.films.find({ actors: { $in: ["artist:4", "artist:18", "artist:11"] } })
```

#### b. Afficher les films sans résumé
```javascript
db.films.find({ summary: { $exists: false } })
```

#### c. Afficher les films avec Leonardo DiCaprio en 1997
```javascript
db.films.find({ "actors.first_name": "Leonardo", "actors.last_name": "DiCaprio", year: 1997 })
```

#### d. Afficher les films avec Leonardo DiCaprio **ou** en 1997
```javascript
db.films.find({ $or: [{ "actors.first_name": "Leonardo", "actors.last_name": "DiCaprio"}, { year: 1997 }] })
```

---

## 4. Arrêt et Suppression de MongoDB

### 4.1 Arrêter MongoDB
```bash
docker stop mongodb
```

### 4.2 Supprimer le conteneur
```bash
docker rm mongodb
```

---

## Conclusion
MongoDB est un outil puissant pour stocker et interroger des données non structurées. Grâce à ses fonctionnalités avancées et sa flexibilité, il est particulièrement adapté aux applications modernes nécessitant des bases de données évolutives et performantes.
