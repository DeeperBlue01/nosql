# Rapport Technique : Introduction à CouchDB et MapReduce

## Introduction

**Apache CouchDB** est une base de données NoSQL orientée documents qui utilise JSON pour stocker les données, HTTP pour l'accès et **MapReduce** pour les requêtes. Grâce à sa flexibilité et sa capacité de réplication distribuée, CouchDB est particulièrement adapté aux applications web et mobiles.

Ce rapport explore l'installation de CouchDB, l'ajout et la manipulation de documents, ainsi que l'utilisation de MapReduce pour des requêtes avancées.

---

## 1. Installation et Configuration

### 1.1 Installation avec Docker (Méthode recommandée)
```bash
docker run -d --name couchdb \
  -e COUCHDB_USER=admin \
  -e COUCHDB_PASSWORD=secret \
  -p 5984:5984 \
  couchdb:latest
```
- `COUCHDB_USER` et `COUCHDB_PASSWORD` définissent les identifiants d’administration.
- `-p 5984:5984` expose le service CouchDB sur le port 5984.

### 1.2 Vérification du bon fonctionnement
```bash
curl -X GET http://admin:secret@localhost:5984/
```
La réponse doit contenir `{"couchdb":"Welcome", "version":"X.X.X"}` confirmant l’installation réussie.

---

## 2. Manipulation de Données

### 2.1 Création d’une base de données
```bash
curl -X PUT http://admin:secret@localhost:5984/films
```

### 2.2 Ajout de documents

#### Ajout d’un document unique
```bash
curl -X POST http://admin:secret@localhost:5984/films \
  -H "Content-Type: application/json" \
  -d '{"title":"Inception", "year":2010}'
```

#### Ajout en masse
```bash
curl -X POST http://admin:secret@localhost:5984/films/_bulk_docs \
  -H "Content-Type: application/json" \
  -d @films.json
```

### 2.3 Récupération et mise à jour de documents

#### Lire un document
```bash
curl -X GET http://admin:secret@localhost:5984/films/doc_id
```

#### Modifier un document existant
```bash
curl -X PUT http://admin:secret@localhost:5984/films/doc_id \
  -H "Content-Type: application/json" \
  -d '{"_rev":"1-xxx", "title":"Interstellar", "year":2014}'
```

---

## 3. Utilisation de MapReduce

### 3.1 Vue 1 : Nombre de films par année

#### Fonction Map
```javascript
function (doc) {
  if (doc.year && doc.title) {
    emit(doc.year, 1);
  }
}
```

#### Fonction Reduce
```javascript
function (keys, values, rereduce) {
  return sum(values);
}
```
Cette vue permet d’obtenir le nombre de films sortis par année.

### 3.2 Vue 2 : Films par acteur

#### Fonction Map
```javascript
function (doc) {
  if (doc.actors) {
    doc.actors.forEach(actor => {
      emit(actor.name, doc.title);
    });
  }
}
```

#### Fonction Reduce
```javascript
function (keys, values) {
  return { count: values.length, films: values };
}
```
Cette vue regroupe les films joués par chaque acteur et compte le nombre total de films.

### 3.3 Vue 3 : Films par réalisateur

#### Fonction Map
```javascript
function (doc) {
  if (doc.director) {
    emit(doc.director, doc.title);
  }
}
```

#### Fonction Reduce
```javascript
function (keys, values) {
  return values.length;
}
```
Cette vue regroupe les films réalisés par chaque réalisateur et compte le total de films par réalisateur.

---

## 4. Calcul Matriciel avec MapReduce

### 4.1 Modélisation des documents
Chaque ligne de la matrice est stockée sous forme de document JSON :
```json
{
  "page": "P1",
  "liens": [
    {"vers": "P2", "poids": 0.3},
    {"vers": "P3", "poids": 0.7}
  ]
}
```

### 4.2 Calcul de la norme des vecteurs

#### Fonction Map
```javascript
function (doc) {
  var somme = 0;
  doc.liens.forEach(function (lien) {
    somme += Math.pow(lien.poids, 2);
  });
  emit(doc.page, Math.sqrt(somme));
}
```

#### Fonction Reduce
```javascript
function (keys, values, rereduce) {
  return values;
}
```
Cette vue calcule la norme de chaque vecteur représentant une page web.

### 4.3 Produit Matrice-Vecteur

#### Fonction Map
```javascript
function (doc) {
  doc.liens.forEach(function (lien) {
    emit(lien.vers, lien.poids * W[lien.vers]);
  });
}
```

#### Fonction Reduce
```javascript
function (keys, values, rereduce) {
  return values.reduce(function (a, b) { return a + b; }, 0);
}
```
Cette vue permet de multiplier une matrice M par un vecteur W pour obtenir une nouvelle répartition des poids.

---

## 5. Conclusion

CouchDB est une base de données puissante et flexible qui se distingue par :
- Son stockage **JSON natif**
- Son API **RESTful**
- Son moteur de requêtes **MapReduce**
- Sa **réplication multi-cluster**

Grâce à **MapReduce**, CouchDB permet d'effectuer des agrégations complexes et efficaces sur de grandes quantités de données. Il est particulièrement adapté aux applications web et mobiles nécessitant une synchronisation fiable des données.

---

## 6. Ressources Complémentaires
- **Documentation officielle** : [https://docs.couchdb.org/](https://docs.couchdb.org/)
- **CouchDB en 10 minutes** : [https://guide.couchdb.org](https://guide.couchdb.org)

