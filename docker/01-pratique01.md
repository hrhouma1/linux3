
# Pratique 01 🚀 : Gestion de conteneurs Docker

### Exercice 1 : Lancer un conteneur
**Objectif :** Lancer un conteneur simple.

1. Lancer un conteneur avec l'image `nginx`.
2. Afficher la liste des conteneurs en cours d'exécution.

```bash
docker run -d --name mon_nginx nginx
docker ps
```
3. Proposez une amélioration des commandes ci-dessus

### Exercice 2 : Afficher les images Docker
**Objectif :** Comprendre la gestion des images Docker.

1. Télécharger l'image `alpine`.
2. Afficher la liste des images disponibles.

```bash
docker pull alpine
docker images
```

### Exercice 3 : Supprimer un conteneur
**Objectif :** Supprimer un conteneur après l'avoir arrêté.

1. Lancer un conteneur `hello-world`.
2. Arrêter ce conteneur.
3. Supprimer le conteneur.

```bash
docker run --name mon_hello hello-world
docker ps
docker ps -a
docker stop mon_hello
docker rm mon_hello
docker ps
docker ps -a
```

### Exercice 4 : Créer un conteneur interactif
**Objectif :** Accéder à un conteneur en mode interactif.

1. Lancer un conteneur `ubuntu` en mode interactif.
2. Lister les fichiers à l'intérieur du conteneur.

```bash
docker run -it ubuntu
cat /etc/os-release
ls
```

3. Ouvrir un autre terminal (terminal 2) et exécutez la commande suivante. Qu'observez-vous? 

```bash
docker ps
```

4. Revenir au premier terminal(terminal 1) et exécutez la commande suivante.

```bash
exit
```

5. Revenir au terminal #2 et exécutez la commande suivante. Qu'observez-vous? 

```bash
docker ps
```

6. Qu'observez-vous dans les résultats ? Pourquoi le résultat est-il différent de celui attendu dans les étapes 3. et 5.?



7. Comparez les commandes *sans* et *avec* l'option -d

```bash
docker run -it ubuntu
ls
exit
docker ps
docker run -d -it ubuntu
docker ps
```

### Exercice 5 : Supprimer toutes les images
**Objectif :** Comprendre la suppression des images.

1. Lancer un conteneur avec l'image `busybox`.
2. Supprimer toutes les images Docker du système.

```bash
docker run --name mon_busybox busybox
docker rmi $(docker images -q)
```
3. Si vous avez des images utilisées par des conteneurs, docker refusera de les supprimer


```bash
docker ps
docker ps -a
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
docker ps
docker ps -a
docker images
docker rmi $(docker images -q)
docker images

```
