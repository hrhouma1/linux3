### **Devoir :**

Vous allez créer un conteneur avec Apache installé à l'intérieur et configurer une page web simple. Vous devez effectuer ce devoir avec **deux méthodes** :
1. **Sans Dockerfile** : En effectuant toutes les étapes manuellement à l'intérieur du conteneur.
2. **Avec Dockerfile** : En automatisant la configuration avec un fichier Dockerfile.

#### **Énoncé :**
1. Créez un conteneur à partir d'une image Ubuntu et nommez-le `c1`.
2. Connectez-vous à ce conteneur à l'aide de la commande `docker exec`.
3. Mettez à jour les paquets du conteneur avec la commande `apt update`.
4. Installez le serveur web Apache à l'intérieur du conteneur avec `apt install apache2`.
5. Sur votre machine hôte, créez un fichier `index.html` avec le contenu suivant :
   ```html
   <h1>Hello world.. running in apache2</h1>
   ```
6. Copiez ce fichier de votre machine hôte vers le conteneur à l'emplacement `/var/www/html/index.html`.
7. Vérifiez si vous pouvez accéder à cette page dans votre navigateur web en accédant à `http://localhost`.

---

### **Méthode 1 : Sans Dockerfile**

#### **Étapes détaillées :**

1. **Créer un conteneur Ubuntu** :
   - Exécutez la commande suivante pour créer et démarrer un conteneur nommé `c1` à partir de l'image `ubuntu` :
     ```bash
     docker run -d --name c1 -p 80:80 ubuntu
     ```
   - Cela crée un conteneur Ubuntu et expose le port 80 de votre machine hôte au port 80 du conteneur.

2. **Se connecter au conteneur** :
   - Utilisez la commande suivante pour ouvrir un terminal interactif dans le conteneur `c1` :
     ```bash
     docker exec -it c1 bash
     ```

3. **Mettre à jour les paquets** :
   - Une fois connecté au conteneur, exécutez la commande suivante pour mettre à jour la liste des paquets :
     ```bash
     apt update
     ```

4. **Installer Apache** :
   - Installez le serveur web Apache en exécutant la commande suivante :
     ```bash
     apt install apache2 -y
     ```

5. **Vérifier Apache** :
   - Après l'installation, démarrez Apache (si ce n'est pas déjà fait) et vérifiez qu'il est en cours d'exécution :
     ```bash
     service apache2 start
     service apache2 status
     ```

6. **Créer un fichier `index.html` sur la machine hôte** :
   - Sur votre machine hôte (hors du conteneur), créez un répertoire de travail et un fichier `index.html` :
     ```bash
     mkdir monsite
     cd monsite
     echo "<h1>Hello world.. running in apache2</h1>" > index.html
     ```

7. **Copier le fichier `index.html` vers le conteneur** :
   - Utilisez la commande `docker cp` pour copier le fichier HTML de votre hôte vers le conteneur à l'emplacement `/var/www/html/` :
     ```bash
     docker cp index.html c1:/var/www/html/index.html
     ```

8. **Vérifier l'accès à la page** :
   - Ouvrez un navigateur web et accédez à l'adresse `http://localhost`. Si tout fonctionne correctement, vous devriez voir la page avec le texte "Hello world.. running in apache2".

9. **Stopper et supprimer le conteneur (facultatif)** :
   - Après avoir terminé, vous pouvez arrêter et supprimer le conteneur avec les commandes suivantes :
     ```bash
     docker stop c1
     docker rm c1
     ```



---


### **Étape supplémentaire après la méthode 1 : Libérer les ports**

Après avoir terminé la **Méthode 1**, il est recommandé de **stopper et supprimer le conteneur** pour libérer les ports avant de passer à la **Méthode 2**.

#### **Libérer les ports** :
1. **Stopper le conteneur** `c1` :
   ```bash
   docker stop c1
   ```

2. **Supprimer le conteneur** `c1` :
   ```bash
   docker rm c1
   ```

3. **Vérifier que le port 80 est libéré** en listant les conteneurs actifs :
   ```bash
   docker ps
   ```


---

**Rappel** : Les ports doivent toujours être libérés pour permettre de réutiliser le même port dans d'autres conteneurs, sinon Docker renverra une erreur de conflit de ports si le même port est déjà utilisé.
---

### **Méthode 2 : Avec Dockerfile**

#### **Explication et Étapes détaillées :**

Dans cette méthode, vous allez automatiser la création du conteneur avec Apache et le fichier HTML en utilisant un Dockerfile.

1. **Créer un fichier Dockerfile** :
   - Dans le répertoire de travail `monsite`, créez un fichier nommé `Dockerfile` :
     ```bash
     touch Dockerfile
     ```

2. **Contenu du Dockerfile** :
   - Ouvrez le fichier avec un éditeur de texte et ajoutez le contenu suivant :
     ```dockerfile
     # Utiliser l'image Ubuntu comme base
     FROM ubuntu

     # Mettre à jour les paquets et installer Apache
     RUN apt update && apt install -y apache2 curl

     # Copier le fichier index.html depuis l'hôte vers le conteneur
     COPY index.html /var/www/html/index.html

     # Exposer le port 80 pour le conteneur
     EXPOSE 80

     # Démarrer Apache en premier plan pour que le conteneur continue à tourner
     CMD ["apachectl", "-D", "FOREGROUND"]
     ```

3. **Créer un fichier `index.html`** :
   - Toujours dans le répertoire `monsite`, créez le fichier `index.html` comme suit :
     ```bash
     echo "<h1>Hello world.. running in apache2</h1>" > index.html
     ```

4. **Construire l'image Docker** :
   - Utilisez la commande suivante pour créer une image à partir du Dockerfile. Nommez l'image `apache_image` :
     ```bash
     docker build -t apache_image .
     ```

5. **Lancer un conteneur à partir de l'image** :
   - Une fois l'image construite, lancez un conteneur basé sur cette image :
     ```bash
     docker run -d --name c1 -p 80:80 apache_image
     ```

6. **Vérifier l'accès à la page** :
   - Comme dans la méthode précédente, ouvrez un navigateur et accédez à `http://localhost`. La page avec le texte "Hello world.. running in apache2" devrait apparaître.

7. **Vérifier l'image créée** :
   - Vous pouvez vérifier que l'image `apache_image` a bien été créée en listant les images Docker disponibles :
     ```bash
     docker images
     ```

8. **Stopper et supprimer le conteneur et l'image (facultatif)** :
   - Si vous avez fini avec le conteneur, vous pouvez le stopper et le supprimer :
     ```bash
     docker stop c1
     docker rm c1
     docker rmi apache_image
     ```

---

### **Résumé des différences entre les deux méthodes :**

1. **Méthode sans Dockerfile** : Vous réalisez manuellement toutes les étapes à l'intérieur du conteneur (installation, configuration). Cette méthode peut être utile pour la découverte des commandes Docker et la manipulation des conteneurs, mais elle manque d'automatisation.

2. **Méthode avec Dockerfile** : Vous automatisez la création et la configuration du conteneur en écrivant toutes les instructions dans un Dockerfile. Cette méthode est plus efficace, reproductible, et utilisée couramment dans des environnements de production ou de développement.

