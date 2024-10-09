----------------------------
# 1 - Objectif : 
----------------------------
- *comprendre le  concept d’avoir un processus qui tourne  dans un conteneur Docker :*

----------------------------
# 2 - Exercices : 
----------------------------

### Exercice 1 : Lancer un processus simple dans un conteneur
1. Télécharge l’image `alpine` (un système Linux minimal) en utilisant la commande :
   ```bash
   docker pull alpine
   ```
2. Lance un conteneur avec cette image et un processus qui affiche un message puis reste actif pendant 30 secondes :
   ```bash
   docker run alpine sh -c "echo 'Bonjour depuis Alpine' && sleep 30"
   ```
3. Pendant que le processus est en cours, vérifie que le conteneur est actif avec `docker ps`.

**Question : Pourquoi `docker ps` montre que le conteneur est actif uniquement pendant 30 secondes ?**

---

### Exercice 2 : Garder un processus actif dans le conteneur
1. Lance un nouveau conteneur avec `alpine` mais cette fois-ci, laisse le processus `sleep` actif pour 1 minute :
   ```bash
   docker run alpine sh -c "sleep 60"
   ```
2. Pendant que le conteneur tourne, exécute la commande suivante pour observer son état :
   ```bash
   docker ps
   ```
3. Vérifie si le conteneur est toujours actif après 1 minute en utilisant `docker ps -a`.

**Question : Qu’est-ce qui se passe après que le temps imparti est terminé ?**

---

### Exercice 3 : Lancer un conteneur interactif
1. Lance un conteneur en mode interactif avec l’image `ubuntu` et démarre un shell bash :
   ```bash
   docker run -it ubuntu bash
   ```
2. Une fois dans le conteneur, tape des commandes comme `ls`, `pwd`, ou `echo "Bonjour Docker"` pour interagir avec le système.
3. Tape `exit` pour quitter le conteneur.

**Question : Quelle est la différence entre un conteneur interactif et un conteneur qui se ferme automatiquement après l'exécution d'une commande ?**

---

### Exercice 4 : Exécuter un serveur web basique
1. Crée un fichier Dockerfile simple qui va installer un serveur web basique (par exemple avec `python`) et qui garde le processus du serveur actif :
   ```dockerfile
   FROM python:3.9-slim
   CMD ["python3", "-m", "http.server", "8080"]
   ```
2. Construis et lance l’image :
   ```bash
   docker build -t mon-serveur-web .
   docker run -p 8080:8080 mon-serveur-web
   ```
3. Ouvre ton navigateur et accède à `http://localhost:8080` pour voir si le serveur tourne.

**Question : Pourquoi le conteneur reste actif tant que le serveur est en cours d'exécution ?**

---

### Exercice 5 : Superviser un processus avec `docker exec`
1. Lance un conteneur qui exécute `sleep 1000` pour le garder actif pendant un certain temps :
   ```bash
   docker run -d alpine sleep 1000
   ```
2. Utilise `docker exec` pour te connecter à ce conteneur et lancer une commande à l’intérieur sans interrompre le processus `sleep` :
   ```bash
   docker exec -it <ID_conteneur> sh
   ```
3. À l'intérieur du conteneur, exécute la commande `echo "Processus en cours"`. 

**Question : Quelle est l’utilité de `docker exec` dans la gestion des processus à l'intérieur d'un conteneur déjà en cours d’exécution ?**



------------
# 3 - Corrections
------------


### **Exercice 1 : Lancer un processus simple dans un conteneur**

1. **Télécharger l’image `alpine` :**

   ```bash
   docker pull alpine
   ```

   Cette commande télécharge une image de base `alpine`, un système d'exploitation Linux très léger.

2. **Lancer un conteneur avec un message et un délai de 30 secondes :**

   ```bash
   docker run alpine sh -c "echo 'Bonjour depuis Alpine' && sleep 30"
   ```

   Cette commande crée et lance un conteneur à partir de l'image `alpine`. Elle exécute deux commandes : `echo`, qui affiche un message, suivi de `sleep 30`, qui garde le conteneur actif pendant 30 secondes avant qu'il ne se termine.

3. **Vérifier que le conteneur est actif :**

   Pendant ces 30 secondes, utilise la commande suivante pour voir le conteneur actif :

   ```bash
   docker ps
   ```

**Question : Pourquoi `docker ps` montre que le conteneur est actif uniquement pendant 30 secondes ?**

**Réponse :** Le conteneur exécute un processus unique (ici `sh -c "echo ... && sleep 30"`). Une fois que le processus se termine (après les 30 secondes du `sleep`), le conteneur se ferme automatiquement. Docker arrête un conteneur dès que son processus principal se termine.

---

### **Exercice 2 : Garder un processus actif dans le conteneur**

1. **Lancer un conteneur avec un processus actif pendant 1 minute :**

   ```bash
   docker run alpine sh -c "sleep 60"
   ```

   Cette commande garde le conteneur actif pendant 60 secondes en exécutant la commande `sleep 60`.

2. **Vérifier l'état du conteneur pendant qu'il est actif :**

   Pendant ces 60 secondes, tu peux vérifier que le conteneur est en cours d'exécution avec :

   ```bash
   docker ps
   ```

3. **Vérifier l’état du conteneur après la fin du processus :**

   Après une minute, le conteneur devrait être stoppé. Vérifie son état avec :

   ```bash
   docker ps -a
   ```

**Question : Qu’est-ce qui se passe après que le temps imparti est terminé ?**

**Réponse :** Une fois que le processus `sleep 60` est terminé, Docker arrête automatiquement le conteneur, car il n'y a plus de processus en cours d'exécution à l'intérieur. Le conteneur passe alors à l'état "exited".

---

### **Exercice 3 : Lancer un conteneur interactif**

1. **Lancer un conteneur interactif avec `ubuntu` et accéder à un shell bash :**

   ```bash
   docker run -it ubuntu bash
   ```

   Le drapeau `-it` permet d'ouvrir le conteneur en mode interactif. Cela lance une session `bash` où tu peux taper des commandes comme si tu étais directement dans un terminal sur une machine Ubuntu.

2. **Interagir avec le conteneur :**

   Une fois dans le conteneur, exécute les commandes suivantes pour explorer l'environnement :

   ```bash
   ls
   pwd
   echo "Bonjour Docker"
   ```

3. **Quitter le conteneur :**

   Tape `exit` pour quitter le conteneur.

**Question : Quelle est la différence entre un conteneur interactif et un conteneur qui se ferme automatiquement après l'exécution d'une commande ?**

**Réponse :** Un conteneur interactif (avec `-it`) reste ouvert tant que l'utilisateur ne ferme pas manuellement la session en tapant `exit`. En revanche, un conteneur non interactif se ferme automatiquement dès que la commande principale (comme `sleep` ou `echo`) est terminée.

---

### **Exercice 4 : Exécuter un serveur web basique**

1. **Créer un Dockerfile pour un serveur web avec `python` :**

   Le fichier `Dockerfile` contient les instructions nécessaires pour construire une image Docker.

   ```dockerfile
   FROM python:3.9-slim
   CMD ["python3", "-m", "http.server", "8080"]
   ```

   Ce Dockerfile utilise l'image `python:3.9-slim` et exécute un serveur web basique sur le port 8080.

2. **Construire et lancer l’image :**

   - Construis l'image :

     ```bash
     docker build -t mon-serveur-web .
     ```

   - Lance le conteneur en mappant le port 8080 de l’hôte au conteneur :

     ```bash
     docker run -p 8080:8080 mon-serveur-web
     ```

3. **Vérifier le serveur web :**

   Ouvre un navigateur et accède à `http://localhost:8080`. Si tout fonctionne, tu devrais voir un répertoire listé par le serveur web Python.

**Question : Pourquoi le conteneur reste actif tant que le serveur est en cours d'exécution ?**

**Réponse :** Le conteneur reste actif car le serveur web Python (`python3 -m http.server`) est un processus qui tourne en continu pour répondre aux requêtes. Tant que ce processus est en cours d’exécution, Docker garde le conteneur actif.

---

### **Exercice 5 : Superviser un processus avec `docker exec`**

1. **Lancer un conteneur `alpine` avec un processus `sleep` actif :**

   ```bash
   docker run -d alpine sleep 1000
   ```

   Le conteneur est lancé en arrière-plan (`-d`), et le processus `sleep 1000` le garde actif pendant 1000 secondes.

2. **Se connecter au conteneur avec `docker exec` :**

   Utilise `docker exec` pour te connecter à un conteneur en cours d’exécution sans interrompre son processus principal (`sleep 1000`).

   ```bash
   docker exec -it <ID_conteneur> sh
   ```

3. **Exécuter une commande à l’intérieur du conteneur :**

   À l'intérieur du conteneur, exécute cette commande :

   ```bash
   echo "Processus en cours"
   ```

   Cette commande imprime le message sans interrompre le processus `sleep` en cours.

**Question : Quelle est l’utilité de `docker exec` dans la gestion des processus à l'intérieur d'un conteneur déjà en cours d’exécution ?**

**Réponse :** La commande `docker exec` permet d'exécuter des commandes dans un conteneur déjà en cours d’exécution sans perturber le processus principal. C’est très utile pour diagnostiquer ou manipuler un conteneur en temps réel (par exemple, pour vérifier l'état du système, installer des outils supplémentaires ou manipuler des fichiers).








--------------
# 4 - Annexe 1 : Comprendre l'état "Exited" d'un conteneur Docker
--------------

1. **Lancer un conteneur sans processus actif :**
   - Exécute la commande suivante pour lancer un conteneur basé sur l'image `ubuntu` :
     ```bash
     docker run ubuntu
     ```
   - Immédiatement après, tape la commande suivante pour vérifier l’état des conteneurs actifs :
     ```bash
     docker ps
     ```
   - Ensuite, utilise `docker ps -a` pour voir la liste de tous les conteneurs, y compris ceux qui se sont arrêtés :
     ```bash
     docker ps -a
     ```

2. **Observer l’état "Exited" :**
   - Tu vas remarquer que le conteneur `ubuntu` a un statut "Exited" après seulement quelques secondes.

3. **Explication :**
   - Quand tu lances `docker run ubuntu` sans spécifier de commande à exécuter, Docker démarre un conteneur basé sur l’image `ubuntu`, mais par défaut, il n'y a aucun processus à exécuter dans ce conteneur. Une fois le conteneur démarré, il se termine immédiatement car il n'a pas de commande en cours d'exécution à l'intérieur.
   - Docker attend toujours une commande pour exécuter un processus. Si aucune commande n’est fournie, le conteneur se ferme dès qu'il a terminé le lancement de l'environnement Ubuntu.

**Question : Pourquoi un conteneur se ferme-t-il automatiquement s'il n'y a pas de processus à exécuter à l'intérieur ?**

---

### Explication Complémentaire pour l'Exercice :
- **Pourquoi le conteneur est en statut "Exited" ?**
  - Docker fonctionne en lançant un processus à l'intérieur du conteneur. Une fois que ce processus se termine, le conteneur s’arrête également. Dans le cas où tu exécutes simplement `docker run ubuntu` sans spécifier un processus, le conteneur démarre, mais comme il n’y a pas de commande à exécuter, le conteneur se termine automatiquement.
  
- **Solution pour garder le conteneur actif :**
  - Pour que le conteneur reste actif, il faut lui donner un processus à exécuter. Par exemple, tu peux lancer un conteneur `ubuntu` avec un shell interactif qui reste ouvert tant que tu n'as pas quitté le shell :
    ```bash
    docker run -it ubuntu bash
    ```

# Conclusion :

- *Ce premier exercice a pour objecif de comprendre pourquoi un conteneur peut avoir un état "Exited" immédiatement après son lancement, et comment nous pouvons nous assurer qu'un conteneur reste actif en lançant un processus à l'intérieur.*
- Pour éviter que le conteneur ne se termine immédiatement après son lancement, il faut lui fournir un processus à exécuter. Par exemple :

1. **Utiliser un shell interactif** :
   - Avec `ubuntu`, tu peux lancer un shell interactif qui restera ouvert tant que tu n'as pas quitté le terminal :
     ```bash
     docker run -it ubuntu bash
     ```
   Dans ce cas, le processus `bash` reste actif, ce qui maintient le conteneur en cours d’exécution.

2. **Exécuter une commande qui garde le conteneur actif** :
   - Tu peux aussi exécuter un conteneur avec l'image `alpine` et utiliser la commande `sleep` pour garder le conteneur actif pendant un certain temps :
     ```bash
     docker run alpine sh -c "sleep 60"
     ```
   Ici, le processus `sleep 60` garde le conteneur actif pendant 60 secondes, car il attend avant de se terminer.

**L'essentiel** : Un conteneur Docker reste en cours d'exécution aussi longtemps qu'un processus tourne à l'intérieur. Si aucun processus n'est lancé ou si le processus se termine, le conteneur passe à l'état "Exited".




