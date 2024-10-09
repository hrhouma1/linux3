# Pratique 06 📋 : Quiz Docker - Commandes et Docker Compose

*Ce quiz est exclusivement centré sur Docker et inclut des questions couvrant les commandes Docker de base, l'utilisation des Dockerfiles, ainsi que des fichiers `docker-compose.yml`.*

### **Partie 1 - QUIZ FORMATIF DOCKER**

1. **Quelle commande est utilisée pour lister tous les conteneurs Docker, y compris ceux qui ne sont pas en cours d'exécution ?**  
*Choix unique* (3 points)  
- `docker ps`  
- `docker images`  
- `docker ps -a`  
- `docker stop`

---

2. **Quelle directive de Dockerfile est utilisée pour copier des fichiers ou des répertoires depuis l'hôte vers l'image Docker ?**  
*Choix unique* (3 points)  
- `ADD`  
- `RUN`  
- `COPY`  
- `EXPOSE`

---

3. **Quel est le but du fichier `docker-compose.yml` ?**  
*Choix unique* (2 points)  
- Gérer le processus de déploiement d'une application dans un environnement Kubernetes  
- Définir des services, des réseaux et des volumes pour une application Docker multi-conteneurs  
- Automatiser la création de clusters Docker Swarm  
- Compiler une image Docker à partir d'un Dockerfile

---

4. **Quelle commande Docker est utilisée pour supprimer une image spécifique ?**  
*Choix unique* (3 points)  
- `docker remove <image>`  
- `docker rmi <image>`  
- `docker rm <image>`  
- `docker delete <image>`

---

5. **Comment appelle-t-on une unité portable et légère utilisée pour exécuter des applications dans Docker ?**  
*Choix unique* (3 points)  
- Machine virtuelle  
- Image  
- Conteneur  
- Cluster

---

6. **Quelle commande est utilisée pour créer un nouveau conteneur à partir d'une image existante ?**  
*Choix unique* (2 points)  
- `docker build`  
- `docker run`  
- `docker start`  
- `docker create`

---

7. **Quelle commande Docker est utilisée pour afficher les informations sur l'ensemble du système Docker, y compris les conteneurs, les images et les volumes ?**  
*Choix unique* (1 point)  
- `docker inspect`  
- `docker info`  
- `docker system`  
- `docker stats`

---

8. **Quelle instruction Dockerfile est utilisée pour installer des dépendances à l'intérieur d'une image Docker ?**  
*Choix unique* (2 points)  
- `CMD`  
- `ENTRYPOINT`  
- `COPY`  
- `RUN`

---

9. **Quelle commande Docker permet d'attacher le terminal local au processus standard d'un conteneur en cours d'exécution ?**  
*Choix unique* (2 points)  
- `docker start`  
- `docker exec`  
- `docker attach`  
- `docker run`

---

10. **Quel outil Docker permet de gérer le déploiement de conteneurs sur plusieurs machines en utilisant une seule commande ?**  
*Choix unique* (2 points)  
- Docker Machine  
- Docker Swarm  
- Docker Compose  
- Docker Hub

---

11. **Quelle option permet de suivre en direct les journaux d'un conteneur Docker ?**  
*Choix unique* (2 points)  
- `docker logs -f <conteneur>`  
- `docker follow <conteneur>`  
- `docker trace <conteneur>`  
- `docker inspect <conteneur>`

---

12. **Quelle commande Docker crée un nouveau conteneur à partir d'une image spécifique ?**  
*Choix unique* (2 points)  
- `docker run <image>`  
- `docker create <image>`  
- `docker start <image>`  
- `docker init <image>`

---

13. **Comment peut-on passer des variables d'environnement à un conteneur au moment de son lancement ?**  
*Choix unique* (2 points)  
- En utilisant l'option `-e` avec `docker run`  
- Avec l'option `--config` dans `docker start`  
- En modifiant le Dockerfile pour inclure les variables  
- Par le biais de la commande `docker vars set`

---

14. **Quelle commande est utilisée pour afficher la version actuelle de Docker installée sur le système ?**  
*Choix unique* (2 points)  
- `docker --version`  
- `docker version`  
- `docker info`  
- `docker config`

---

15. **Quel outil est utilisé pour gérer les secrets et les configurations sensibles dans un cluster Docker Swarm ?**  
*Choix unique* (2 points)  
- Docker Configs  
- Docker Secrets  
- Docker Volumes  
- Docker Registers

---

16. **Quel service est configuré pour démarrer avec `docker-compose up` dans cet extrait de `docker-compose.yml` ?**  
*Choix unique* (3 points)  
```yaml
version: '3'
services:
  web:
    image: nginx
    ports:
      - "80:80"
```
- web  
- nginx  
- ports  
- 80

---

17. **Quelle directive est utilisée pour monter un volume nommé `data` dans cet exemple de `docker-compose.yml` ?**  
*Choix unique* (2 points)  
```yaml
version: '3'
services:
  app:
    image: myapp
    volumes:
      - data:/var/lib/data
volumes:
  data:
```
- services  
- app  
- image  
- volumes

---

18. **Quelle commande Docker Compose est utilisée pour arrêter tous les services définis dans un fichier `docker-compose.yml` ?**  
*Choix unique* (2 points)  
- `docker-compose down`  
- `docker-compose stop`  
- `docker-compose remove`  
- `docker-compose halt`

---

19. **Comment exécuter un service en arrière-plan à l'aide de Docker Compose ?**  
*Choix unique* (3 points)  
- `docker-compose run <service> -d`  
- `docker-compose exec <service> -d`  
- `docker-compose up -d`  
- `docker-compose start -d`

---

20. **Quel est le rôle de l'option `depends_on` dans cet exemple de `docker-compose.yml` ?**  
*Choix unique* (2 points)  
```yaml
version: '3'
services:
  web:
    image: nginx
    depends_on:
      - db
  db:
    image: postgres
```
- Définir l'ordre de démarrage des services  
- Spécifier les variables d'environnement pour les services  
- Configurer les ports exposés par le service  
- Définir le nom de l'image à utiliser


