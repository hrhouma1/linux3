### **Question 1 : Création et optimisation d'une image Docker**
Vous développez une application web en **Node.js** qui nécessite l'installation des dépendances via **npm** et le lancement du serveur avec `node app.js`.  

#### **Tâches :**
1. **Écrivez un Dockerfile optimisé** pour cette application en respectant les **bonnes pratiques** (taille réduite, gestion efficace des dépendances, build performant).
2. Expliquez comment exécuter un **conteneur** à partir de cette image en mappant le port 3000 du conteneur avec le port 8080 de l'hôte.

---

### **Question 2 : Orchestration avec Docker Compose**
Vous devez déployer une **application Flask** qui interagit avec une base de données **PostgreSQL** en utilisant **Docker Compose**.

#### **Tâches :**
1. **Écrivez un fichier `docker-compose.yml`** qui définit :
   - Un service **web** basé sur l’image officielle **Python**, exécutant l’application Flask.
   - Un service **db** utilisant **PostgreSQL**, avec un volume pour la persistance des données.
   - Une configuration réseau permettant la communication entre les services.

2. Expliquez **les commandes Docker Compose** nécessaires pour :
   - Lancer les services.
   - Voir les logs de l’application Flask.
   - Arrêter et supprimer les conteneurs sans perdre les données.
