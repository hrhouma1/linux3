
===============================================
# PRATIQUE 1: DOCKER SUR AZURE 🐳
===============================================

   __________________________________________________________
  |                                                          |
  | 📚 VUE D'ENSEMBLE                                         |
  |__________________________________________________________|

  Ce guide pratique vous accompagne pas à pas dans la 
  maîtrise de Docker sur Azure, avec une attention particulière 
  sur les commandes de base, les conteneurs, Dockerfile, 
  et les volumes.

===============================================
🔰 PARTIE 1 : COMMANDES DE BASE
===============================================

   __________________________________________________________
  | COMMANDES ESSENTIELLES                                    |
  |__________________________________________________________|

  1. `sudo su -`                # Passer en mode administrateur
  2. `apt-get update`           # Mettre à jour les paquets
  3. `cat /etc/os-release`      # Afficher la version du système

  ---
  COMMANDES DOCKER :
  ---
  4. `docker ps`                # Conteneurs en cours d'exécution
  5. `docker ps -a`             # Tous les conteneurs
  6. `docker run hello-world`   # Tester Docker

===============================================
🚀 PARTIE 2 : INSTALLATION D'UN CONTENEUR UBUNTU
===============================================

   __________________________________________________________
  | CRÉATION DU CONTENEUR                                    |
  |__________________________________________________________|

  1. Créez et démarrez un conteneur Ubuntu :
     ```
     docker run -d -it --name myapacheubuntu -p 81:80 ubuntu:20.04
     ```

  2. Lister les images disponibles :
     ```
     docker images
     ```

  3. Vérifiez le conteneur actif :
     ```
     docker ps
     ```

  ---
  CONFIGURATION D'APACHE :
  ---
  4. Connectez-vous au conteneur :
     ```
     docker exec -it myapacheubuntu bash
     ```

  5. Installez Apache et testez son statut :
     ```
     apt update && apt install apache2
     service apache2 status
     ```

  6. Testez la page sur votre navigateur :
     ```
     http://<Votre_IP>:81
     ```

===============================================
🌐 MODIFICATION DE LA PAGE PAR DÉFAUT
===============================================

   __________________________________________________________
  | CHANGEMENT DU FICHIER HTML                               |
  |__________________________________________________________|

  1. Accédez au dossier web :
     ```
     cd /var/www/html/
     ```

  2. Supprimez le fichier par défaut :
     ```
     rm index.html
     ```

  3. Téléchargez une nouvelle page :
     ```
     wget www.yahoo.com
     ```

  4. Vérifiez les changements dans le navigateur :
     ```
     http://<Votre_IP>:81
     ```

===============================================
🗑️ SUPPRESSION DES RESSOURCES DOCKER
===============================================

   __________________________________________________________
  | CONTENEURS                                               |
  |__________________________________________________________|

  1. Listez tous les conteneurs :
     ```
     docker ps -a
     ```

  2. Arrêtez tous les conteneurs actifs :
     ```
     docker stop $(docker ps -a -q)
     ```

  3. Supprimez les conteneurs arrêtés :
     ```
     docker rm $(docker ps -a -q)
     ```

  ---
  IMAGES :
  ---
  4. Listez toutes les images :
     ```
     docker images
     ```

  5. Supprimez toutes les images :
     ```
     docker rmi $(docker images -q)
     ```

===============================================
🔄 MANIPULATIONS SPÉCIALES
===============================================

   __________________________________________________________
  | SERVEUR FLASK AVEC DOCKERFILE                            |
  |__________________________________________________________|

  Exemple d'un Dockerfile simple :
  ```
  FROM python:3.7
  COPY myapp.py /tmp
  RUN pip install flask
  EXPOSE 8080
  CMD ["python", "/tmp/myapp.py"]
  ```

  - Étape 1 : Construisez l'image :
    ```
    docker build -t flask_server .
    ```

  - Étape 2 : Démarrez le conteneur :
    ```
    docker run -d -p 8080:8080 flask_server
    ```

===============================================
📦 DOCKER VOLUMES
===============================================

   __________________________________________________________
  | PERSISTANCE DES DONNÉES                                  |
  |__________________________________________________________|

  1. Créez un conteneur avec un volume :
     ```
     docker run -d --name mysql \
       -e MYSQL_ROOT_PASSWORD=root \
       -v /opt/data:/var/lib/mysql mysql
     ```

  2. Vérifiez les données persistantes :
     ```
     ls /opt/data
     ```

  ---
  RÉUTILISATION :
  ---
  - Relancez un nouveau conteneur pointant sur le volume existant :
    ```
    docker run -d --name mysql \
      -e MYSQL_ROOT_PASSWORD=root \
      -v /opt/data:/var/lib/mysql mysql
    ```

===============================================
🚀 DOCKER COMPOSE
===============================================

   __________________________________________________________
  | EXEMPLE AVEC WORDPRESS & MYSQL                          |
  |__________________________________________________________|

  Contenu du fichier `docker-compose.yaml` :
  ```
  version: '3.3'

  services:
     db:
       image: mysql:5.7
       environment:
         MYSQL_ROOT_PASSWORD: somepassword
         MYSQL_DATABASE: wordpress
         MYSQL_USER: wordpress
         MYSQL_PASSWORD: wordpress
     wordpress:
       image: wordpress:latest
       depends_on:
         - db
       ports:
         - "8000:80"
  ```

  1. Lancez tous les services :
     ```
     docker-compose up -d
     ```

  2. Arrêtez les services et supprimez les conteneurs :
     ```
     docker-compose down
     ```

===============================================
RÉSUMÉ DES COMMANDES 📝
===============================================

```
docker ps                      # Conteneurs actifs
docker images                  # Images disponibles
docker run -d --name c1 nginx  # Démarrer un conteneur nginx
docker stop $(docker ps -a -q) # Arrêter tous les conteneurs
docker rm $(docker ps -a -q)   # Supprimer tous les conteneurs
docker rmi $(docker images -q) # Supprimer toutes les images
docker-compose up              # Lancer avec Docker Compose
docker-compose down            # Arrêter avec Docker Compose
```

===============================================
FIN DU DOCUMENT 📘
===============================================
```

This structure includes an ASCII-styled layout and organizes your content for clarity and usability. Let me know if you want further modifications or additions!
