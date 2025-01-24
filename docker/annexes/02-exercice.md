### **Démonstration Complète : Dockerfile avec Apache et Utilisation des Arguments**

Dans cet exercice, nous allons démontrer comment configurer un serveur Apache avec Docker, en utilisant des **variables ARG et ENV**, et expliquer les options comme `-p` (ports), `--name` (nom du conteneur), et d'autres paramètres Docker.

---

#### **Dockerfile pour Apache**
```Dockerfile
# Utiliser une image de base avec Apache
FROM httpd:2.4

# Définir une variable ARG pour personnaliser la page d'accueil
ARG WELCOME_MESSAGE="Bienvenue sur mon serveur Apache Docker!"

# Copier une page d'accueil personnalisée dans le dossier racine d'Apache
COPY index.html /usr/local/apache2/htdocs/

# Utiliser ENV pour rendre le message accessible dans le conteneur
ENV MESSAGE=${WELCOME_MESSAGE}

# Ajouter une commande pour injecter le message dans la page d'accueil au démarrage
CMD echo "<html><body><h1>$MESSAGE</h1></body></html>" > /usr/local/apache2/htdocs/index.html && httpd-foreground
```

---

#### **Créer un fichier `index.html` pour la page d'accueil**
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bienvenue</title>
</head>
<body>
    <h1>Message par défaut</h1>
    <p>Ce contenu peut être remplacé par le message personnalisé défini lors de la construction.</p>
</body>
</html>
```

---

### **Explications**
1. **Image de base :**
   - On utilise `httpd:2.4`, une image officielle d'Apache.
   - Cela configure un serveur HTTP prêt à l'emploi.

2. **ARG et ENV :**
   - `ARG WELCOME_MESSAGE`: Permet de définir un message d'accueil lors de la construction de l'image.
   - `ENV MESSAGE=${WELCOME_MESSAGE}`: Passe ce message en tant que variable d'environnement pour le conteneur.

3. **CMD :**
   - Injecte dynamiquement le message personnalisé dans le fichier HTML.
   - Lance ensuite Apache (`httpd-foreground`).

---

### **Étapes d'Exécution**

1. **Construire l'image :**
   ```bash
   docker build -t apache-demo --build-arg WELCOME_MESSAGE="Bienvenue à tous les étudiants Docker!" .
   ```

2. **Lancer un conteneur avec Apache :**
   - Attribuer un nom au conteneur (`--name`).
   - Publier le port Apache (80 par défaut) sur un port local (`-p`).
   ```bash
   docker run --name my-apache-container -d -p 8080:80 apache-demo
   ```

3. **Tester le serveur :**
   - Ouvrez votre navigateur à l’adresse : [http://localhost:8080](http://localhost:8080).
   - Le message personnalisé devrait s’afficher.

4. **Afficher les logs du conteneur :**
   ```bash
   docker logs my-apache-container
   ```

5. **Arrêter et supprimer le conteneur :**
   - Arrêter :
     ```bash
     docker stop my-apache-container
     ```
   - Supprimer :
     ```bash
     docker rm my-apache-container
     ```

---

### **Explication des Commandes Docker**

- `docker build -t apache-demo ...` : Crée une image Docker avec le nom `apache-demo`.
- `docker run --name my-apache-container ...` :
  - `--name` : Attribue un nom personnalisé au conteneur.
  - `-d` : Détache le conteneur pour qu'il s'exécute en arrière-plan.
  - `-p 8080:80` : Redirige le port 8080 de l’hôte vers le port 80 du conteneur.
- `docker logs` : Affiche les journaux du conteneur.
- `docker stop` et `docker rm` : Permettent d’arrêter et de supprimer le conteneur.

---

### **Exercice Supplémentaire**

1. **Personnaliser le Message :**
   - Construisez l’image avec une autre valeur pour `WELCOME_MESSAGE` :
     ```bash
     docker build -t apache-demo --build-arg WELCOME_MESSAGE="Bonjour depuis un conteneur Apache !" .
     ```

2. **Changer de Port :**
   - Lancez un autre conteneur avec un port différent :
     ```bash
     docker run --name apache-alt -d -p 9090:80 apache-demo
     ```
   - Accédez au serveur sur [http://localhost:9090](http://localhost:9090).

3. **Vérifier les Conteneurs en Cours d’Exécution :**
   - Utilisez la commande suivante pour voir les conteneurs actifs :
     ```bash
     docker ps
     ```

4. **Supprimer l’Image :**
   - Une fois les tests terminés, supprimez l’image pour libérer de l’espace :
     ```bash
     docker image rm apache-demo
     ```

---

### **Résultats Attendus**
1. Une page personnalisée affichant le message fourni lors de la construction.
2. Une compréhension pratique des options Docker (`-p`, `--name`, `docker logs`, etc.).
3. La capacité de personnaliser et de gérer un serveur Apache avec Docker.

---

### **Conclusion**
Cet exercice vous montre comment utiliser **ARG** et **ENV** pour personnaliser un serveur Apache, tout en explorant les principales commandes Docker pour une gestion efficace des conteneurs.




# Annexe + Résumé


### Contenus des fichiers

#### **Fichier `Dockerfile`**
```Dockerfile
FROM httpd:2.4

ARG WELCOME_MESSAGE="Bienvenue sur mon serveur Apache Docker!"
ENV MESSAGE=${WELCOME_MESSAGE}

COPY index.html /usr/local/apache2/htdocs/

CMD echo "<html><body><h1>$MESSAGE</h1></body></html>" > /usr/local/apache2/htdocs/index.html && httpd-foreground
```

#### **Fichier `index.html`**
```html
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>Bienvenue</title>
</head>
<body>
    <h1>Message par défaut</h1>
</body>
</html>
```

### Commandes Complètes
```bash
# Créer le fichier Dockerfile
touch Dockerfile

# Créer le fichier index.html
touch index.html

# Construire l'image avec un message personnalisé
docker build -t apache-demo --build-arg WELCOME_MESSAGE="Bonjour depuis Apache Docker!" .

# Lancer le conteneur sur le port 8080 avec un nom personnalisé
docker run --name my-apache-container -d -p 8080:80 apache-demo

# Lister les conteneurs en cours d'exécution
docker ps

# Tester le serveur avec curl
curl http://localhost:8080

# Arrêter le conteneur
docker stop my-apache-container

# Supprimer le conteneur
docker rm my-apache-container

# Supprimer l'image Docker
docker rmi apache-demo
```
