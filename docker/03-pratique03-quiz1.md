
# Objectif:  

*Ce quiz teste la compréhension de base sur l’utilisation de Docker et des options essentielles comme `-d` et `-it`.*

### **QUIZ 1 - Docker (100 points)**

**1. Qu'est-ce qu'un conteneur Docker ?** (10 points)  
*Choix unique*  
- Un processus isolé qui partage le noyau du système d'exploitation hôte.  
- Un système d'exploitation virtuel complet.

---

**2. Comment exécutez-vous une image Docker nommée "monapp" en mode détaché ?** (10 points)  
*Choix unique*  
- `docker start monapp`  
- `docker run -d monapp`

---

**3. Quelle commande Docker affiche toutes les images Docker disponibles sur votre machine ?** (10 points)  
*Choix unique*  
- `docker ps`  
- `docker images`

---

**4. Comment spécifiez-vous une version spécifique de l'image dans un Dockerfile ?** (10 points)  
*Choix unique*  
- En ajoutant le tag de version après le nom de l'image, comme dans `FROM nginx:1.14`.  
- En utilisant `VERSION 1.14` dans le Dockerfile.

---

**5. Que signifie l'option `-d` dans `docker run -d nginx` ?** (10 points)  
*Choix unique*  
- Exécute le conteneur en mode interactif.  
- Exécute le conteneur en arrière-plan (mode détaché).

---

**6. Que signifie l'option `-it` dans `docker run -it ubuntu bash` ?** (10 points)  
*Choix unique*  
- Exécute le conteneur en mode interactif avec un terminal attaché.  
- Exécute le conteneur en arrière-plan avec un terminal attaché.

---

**7. Que manque-t-il dans cette commande pour l'exécuter en mode interactif et attacher un terminal ? `docker run ubuntu`** (10 points)  
*Choix unique*  
- `-d`  
- `-it`  
- Les deux

---

**8. Si vous voulez exécuter un conteneur nginx en arrière-plan, quelle option devez-vous utiliser ?** (10 points)  
*Choix unique*  
- `docker run -it nginx`  
- `docker run -d nginx`

---

**9. Exemple pratique : Essayez la commande suivante et déterminez ce qui manque pour la rendre interactive :**  
`docker run ubuntu echo "Bonjour"`  
Quelle option faut-il ajouter pour que cette commande ouvre un shell interactif dans le conteneur ?  
*Choix unique*  
- `-d`  
- `-it`

---

**10. Après avoir exécuté `docker run ubuntu`, que montre la commande `docker ps` ?** (10 points)  
*Choix unique*  
- Elle affiche le conteneur Ubuntu qui vient d'être lancé.  
- Elle n'affiche aucun conteneur car le conteneur Ubuntu s'est arrêté.  
- Elle montre une liste de toutes les images Docker installées.  
- Elle affiche un message d'erreur.



