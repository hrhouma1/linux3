# PRATIQUE 1 DOCKER SUR AZURE 🐳

## 📚 Vue d'ensemble
Ce guide pratique vous accompagne pas à pas dans la maîtrise de Docker sur Azure.

## 🔰 Partie 1 : Commandes de base
### Configuration initiale du système
1. `sudo su -` # Passer en mode administrateur
2. `apt-get update` # Mettre à jour les paquets
3. `cat /etc/os-release` # Afficher la version du système

### Commandes Docker essentielles
4. `docker ps` # Afficher les conteneurs en cours d'exécution
5. `docker ps -a` ou `docker ps --all` # Afficher tous les conteneurs
6. `docker run hello-world` # Tester l'installation de Docker

## 🚀 Partie 2 : Installation d'un conteneur Ubuntu 20.04
### Création et configuration du conteneur
7. `docker run -d -it --name myapacheubuntu -p 81:80 ubuntu:20.04` # Créer et démarrer le conteneur
8. `docker images` # Lister les images Docker
9. `docker ps` # Vérifier le conteneur en cours d'exécution
10. `docker ps -a` # Afficher tous les conteneurs

### Installation et configuration d'Apache
11. `docker exec -it myapacheubuntu bash` # Se connecter au conteneur
12. `cat /etc/os-release` # Vérifier la version d'Ubuntu
13. `apt update` # Mettre à jour les paquets
14. `apt install apache2` # Installer Apache2

### Tests et vérifications
15. `curl localhost:80` # Test initial (échec prévu - curl non installé et Apache non démarré)
16. `apt install curl` # Installer curl
17. `curl localhost:80` # Retester la connexion
18. `service apache2 status` # Vérifier le statut d'Apache (inactif)
19. `service apache2 start` # Démarrer Apache
20. `service apache2 status` # Revérifier le statut
21. Testez `Votre_adresse_IP:81` (exemple: `52.90.12.80:81`)

## 🌐 Modification de la page par défaut
### Gestion des fichiers web
22. `cd /var/www/html/` # Aller dans le répertoire web
23. `ls` # Afficher le contenu (page index.html par défaut)
24. `rm index.html` # Supprimer la page par défaut
25. `ls` # Vérifier la suppression
26. `apt install wget` # Installer wget pour télécharger des pages web
27. `wget www.yahoo.com` # Télécharger la page Yahoo
28. `ls` # Vérifier le téléchargement

### Tests finaux et déconnexion
29. Testez sur le navigateur `Votre_adresse_IP:81`
30. `curl localhost:80` # Tester en local
31. `exit` # Sortir du conteneur
32. `curl VOTRE_ADRESSE_IP:81` # Tester depuis la machine virtuelle
33. `exit` # Quitter le mode administrateur
34. `exit` # Quitter la machine virtuelle

# Pratique 2
# Table des Manipulations

## Partie 1 - Introduction et Commandes de Base
Manipulations # 0 - Introduction aux commandes Docker de base
Manipulations # 1 - Commandes essentielles pour la gestion des conteneurs et images

## Partie 2 - Création de Serveurs avec Dockerfile
Manipulations # 2 - Serveur Python + Flask (Démo 1)
Manipulations # 3 - Serveur AlmaLinux + Apache (Démo 2) 
Manipulations # 4 - Gestion avancée des conteneurs et création d'une image Nginx
Manipulations # 5 - Serveur Nginx avec modification des ports (Démo 3)

## Partie 3 - Exercices Pratiques
Manipulations # 6 - Révision des commandes de gestion Docker
Manipulations # 7 - Devoir pratique (sans Dockerfile)
Manipulations # 8 - Correction du devoir
Manipulations # 9 - Approfondissement: Gestion des conteneurs et création d'images


🗑️ Manipulations # 0 - COMMENT SUPPRIMER DES CONTENEURS ET DES IMAGES
# Supprimer les conteneurs
## Afficher les conteneurs
* `docker ps`            # Affiche les conteneurs en cours d'exécution
* `docker ps -a`         # Affiche tous les conteneurs (actifs et arrêtés)
* `docker ps -a -q`      # Affiche uniquement les IDs de tous les conteneurs

## Arrêter et supprimer les conteneurs
* `docker stop $(docker ps -a -q)`  # Arrête tous les conteneurs en cours d'exécution
* `docker rm $(docker ps -a -q)`    # Supprime tous les conteneurs arrêtés

# Supprimer les images
## Afficher les images
* `docker images`        # Liste toutes les images Docker
* `docker images -q`     # Liste uniquement les IDs des images

## Supprimer les images
* `docker rmi $(docker images -q)`  # Supprime toutes les images Docker
























🔄 Manipulations # 1 - COMMANDES DE BASE DOCKER

# Gestion des images
## Télécharger et afficher les images
* `docker pull centos`     # Télécharge l'image CentOS depuis Docker Hub
* `docker images`         # Liste toutes les images Docker disponibles localement

# Gestion des conteneurs 
## Créer et gérer un conteneur
* `docker run -d --name c1 nginx`  # Crée et démarre un conteneur nginx en arrière-plan
* `docker images`                  # Vérifie les images disponibles  
* `docker ps`                      # Liste les conteneurs en cours d'exécution
* `docker ps -a`                   # Liste tous les conteneurs (actifs et arrêtés)

## Arrêter et supprimer un conteneur
* `docker stop c1` - Arrête le conteneur c1
* `docker rm c1` - Supprime le conteneur c1  
* `docker ps` - Vérifie les conteneurs actifs
* `docker ps -a` - Vérifie tous les conteneurs
* `docker images` - Liste à nouveau les images disponibles


























🔄 Manipulations # 2 - CRÉER UN SERVEUR AVEC PYTHON + FLASK (DÉMO 1 -DOCKERFILE)

# Objectif
Créer un serveur web simple avec Python et Flask en utilisant Docker

# Ressources
- Code source: https://github.com/hrhouma/kubernetes1
- Exemple détaillé: https://github.com/hrhouma/kubernetes1/tree/kuberntes-v1/docker

# Fichiers nécessaires
## Dockerfile
Contient les instructions pour construire l'image Docker:
- Utilise Python 3.7 comme image de base
- Copie l'application Flask
- Installe les dépendances
- Expose le port 8080
- Lance l'application

## myapp.py 
Application Flask simple qui:
- Crée un serveur web basique
- Retourne un message "Hello" sur la route principale
- Écoute sur le port 8080

# Dockerfile
# Base image
``` ssh
FROM python:3.7
# Copy application code
COPY myapp.py /tmp
# Install dependencies
RUN pip install flask
# Configure container
EXPOSE 8080
CMD ["python", "/tmp/myapp.py"]
```

# myapp.py
Code source de l'application Flask:
``` ssh
from flask import Flask
import os

app = Flask(__name__)
@app.route('/')

def hello():
    return('Hello from container..This is my first docker container\n')

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080, debug=True)
```

# Étapes
1. Créer un répertoire pour le projet
2. Créer et configurer le Dockerfile
3. Créer le fichier myapp.py
4. Construire l'image Docker
5. Exécuter le conteneur

```ssh 
mkdir exemple1
cd exemple1
nano Dockerfile
copier/coller le contenu de Dockerfile
ls
nano myapp.py
```


# Copier le contenu de myapp.py


docker build -t monkiki .
docker ps
docker run -d --name c1monkiki -p 9000:8080  monkiki

⇒ monkiki : Le nom de l’image que je vais créer 
⇒ c1monkiki : Le nom de mon conteneur 

Manipulations # 3 - CRÉER UN SERVEUR AVEC ALMA + APACHE
(DÉMO 2 -DOCKERFILE)

Démo Dockerfile numéro #2
Démo Dockerfile numéro #2 (ALMA LINUX)- Voir annexe - Exercice 2 - Dockerfile

Consultez ce lien sur github : 
ou aller au dossier UTILE et télécharger les instructions depuis Exercice1- Dockerfile.txt
https://drive.google.com/drive/folders/1EVIHqsCGu5lw-rpNCdcVPdlANrDqz75K?usp=sharing 


1-cd /home/hrehouma
2-mkdir exemple2
3-cd exemple2
4-nano Dockerfile
5-tu copies et tu colles le contenu de Dockerfile
6-nano script.sh
7-tu copies et tu colles le contenu de script.sh
8-nano index.html
9-tu copies et tu colles le contenu de index.html
10- tu t’assures que tous les fichiers sont dans le dossier exemple1 en exécutant ls (Dockerfile, script.sh et index.html)
11-exécute la commande suivante : 
docker build -t monsiteapache . (le point est important)
12- exécute la commande suivante : 
docker run -d -p 8080:80 --name siteapache monsiteapache
13- Allez dans le navigateur et tu vérifies si nous avons la page web
http://localhost:8080



Supprimer les conteneurs
docker ps
docker ps -a
docker ps -a -q
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

Supprimer les images
docker images
docker images -q
docker rmi $(docker images -q)


Manipulations # 4 - COMMANDES DE BASE 2 - 
SUPPRIMER DES IMAGES ET DES CONTAINERS
&& CRÉER UNE IMAGE NGINX

docker run -it -d -p 8080:80 --name c1 nginx (testez localhost:8080)
docker run -it -d -p 8081:80 --name c2 nginx (testez localhost:8081)
docker run -it -d -p 8082:80 --name c3 nginx (testez localhost:8082)
docker ps
docker ps -q (donne les ids)
docker stop a69983c1a0d3 ou docker stop a69
docker stop 262c9da97eb4 
docker stop 66793fb59d9
docker ps
docker ps -a
docker start a69983c1a0d3
docker start 262c9da97eb4 
docker start 66793fb59d9
docker ps
docker stop c1
docker stop c2
docker stop c3
docker ps
docker start c1
docker start c2
docker start c3

docker rm a69983c1a0d3 ou docker rm c1

Si on veut supprimer tous les containers : 
docker stop $(docker ps -a -q) ensuite docker rm $(docker ps -a -q)

Astuce :  Exécutez cette commande en premier pour comprendre la signification de -q 

docker ps -a -q (ça donne les ids)

⇒ ça ne fonctionne pas !!!! En effet, les containers doivent être stoppés avant de les supprimer

docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

Est-ce que nous avons tout supprimé ? ⇒ la réponse est non ! Il reste les images
docker images
docker images -q
docker rmi $(docker images -q)
docker images
 docker run -it -d -p 8080:80 --name c1 nginx
Manipulations # 5 - CRÉER UN SERVEUR AVEC NGINX 
&& CHANGER LES PORTS INTERNES DE 80 à 90 
(DÉMO 3 -DOCKERFILE)

Démo Dockerfile numéro #3 

Consultez ce lien sur github : 

mkdir exemple3
cd exemple3
nano Dockerfile

Contenu du Dockerfile
# Utiliser une image Nginx
FROM nginx
# Copier le fichier HTML personnalisé dans le conteneur
COPY index.html /usr/share/nginx/html/index.html

nano index.html 
Contenu du index.html
<html> Hello from nginx </html>
docker build -t img_site_nginx .
docker images
docker run -d -p 9001:90 --name c1_site_nginx img_site_nginx
docker ps

Essayez http://localhost:9001 (ça ne fonctionne pas, le port interne est à 80 !!!)

docker exec -it c1_site_nginx bash
ls
cd /etc/
cd nginx
nano /etc/nginx/conf.d/default.conf (https://unix.stackexchange.com/questions/355911/trouble-installing-nano)
apt-get update
apt-get install nano
nano /etc/nginx/conf.d/default.conf

Changez le port 80 à 90
server {
    listen       90;
    listen  [::]:90;
    server_name  localhost;

nginx -s reload (https://docs.nginx.com/nginx/admin-guide/basic-functionality/runtime-control/)
exit
Essayez http://localhost:9001

Manipulations # 6 - SUPPRIMER DES CONTENEURS ET DES IMAGES

Supprimer les conteneurs

docker ps
docker ps -a
docker ps -a -q
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
docker ps -a

Supprimer les images

docker images
docker images -q
docker rmi $(docker images -q)
docker images



















Manipulations # 7 - COMMANDES DE BASE 2

docker --help
docker ps --help
docker build --help

Changer l’image : 

Reprenons l’exemple de l'exercice 1 ci-haut (Démo Dockerfile numéro #1 (PYTHON)) et construisons un container exercice 1

nano Dockerfile 
(Exemple 1 - voir page 4 Manipulations # 2 - CRÉER UN SERVEUR AVEC PYTHON + FLASK (DÉMO 1 -DOCKERFILE))

docker build -t myimage-13oct:1.0
docker images
docker run -d --name c1 -p 8081:8080 myimage-13oct:1.0
docker ps

D’autres conteneurs : 

docker run -d --name c2 -p 8082:8080 myimage-13oct:1.0
docker run -d --name c3 -p 8083:8080 myimage-13oct:1.0
docker run -d --name c4 -p 8084:8080 myimage-13oct:1.0
docker run -d --name c5 -p 8085:8080 myimage-13oct:1.0
docker ps
docker images
docker exec -it c1 bash
cd /tmp
ls (on va trouver myapp.py)
exit















Manipulations # 8 - DEVOIR À LA MAISON

DEVOIR
Faites le devoir avec deux méthodes (1-sans Dockerfile et 2- avec Dockerfile)
➔	Créez un conteneur avec une image Ubuntu (avec le nom de conteneur c1)
➔	Connectez-vous au conteneur — docker exec -it c1 bash
➔	Mettez à jour le dépôt (apt update) dans le conteneur
➔	Installez apache2 dans le conteneur (apt install apache2). 
Note :
➔	Créez un fichier index.html sur la machine hôte avec le contenu ci-dessous :
<h1>Hello world.. running in apache2</h1>
➔	Copiez ce fichier de l'hôte au conteneur à l'emplacement /var/www/html/index.html
➔	Vérifiez si vous pouvez accéder à cette page depuis un navigateur

























Manipulations # 9 - CORRECTION PARTIE 1 
&& -it
&& Créer un container avec plusieurs ports exposés
&& Créer une image à partir du container 

Partie 9-1
docker run -d --name myapache -p 81:80 ubuntu

Si vous suivez ces commandes, vous verrez que le conteneur Apache ne fonctionne pas (en utilisant 'docker ps'). Mais si vous utilisez ('docker ps -a'), vous verrez qu'il a été lancé mais s'est arrêté automatiquement (car il n'y a rien à l'intérieur du conteneur qui fonctionne).

docker rm myapache 
docker run -d -it --name myapache -p 81:80 ubuntu
docker ps
docker exec -it myapache bash
cat /etc/os-release
exit
apt update
apt install apache2
apt install curl
curl localhost:80
service apache2 status
service apache2 start
curl localhost:80
exit
RÉSUMÉ : 
 



Partie 9-2

mkdir correctiondevoir
cd correctiondevoir
vi  index.html
<h1> Hello world ..running in apache 2 </h1>
Quitter le fichier escape ensuite :wq!

Essayez la commande top

Nous allons copier le fichier index.html ci-haut

cp index.html myapache:/var/www/html/index.html
curl localhost:81
docker exec -it myapache bash
curl localhost:80
exit
curl localhost:81
Allez au navigateur et saisir : localhost:81

RÉSUMÉ : 
 


Partie 9-3
docker stop myapache
docker rm myapache

Créer un container avec plusieurs ports exposés

docker run -d -it --name myapache -p 81:80 -p 82:80 ubuntu
docker ps
docker exec -it myapache bash
apt update
apt install curl
apt install apache2
service apache2 start
exit
curl localhost:81 (ou copier localhost:81 dans le navigateur)
docker exec -it myapache bash
echo “Hello test 123” > /var/www/html/index.html
exit (je quitte le container/intérieur = port 80)
curl localhost:81 (je suis à l’extérieur)
curl localhost:82

Créer une image à partir du container 

Pour créer une image à partir du container en cours d'exécution ( ⇒ create an image from a running container)

docker commit myapache apache:1.0
docker images




dsdsd Manipulations # 0 - COMMENT SUPPRIMER DES CONTENEURS ET DES IMAGES
Manipulations # 1 - Tag, push, Save, load
Manipulations # 2 - Docker networking
Manipulations # 3 - Docker compose
Manipulations # 4 - Docker volume
Manipulations # 5 - Docker registry
Manipulations # 6 - Container orchestration, introduction à Kubernetes, docker swarm



































Manipulations # 0 - COMMENT SUPPRIMER DES CONTENEURS ET DES IMAGES

Supprimer les conteneurs

docker ps
docker ps -a
docker ps -a -q
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

Supprimer les images

docker images
docker images -q
docker rmi $(docker images -q)
 
































Manipulations # 1 - Tag, push, save, load

 
 
 
 









Manipulations # 1 Tag, Push, Save, Load

docker ps
docker ps -a 
docker rm c1 (sinon s’il roule , il faut le stopper avant avec docker stop c1)
sinon docker stop $(docker ps -a -q) && docker rm $(docker ps -a -q)
docker images (nous supposons avoir  myapache:1.0)

Code de Dockerfile pour construire l’image myapache:1.0

# Étape 1: Écrire le Dockerfile

mkdir exercice4 && cd exercice4 

cat <<EOF > Dockerfile
FROM ubuntu:latest
RUN apt-get update && apt-get install -y apache2
COPY index.html /var/www/html/index.html
EXPOSE 80
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
EOF

Explication optionnelle (sautez cette partie) : 

La commande cat <<EOF > Dockerfile est une technique couramment utilisée dans les scripts shell pour créer ou écrire dans un fichier. Elle utilise la redirection d'entrée "here document". Voici comment cela fonctionne :

- cat: C'est la commande Unix standard pour afficher le contenu d'un fichier.
  
- <<EOF: Ceci commence un "here document". Tout ce qui suit (jusqu'à ce qu'on atteigne un autre EOF) sera considéré comme du contenu d'entrée pour la commande cat. EOF est un délimiteur courant, mais en théorie, n'importe quel mot pourrait être utilisé.

- > Dockerfile: Ceci est une redirection. Au lieu d'afficher le contenu sur la console, il redirige (écrit) le contenu dans le fichier nommé Dockerfile. Si Dockerfile n'existe pas, il sera créé. Si Dockerfile existe déjà, son contenu sera écrasé.

En mettant tout cela ensemble, tout ce qui est écrit entre cat <<EOF et le EOF final sera placé dans le fichier Dockerfile.

Voici un exemple simple pour illustrer cela :

cat <<EOF > myfile.txt
Hello, World!
This is a text file.
EOF

Après avoir exécuté cette commande, vous aurez un fichier nommé myfile.txt contenant :

Hello, World!
This is a text file.

L'utilisation de "here documents" est très pratique pour intégrer du contenu directement dans des scripts sans avoir à créer des fichiers séparément.


# Étape 2: Créer le fichier index.html
cat <<EOF > index.html
<h1>Hello world.. running in apache2</h1>
EOF

# Étape 3: Construire l'image Docker
docker build -t myapache:1.0 .

# Étape 4: Exécuter le conteneur
docker run --name c1 -d -p 81:80 myapache:1.0
Testez http://localhost:81
docker stop c1
docker rm c1

On fait une copie .tar de notre container

docker save myapache:1.0 -o myapache:1.0.tar
ls -lh

Supprimer notre image de base
docker rmi myapache:1.0

Vérifier que l’image est supprimée 
docker images
ls (on peut voir le tar)

Création d’une image à partir du tar
docker load -i myapache:1.0.tar

Vérifier que l’image est crée
docker images 

Tagger l’image avec un nouveau tag
docker tag myapache:1.0 hrehouma1/myapache:1.0 
(remplacez la partie en orange par votre identifiant)
docker images

docker images : Lorsque nous utilisons cette commande, nous pouvons voir 2 images du même docker, mais en réalité "hrehouma1/myapache" n'est qu'une étiquette de l'image docker "myapache", elle fonctionne comme un raccourci.



Maintenant on se connecte à docker hub

docker login

sinon docker logout et ensuite docker login est rendu obligatoire avant d’effectuer des pull des images (avant c’était uniquement obligatoire pour le push). Pour ce faire, nous devons d'abord avoir un compte sur https://hub.docker.com (si vous n'en avez pas, créez-en un). 

docker push hrehouma1/myapache:1.0

Changer le tag pour qu’il corresponde à votre username sur le hub.docker (OBLIGATOIRE!). Allez sur le hub et vous verrez votre image ajoutée au docker hub




























RÉSUMÉ DES COMMANDES


docker ps
docker ps -a
docker rm c1 myapache
docker ps -a
docker images
docker save myapache:1.0 -o myapache1.0.tar
docker save myapache:1.0 -o myapache1.0.tar
ls -l
ls -lh
docker images
docker rmi myapache:1.0
docker images
ls
docker load -i myapache1.0.tar
docker images
docker push myapache:1.0
docker push hrehouma1/myapache:1.0
docker images
docker tag myapache:1.0 hrehouma1/myapache:1.0
docker images
docker push hrehouma1/myapache:1.0
docker login
docker push hrehouma1/myapache:1.0
history | tail -20
history | tail -40 




























Manipulations # 2 - Docker networking

Supprimer les conteneurs
docker ps
docker ps -a
docker ps -a -q
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

Supprimer les images
docker images
docker images -q
docker rmi $(docker images -q)

ifconfig et interfaces virtuelles

Installer les outils de networking
apt install net-tools
ifconfig

 


docker run -d --name c1 nginx
ifconfig

Nous pouvons voir qu’il y a une interface virtuelle qui vient d’être créée. 
Créons un autre container !

 


docker ps
docker run -d --name c2 nginx
ifconfig 

 

Lister les networks
Exécutez cette commande pour voir les docker networks

docker network ls 

 
Inspecter les networks

Inspectons les networks et vérifions les 2 containers à l’intérieur du network 

docker inspect bridge 

 

Entrons dans le premier container 

docker exec -it c1 bash
apt update
apt install iputils-ping


Copions l’adresse ip du deuxième container

 

Pinguer depuis le premier container
ping 172.17.0.3

Quitter le container
exit

Exécutez cette commande pour voir les networks
docker network ls

Exécutez cette commande pour créer un nouveau network (subnet)
docker network --help
docker network create mynet --subnet 192.168.80.0/26
docker network ls
Créer un nouveau container sur le nouveau network
docker run -d --name c3 --network mynet nginx

Lister les conteneurs
docker ps

Exécuter cette commande pour inspecter le bridge network
docker network inspect bridge

*IMPORTANT * 
Vous pouvez vérifier qu’il y a uniquement 2 conteneurs et que le 3ème n’est pas là !
 

*IMPORTANT * 
Exécutez cette commande pour inspecter le nouveau network créé
docker network inspect mynet
 
Exécuter cette commande pour créer un nouveau conteneur dans le nouveau network
docker run -d --name c4 --network mynet nginx

Inspectez encore le nouveau network mynet
docker network inspect mynet

Nous pouvons voir les 3ème et 4ème conteneurs

 
Déconnectez le conteneur du network
docker network disconnect mynet c3
Inspectez encore le nouveau network et remarquez que le 3ème conteneur a été déconnecté du network
docker network inspect mynet
 
Il est passé ou ? Inspectez dans le bridge network ! Il n’est pas ici aussi
docker network inspect bridge
 
Il n’est pas dans le “none” network aussi
docker network inspect none
 

Pour vérifier que le conteneur existe, il suffit d'exécuter docker ps. On le voit. Il existe mais il ne dispose pas de ports pour se connecter à aucun network.
docker ps
On peut observer que la partie dans la colonne PORTS est vide !
Connectons le conteneur c3 encore une fois au bridge network
docker network connect bridge c3
Avec cette commande docker ps, on peut voir que le conteneur est maintenant connecté à un network
 
Exécutez cette commande :  docker network inspect bridge
Nous pouvons voir que le conteneur est maintenant ajouté au network bridge.

*IMPORTANT - EXERCICE !!!! 

Suivez cet exemple pour: 
→ Premièrement: se connecter au conteneur c1 et faire un ping vers le conteneur c4. Nous pouvons constater que cela n'a pas réussi. 
→ Deuxièmement: nous avons fait un ping vers le conteneur c3. Nous pouvons voir que nous avons obtenu une réponse. C'est parce que les deux conteneurs sont dans le même réseau, contrairement à c4 qui est dans un réseau différent (nous pouvons en conclure qu'il n'est pas possible d'établir une connexion entre deux conteneurs se trouvant dans des réseaux différents).

Manipulations # 3 - Docker compose

Supprimer les conteneurs

docker ps
docker ps -a
docker ps -a -q
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

Supprimer les images

docker images
docker images -q
docker rmi $(docker images -q)


THÉORIE 1 

Docker Compose est un outil de Docker qui permet de définir et de gérer des applications multi-conteneurs avec Docker. Avec Docker Compose, vous pouvez utiliser un fichier YAML pour définir les services, les réseaux et les volumes nécessaires pour votre application. Ensuite, avec une seule commande (docker-compose up), vous pouvez démarrer tous les services définis.

Voici une explication plus détaillée de Docker Compose :

Docker Compose est un outil pour définir et gérer des applications Docker multi-conteneurs. Il vous permet de :

1. Définir : Avec un fichier docker-compose.yml, vous pouvez définir les différents conteneurs, les volumes et les réseaux dont votre application a besoin.

2. Gérer : Avec des commandes simples (docker-compose up, docker-compose down, etc.), vous pouvez démarrer, arrêter et reconstruire tous les services définis dans votre fichier de configuration.

3. Automatiser : Docker Compose vous donne la possibilité de déployer et de scaler facilement plusieurs conteneurs qui fonctionnent ensemble.

En somme, Docker Compose facilite la gestion de configurations complexes où plusieurs conteneurs doivent interagir. Au lieu de gérer chaque conteneur individuellement, Docker Compose permet de gérer et de configurer l'ensemble de l'application comme une unité.


THÉORIE 2

Docker Compose
Pour résumer, Compose est un outil pour définir et exécuter des applications Docker multi-conteneurs. Avec Compose, vous utilisez un fichier YAML pour configurer les services de votre application. Ensuite, avec une seule commande, vous créez et démarrez tous les services à partir de votre configuration.

Utiliser Compose est essentiellement un processus en trois étapes :

1. Définissez l'environnement de votre application avec un Dockerfile afin qu'il puisse être reproduit n'importe où.
2. Définissez les services qui composent votre application dans docker-compose.yml afin qu'ils puissent être exécutés ensemble dans un environnement isolé.
3. Exécutez docker-compose up et Compose démarre et exécute votre application entière.

Installation le fichier Docker Compose

⇨	Méthode 1 
apt install docker-compose

⇨	Méthode 2 (pour avoir une version spécifique de docker)
sudo curl -L https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
 
chmod +x /usr/local/bin/docker-compose
 
Vérifiez la version de votre docker-compose en exécutant la commande suivante : 
docker-compose --version 
docker-compose -version.
docker-compose -v 
 

⇨	Méthode 3 (dernière version)


# DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
# mkdir -p $DOCKER_CONFIG/cli-plugins
# curl -SL 
#https://github.com/docker/compose/releases/download/v2.19.0/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
# docker compose version

Ou tout simplement : 

# apt install docker-compose
# docker-compose -v
# docker-compose -version

Exercice 1 docker-compose - (P1) Docker-compose avec WordPress et MySQL

Exemple
touch docker-compose.yaml && nano docker-compose.yaml 
on peut l’appeler docker-compose.yaml ou docker-compose.yml ou compose.yaml ou compose.yml

version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
       WORDPRESS_DB_NAME: wordpress
volumes:
    db_data: {}



# docker-compose up -d

vérifier http://localhost:8000

# docker-compose down

ATTENTION :  docker-compose down supprime les conteneurs.
PREUVE et DÉMO : 
Manipulation 1 : faites docker-compose up mais ne faites pas docker-compose down. Vous allez cependant faire CTRL + C ensuite docker ps -a (les conteneurs sont stoppés mais pas supprimés. À n’importe quel moment, je peux effectuer un docker start etc…). C’est l’équivalent d’exécuter docker stop id. 

Manipulation 2 : faites docker-compose up, ensuite docker-compose down, ensuite docker ps -a . Les conteneurs n’existent pas , ils sont supprimés . C’est comme on a exécuté docker rm id.


Exercice 2 docker-compose - (P2) Docker-compose avec WordPress et MySQL 


Dans cet exercice, nous allons mettre en pause le service db (base de données)
docker ps
docker pause 123f
Vérifier http://localhost:8000
docker stop 123f
vérifier http://localhost:8000

 

docker start 123f

 

docker-compose down











Exercice 3 docker-compose – (P3) Docker-compose avec python
Rappel sur Docker Compose en anglais (optionnelle)
Théoriquement - Pourquoi le docker-compose?

 

Pratiquement - pourquoi le docker-compose ?
3-1. Premier scénario – sans docker compose 
Supprimer 
docker-compose down
docker ps
docker ps -a
docker images 
docker rmi -f $(docker images -q)

Supprimer les conteneurs
docker ps
docker ps -a
docker ps -a -q
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)

Supprimer les images
docker images
docker images -q
docker rmi $(docker images -q)


Exécutez cette commande : 
docker run -d --name redis redis
docker ps
docker exec redis bash
docker exec -it redis bash

 
Exécutez docker ps pour vérifier les conteneurs qui sont en cours de rouler.
docker ps

 
Pour vérifier les images, on exécute la commande
docker images

 
Suivre ces étapes : 
mkdir compose && cd compose
pwd
Allez à cette adresse : 
https://github.com/hrhouma/kubernetes1/tree/master/docker-compose et copier le tous (créer les fichiers avec touch et ensuite copier le contenu)
Ou 
sudo su –
pwd && mkdir projet1 && cd projet1 && git clone  https://github.com/hrhouma/kubernetes1.git 
cd /root/projet1/docker-demo/docker-compose
 
 
Copier le code de : https://github.com/hrhouma/kubernetes1/tree/master/docker-compose

⇨	Dockerfile  
⇨	myapp.py  
⇨	requirements.txt
Dockerfile  
 
myapp.py  
 
Requirements.txt
 

vi Dockerfile  
   

Supprimer les conteneurs
docker ps
docker ps -a
docker ps -a -q
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
Supprimer 
docker-compose down
docker ps
docker ps -a
docker images 
docker rmi -f $(docker images -q)

Premier essai 🖳 ERREUR , comprendre pourquoi docker-compose est important
docker build -t imageflask1:2.0 .
docker run -d --name redis redis
docker run -d --name containerflask -p 8080:8080 imageflask1:2.0
docker exec -it containerflask bash
curl localhost:8080 🡺 erreur

Deuxième essai (Solution : obligation d’utiliser la balise link)
docker stop $(docker ps -a -q) && docker rm $(docker ps -a -q) && docker rmi -f $(docker images -q)
docker build -t imageflask1:2.0 .
docker run -d --name redis redis
docker run -d --name containerflask1 --link redis:redis -p 8080:8080 imageflask1:2.0

docker exec -it containerflask1 bash
curl localhost:8080
 
Erreur 


Maintenant, si vous allez sur le navigateur et changez le port, vous verrez ce résultat. 
 
Chaque fois que vous actualisez, vous verrez le compteur augmenter.
 
docker stop containerflask1 redis
docker rm containerflask1 redis
docker rmi imageflask1:2.0  redis 

Exécutez cette commande, vous verrez les mêmes résultats.
 
RÉSUMÉ DES COMMANDES
 


3-2. Deuxième scénario – avec docker compose 
sudo su –
pwd
mkdir projet1 && cd projet1
clone https://github.com/hrhouma/kubernetes1.git
cd /root/projet1/docker-demo/docker-compose
cat docker-compose.yaml
docker-compose up 
🡺 Erreur pour moi puisque le port 80 à l’extérieur est utilisé (je vais changer au port 81)
nano docker-compose.yaml (je change 80 au port 81 à l’extérieur)
docker-compose up
docker-compose down
docker images
docker ps -a

 


docker-compose up -d
docker-compose ps
docker-compose scale redis=2
docker ps

 

docker exec -it 3bbc708 bash
curl localhost:8080
exit
curl localhost:81

 
 
 

docker ps 
docker stop $(docker ps -a -q) && docker rm $(docker ps -a -q)
docker rmi $(docker images -q)
ou docker rmi -f $(docker images -q) (pour forcer)








RÉSUMÉ DES COMMANDES
 


dd Manipulations # 0 - COMMENT SUPPRIMER DES CONTENEURS ET DES IMAGES
Manipulations # 1 - Tag, push, Save, load
Manipulations # 2 - Docker networking
Manipulations # 3 - Docker compose
Manipulations # 4 - Docker volume
Manipulations # 5 - Docker registry
Manipulations # 6 - docker swarm
Manipulations # 7 - Container orchestration, introduction à Kubernetes





































Manipulations # 4 - Docker volumes 

Vérifiez vos images Docker pour vous assurer qu'aucun conteneur en cours d'exécution

 docker ps -a
 
 docker ps -a -q
 
Assurez-vous qu'il n'y a pas de conteneur en cours d'exécution
 docker ps 
 
Vérifiez les images Docker
 docker images
 
Supprimez les images Docker

 docker rmi `docker images -q`
 
Vérifiez

 docker images
 
Supprimez les images Docker
 docker rmi -f `docker images -q`
 
Vérifiez
 docker images
 
Persistance des données (1/2)
Exécutez cette commande pour créer un conteneur de base de données
 docker run -d mysql
 
Exécutez cette commande
 docker ps
 





Exécutez cette commande
 docker logs kind_ardinghelli
 

Nous rencontrons cette erreur
 

Exécutez cette commande pour créer un conteneur de base de données
 docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=root mysql
 
-e c’est pour les variables d’environnement !
Nous pouvons voir qu'il a été créé
 docker ps -a
 

Exécutez cette commande
 docker exec -it mysql bash
 

Exécutez cette commande pour accéder au conteneur
docker exec -it mysql bash 
bash-4.4# env
 

Exécutez cette commande
bash-4.4# mysql -u root -p
 
Entrez le mot de passe qui est « root »

 
Maintenant, nous pouvons utiliser SQL
mysql> show databases;
 

Suivez ces étapes
mysql> use mysql;
 

Suivez ces étapes
CREATE TABLE Persons (
PersonID int,
LastName varchar(255),
FirstName varchar(255),
Address varchar(255),
City varchar(255)
);
 


Suivez ces étapes
insert into Persons values(1001, 'Sharam', 'Aakash', '22 high street', 'Mumbai');
 
Références SQL : https://www.tutorialspoint.com/sql/sql-insert-query.htm 
Suivez ces étapes
select * from Persons;
 

Suivez ces étapes 
insert into Persons values(1002, 'B', 'Alexandre', '22 high street', 'Mumbai');
 

Quitter
quit
 
Sortir
bash-4.4# exit 
 


Exécutez cette commande pour arrêter le conteneur (si nous relançons le conteneur, nous pouvons toujours trouver les informations de la base de données).
 docker stop mysql
 
Question 1  et évaluation formative 1 : 
Si vous redémarrez le conteneur après l’avoir arrêté (stop), est-ce que vous retrouverez les données ? Essayez les mêmes manipulations après avoir exécuté docker start mysql. OUI
Question 2  et évaluation formative 2 : 
Si vous démarrez un nouveau conteneur après avoir supprimé l’ancien (rm), est-ce que vous retrouverez les données ? Essayez les mêmes manipulations après avoir exécuté docker start mysql. NON
Démo et preuve:
Exécutez cette commande pour supprimer le conteneur
 docker rm mysql
 
Maintenant, si nous relançons le conteneur
 docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=root mysql
 
Accédez au conteneur
 docker exec -it mysql bash
 

Exécutez cette commande => entrez le mot de passe
bash-4.4# mysql -u root -p
 
Nous pouvons voir qu'il n'y a pas de données à l'intérieur de la base de données car nous avons supprimé le conteneur précédent
use mysql; 
 
La table mysql.Persons n’existe pas !!!! 
Comment faire afin de persister les données ? ==­> Partie 2/2
Quitter SQL => sortir du conteneur
quit
 
Ce sont toutes les commandes utilisées
 

Persistance des données (2/2)
1- Étape (1/4) - vérification de l’emplacement des données dans un conteneur mysql
Exécutez cette commande
docker ps
docker exec -it mysql bash 
bash-4.4# mysql -u root -p 
Suivez ces étapes
use mysql
 
Suivez ces étapes
CREATE TABLE Persons (
PersonID int,
LastName varchar(255),
FirstName varchar (255),
Address varchar(255),
City varchar(255)
);
 
Suivez ces étapes
insert into Persons values(1001, 'Sharam', 'Aakash', '22 high street', 'Mumbai');
insert into Persons values(1002, 'B', 'Alexandre', '22 high street', 'Mumbai');

 
Suivez ces étapes
select * from Persons;
 
Quitter la base de données
quit
 
Si nous allons dans ce répertoire, nous pouvons voir où les données sont enregistrées
bash-4.4# cd /var/lib/mysql
bash-4.4# ls
 bash-4.4# ls -ltrh
 
RÉPONSE de l’étape ⅓ : les données sont stockées dans /var/lib/mysql

Sortie
bash-4.4# exit
 
Arrêtez la base de données
 docker stop mysql
mysql
 
Supprimer la base de données
 docker rm mysql 
mysql
 
2- Étape (2/4) - création d’un conteneur avec l’option -v /opt/data:/var/lib/mysql
Vous comprenez que /opt/data est l’emplacement ou je stocke les données à l'extérieur , tandis que  /var/lib/mysql est l’emplacement ou je stocke les données de la bd à l’intérieur du conteneur mysql. Par contre, vous n’êtes pas obligés de stocker les données à l'extérieur à l'emplacement /opt/data/

utilisez cette commande pour créer un docker avec un volume qui y est attaché
 docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=root -v /opt/data:/var/lib/mysql mysql


exécutez cette commande
 cd /opt/data/
 
Vous pouvez voir les données (le dossier mysql)
/opt/data/# ls
 
Revenir à ce répertoire
/opt/data/# cd - /home/ubuntu/docker-demo ou cd --
 

DÉBUT PARTIE OPTIONNEL (QUESTION DE PRATIQUER ENCORE !)  : 
Je vais faire les manipulations de base et ensuite supprimer le conteneur mysql qui stocke les données à l'extérieur dans /opt/data. En effet, je veux stocker les données à l'extérieur dans le dossier /opt/mysqldata . Si vous désirez, vous pouvez vous rendre directement à la fin de la partie optionnelle.
Arrêter le conteneur
 docker stop mysql
 
Supprimer le conteneur
 docker rm mysql
 
FIN PARTIE OPTIONNEL  

Maintenant, exécutez à nouveau cette commande mais avec ce chemin
 docker run -d --name mysql -e  MYSQL_ROOT_PASSWORD=root -v /opt/mysqldata:/var/lib/mysql mysql
ou même 
docker run -d --name mysql -e  MYSQL_ROOT_PASSWORD=root -v /opt/haythemdata:/var/lib/mysql mysql


Nous allons vérifier où les données vont être placées à l'extérieur !!!  VÉRIFIONS !!!!
 cd/opt/mysqldata/
 
Vérifiez si vous avez la table Persons avec ls ==­> PAS DE TABLES Persons 
Exécutez cette commande
docker exec -it mysql bash
 
Connectez-vous à la base de données
bash-4.4# mysql -u root -p
 

Suivez les prochaines étapes
mysql> use mysql
 

CREATE TABLE Persons (
PersonID int,
LastName varchar(255),
FirstName varchar (255),
Address varchar(255),
City varchar(255)
);

insert into Persons values(1001, 'Sharam', 'Aakash', '22 high street', 'Mumbai');
insert into Persons values(1002, 'B', 'Alexandre', '22 high street', 'Mumbai');
select * from Persons;
 
select * from Persons;
 
Quitter
 
Sortie
exit
 
Maintenant, arrêtez le conteneur puis retirez-le
docker ps
docker stop mysql
docker rm mysql

 
Question 3  et évaluation formative 3 : 
Après avoir supprimé le conteneur mysql, les données sont-elles encore dans le dossier /opt/mysqldata? OUI

Ici, nous pouvons voir que les données existent toujours même après la suppression du conteneur grâce au volume que nous avons créé précédemment.
root@ip-172-31-34-187:/opt/mysqldata# ls (c’est le dossier mysql)
 
Question 4  et évaluation formative 4 : 
Après avoir supprimé le conteneur mysql, si je crée un nouveau conteneur mysql qui pointe sur le même dossier à l'extérieur /opt/mysqldata, puisse retrouver la table Persons à l'intérieur ? Le nouveau conteneur chargera-t-il les données depuis /opt/mysqldata? OUI
Alors maintenant, si nous créons un conteneur dans ce chemin spécifique, « var/lib/mysql » aura accès aux mêmes données
root@ip-172-31-34-187:/opt/mysqldata# 
docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=root -v /opt/mysqldata:/var/lib/mysql mysql 
Exécutez cette commande
root@ip-172-31-34-187:/opt/mysqldata# docker exec -it mysql bash
 
Entrez la base de données
bash-4.4# mysql -u root -p
 
Suivez ce processus
use mysql;
 
select * from Persons;
 
quit
 
bash-4.4# exit
 
Pour vérifier l’emplacement de la bd Persons.ibd, nous pouvons procéder de la façon suivante :
A l'extérieur 
cd /opt/mysqldata
cd mysql 
ls
A l'intérieur
cd /var/lib/mysql
cd mysql
ls

Ce sont les commandes utilisées

 

Question 5  et évaluation formative 5 : 
Est-ce possible d’avoir plusieurs volumes à l'extérieur pointant sur des volumes à l'intérieur du conteneur ?  OUI
Réponse : 

docker run -d \
--name mysql \
-e MYSQL_ROOT_PASSWORD=root \
-v /opt/data:/var/lib/mysql \
-v /chemin/vers/autre/dossier:/chemin/dans/conteneur \
-v /chemin2/vers/autre/dossier:/chemin2/dans/conteneur \

mysql












3- Étape (3/4) - création d’un conteneur mongodb
Nous pouvons même exécuter un conteneur MongoDB
root@ip-172-31-34-187:/opt/mysqldata# docker run -d --name mongodb mongo
 
Entrez dans le conteneur
root@ip-172-31-34-187: ~# docker exec -it mongodb bash
 
Exécutez cette commande
/# mongosh
 
/# exit
 
Nous pouvons voir notre conteneur créé 
 docker ps -a

Supprimer ce conteneur
root@ip-172-31-34-187: ~# docker rm kind_ardinghelli
 
docker stop mysql mongodb
docker rm mysql mongodb

 

4- Étape (4/4) - création d’un conteneur avec 2 volumes détachés

Maintenant, nous exécutons un conteneur avec 2 volumes attachés
root@ip-172-31-34-187:/opt/mysqldata# 

docker run -d \
--name mysql  \
-e MYSQL_ROOT_PASSWORD=root \
-v /opt/newdata:/var \
-v /opt/mysqldata:/var/lib/mysql 
mysql


Entrez dans le conteneur
docker exec -it mysql bash
 
Suivez ces commandes
bash-4.4# cd /var/
 

Suivez ces commandes
bash-4. 4# touch f1 f2 f3 
bash-4. 4#  ls
 
bash-4. 4# exit
 



Vérifiez maintenant à l'extérieur !!!!!
 
Ici, nous pouvons voir que même le nouveau fichier créé est joint à l'endroit où le nouveau volume a été créé.
cd /opt/newdata/
ls
 

Question 6  et évaluation formative 6 : 
Créons un fichier f4 à l'intérieur du conteneur au niveau de /var/lib/mysql, ou puis-je le retrouver à l'extérieur ? dans /opt/newdata ou dans /opt/mysqldata   ? Réponse Dans /opt/mysqldata

docker ps
docker exec -it mysql bash
cd /var/lib/mysql
touch f4
ls 
exit
cd /opt/mysqldata
ls











Manipulations # 5 - Docker registry ( A AMELIORER RAM)

Étape 1
Ici, nous avons créé un registre privé
docker run -d -p 5000:5000 --name priv_registry registry
 
registry c’est une image proposée par docker pour créer des registres privés. 
AWS propose ECR (payant).
AZURE propose ACR (payant).
Google propose GCR (payant).
Docker Inc. proposes registry (gratuit).
Étape 2
Nous pouvons le vérifier sur le navigateur
:5000/v2/_catalog
 
Étape 3
Nous marquons d’abord le conteneur mysql
docker tag mysql localhost:5000/mysql
 
Étape 4
Maintenant on pousse le conteneur tagué localhost:5000/mysql
docker push localhost:5000/mysql
 
Étape 5
Si nous actualisons le navigateur, nous pouvons voir que le conteneur est poussé
:5000/v2/_catalog
 

Question 7 et évaluation formative 7 : 
Trouvons les données à l'intérieur de votre conteneur priv_registry ? 
Réponse : 
docker exec priv_registry sh
cd /var/lib/registry/docker/registry/v2/repositories
ls

 
Capture d’écran en utilisant play with docker !
 

Toutes les commandes utilisées

 


Question 8 et évaluation formative 8 : 
https://github.com/hrhouma/docker-registry
https://github.com/hrhouma/docker-registry/blob/main/README.md 
https://github.com/hrhouma/docker-registry/blob/main/Docker%20Registry%20%231.md 


























