---
# PRATIQUE 1 - Introduction aux commandes de base
---

# Vue d'ensemble
Ce guide pratique vous accompagne pas à pas dans les commandes de base de Docker.

# Partie 1 : Commandes de base
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
