# Commandes Docker Fondamentales

**Gestion des Conteneurs**
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

**Gestion des Images**
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

**Réseau**
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

**Volumes et Stockage**
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

**Docker Compose**
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

**Maintenance**
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

**Registry**
```ascii
┌─ Registry ───────────────────────────────────┐
│ docker login         # Connexion registry    │
│ docker logout        # Déconnexion          │
│ docker pull img      # Télécharger image    │
│ docker push img      # Pousser image        │
│ docker search term   # Rechercher images    │
└──────────────────────────────────────────────┘
```

