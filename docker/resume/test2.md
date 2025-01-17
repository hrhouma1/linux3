---
# PRATIQUE 0 - Résumé des commandes Docker Fondamentales
---


# **Gestion des Conteneurs**
```ascii
┌─ Commandes de Base ─────────────────────────────┐
│ docker ps                # Conteneurs actifs     │
│ docker ps -a            # Tous conteneurs       │
│ docker ps -q            # Uniquement les IDs    │
│ docker start <nom/id>   # Démarrer conteneur    │
│ docker stop <nom/id>    # Arrêter conteneur     │
│ docker restart <nom/id> # Redémarrer conteneur  │
│ docker rm <nom/id>      # Supprimer conteneur   │
│ docker logs <nom/id>    # Voir les logs         │
│ docker inspect <nom/id> # Détails conteneur     │
└───────────────────────────────────────────────-─┘

┌─ Exécution et Interaction ──────────────────────┐
│ docker run -d image     # Démarrer détaché      │
│ docker run -it image    # Mode interactif       │
│ docker exec -it c1 cmd  # Exécuter commande     │
│ docker attach c1        # Attacher au conteneur │
│ docker cp src:dst c1    # Copier fichiers       │
│ docker top c1           # Processus actifs      │
└───────────────────────────────────────────────-─┘

┌─ Options de Run ────────────────────────────────┐
│ --name nom             # Nommer le conteneur    │
│ -p 8080:80            # Mapper les ports       │
│ -v /host:/container   # Monter volume          │
│ -e VAR=valeur         # Variable d'environ.    │
│ --network réseau      # Connecter au réseau    │
│ --restart=always      # Redémarrage auto       │
└───────────────────────────────────────────────-─┘
```

# **Gestion des Images**
```ascii
┌─ Commandes Images ──────────────────────────────┐
│ docker images          # Lister images          │
│ docker pull image      # Télécharger image     │
│ docker push image      # Pousser image         │
│ docker rmi image       # Supprimer image       │
│ docker build -t img .  # Construire image      │
│ docker tag img new     # Tagger image          │
│ docker save img > file # Sauvegarder image     │
│ docker load < file     # Charger image         │
└───────────────────────────────────────────────-─┘

┌─ Construction ────────────────────────────────┐
│ docker commit c1 img   # Créer image         │
│ docker history img     # Voir historique     │
│ docker diff c1         # Voir modifications  │
│ docker search terme    # Chercher images     │
└─────────────────────────────────────────────-─┘
```

# **Réseau**
```ascii
┌─ Gestion Réseau ───────────────────────────────┐
│ docker network ls      # Lister réseaux        │
│ docker network create  # Créer réseau          │
│ docker network rm      # Supprimer réseau      │
│ docker network connect # Connecter conteneur   │
│ docker network prune   # Nettoyer réseaux     │
└───────────────────────────────────────────────┘

┌─ Configuration ───────────────────────────────┐
│ --network bridge      # Réseau par défaut    │
│ --network host        # Mode hôte            │
│ --network none        # Sans réseau          │
│ --dns 8.8.8.8        # Configurer DNS       │
└─────────────────────────────────────────────-┘
```

# **Volumes et Stockage**
```ascii
┌─ Gestion Volumes ──────────────────────────────┐
│ docker volume ls       # Lister volumes        │
│ docker volume create   # Créer volume          │
│ docker volume rm       # Supprimer volume      │
│ docker volume prune    # Nettoyer volumes      │
│ docker volume inspect  # Inspecter volume      │
└───────────────────────────────────────────────┘

┌─ Types de Montage ─────────────────────────────┐
│ -v vol:/chemin        # Volume nommé           │
│ -v /host:/container   # Bind mount             │
│ --mount type=volume   # Mount explicite        │
│ --tmpfs /chemin       # Montage temporaire     │
└───────────────────────────────────────────────┘
```

# **Docker Compose**
```ascii
┌─ Commandes Compose ────────────────────────────┐
│ docker-compose up     # Démarrer services      │
│ docker-compose down   # Arrêter services       │
│ docker-compose ps     # État services          │
│ docker-compose logs   # Logs services          │
│ docker-compose build  # Construire services    │
│ docker-compose pull   # Télécharger images     │
└───────────────────────────────────────────────┘

┌─ Options Compose ──────────────────────────────┐
│ -d                    # Mode détaché           │
│ --build              # Forcer construction     │
│ --force-recreate     # Recréer conteneurs     │
│ --scale service=n    # Échelle service        │
│ -f fichier.yml       # Fichier spécifique     │
└───────────────────────────────────────────────┘
```

# **Maintenance**
```ascii
┌─ Nettoyage ──────────────────────────────────┐
│ docker system prune   # Nettoyer système     │
│ docker container prune# Nettoyer conteneurs  │
│ docker image prune    # Nettoyer images      │
│ docker network prune  # Nettoyer réseaux     │
│ docker volume prune   # Nettoyer volumes     │
└──────────────────────────────────────────────┘

┌─ Surveillance ────────────────────────────────┐
│ docker stats          # Statistiques         │
│ docker events         # Événements           │
│ docker info          # Info système          │
│ docker version       # Version Docker        │
└──────────────────────────────────────────────┘
```

# **Registry**
```ascii
┌─ Registry ───────────────────────────────────┐
│ docker login         # Connexion registry    │
│ docker logout        # Déconnexion          │
│ docker pull img      # Télécharger image    │
│ docker push img      # Pousser image        │
│ docker search term   # Rechercher images    │
└──────────────────────────────────────────────┘
```



---
# PRATIQUE 1 - Introduction aux commandes de base
---

# Partie 1 : Commandes de base

Ce guide pratique vous accompagne pas à pas dans les commandes de base de Docker.

# Configuration initiale du système
1. `sudo su -` # Passer en mode administrateur
2. `apt-get update` # Mettre à jour les paquets
3. `cat /etc/os-release` # Afficher la version du système

# Commandes Docker essentielles
4. `docker ps` # Afficher les conteneurs en cours d'exécution
5. `docker ps -a` ou `docker ps --all` # Afficher tous les conteneurs
6. `docker run hello-world` # Tester l'installation de Docker

# Partie 2 : Installation d'un conteneur Ubuntu 20.04
# Création et configuration du conteneur
7. `docker run -d -it --name myapacheubuntu -p 81:80 ubuntu:20.04` # Créer et démarrer le conteneur
8. `docker images` # Lister les images Docker
9. `docker ps` # Vérifier le conteneur en cours d'exécution
10. `docker ps -a` # Afficher tous les conteneurs

# Installation et configuration d'Apache
11. `docker exec -it myapacheubuntu bash` # Se connecter au conteneur
12. `cat /etc/os-release` # Vérifier la version d'Ubuntu
13. `apt update` # Mettre à jour les paquets
14. `apt install apache2` # Installer Apache2

# Tests et vérifications
15. `curl localhost:80` # Test initial (échec prévu - curl non installé et Apache non démarré)
16. `apt install curl` # Installer curl
17. `curl localhost:80` # Retester la connexion
18. `service apache2 status` # Vérifier le statut d'Apache (inactif)
19. `service apache2 start` # Démarrer Apache
20. `service apache2 status` # Revérifier le statut
21. Testez `Votre_adresse_IP:81` (exemple: `52.90.12.80:81`)

# Modification de la page par défaut
# Gestion des fichiers web
22. `cd /var/www/html/` # Aller dans le répertoire web
23. `ls` # Afficher le contenu (page index.html par défaut)
24. `rm index.html` # Supprimer la page par défaut
25. `ls` # Vérifier la suppression
26. `apt install wget` # Installer wget pour télécharger des pages web
27. `wget www.yahoo.com` # Télécharger la page Yahoo
28. `ls` # Vérifier le téléchargement

# Tests finaux et déconnexion
29. Testez sur le navigateur `Votre_adresse_IP:81`
30. `curl localhost:80` # Tester en local
31. `exit` # Sortir du conteneur
32. `curl VOTRE_ADRESSE_IP:81` # Tester depuis la machine virtuelle
33. `exit` # Quitter le mode administrateur
34. `exit` # Quitter la machine virtuelle



---
# PRATIQUE 2 - Introduction aux commandes de base
---
