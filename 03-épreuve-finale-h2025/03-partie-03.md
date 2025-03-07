# Examen Pratique : Déploiement d’une Infrastructure Docker avec Ansible  

**Objectif** : Déployer une infrastructure Docker, automatiser l’installation d’Apache avec Ansible et diagnostiquer des erreurs système.  

**Votre mission** : Suivez les étapes, exécutez les commandes et **analysez les erreurs** pour les résoudre.  

- **Durée** : 2h30  
- **Évaluation** : Installation réussie, déploiement d’Apache fonctionnel, diagnostic et résolution des erreurs.  

---

# Partie 1 : Installation de Docker et Docker Compose  

- **But** : Installer Docker et Docker Compose sur une machine Ubuntu 22.04 Desktop (Famille : Debian, Nom : Jammy Jellyfish, Édition : Desktop, pas Server).
- **Instructions** :  

### **💬 Question 1 :**  
Installez Docker et Docker-Compose. Donnez les commandes.  
...........................................................................................................................  

Exécutez la commande suivante pour vérifier l’installation de Docker :  

```bash
docker --version
```

📌 **💡 Exemple d'Output attendu** :  
```bash
Docker version 24.0.6, build ed223bc
```

### **💬 Question 2 :**  
Fournissez une capture d’écran prouvant que Docker est bien installé.  
...........................................................................................................................  

### **💬 Question 3 :**  
Fournissez une capture d’écran prouvant que Docker Compose est bien installé.  
...........................................................................................................................  

### **💬 Question 4 :**  
Pourquoi Docker est-il essentiel dans un environnement automatisé ?  
...........................................................................................................................  

---

## Partie 2 : Création et Démarrage des Conteneurs  

- **But** : Créer une infrastructure avec **deux conteneurs Ubuntu**.  
- **Instructions** :

1️⃣ Créez un dossier de travail :  
```bash
mkdir examen && cd examen
nano docker-compose.yml
```

2️⃣ Copiez-collez le contenu suivant :  

```yaml
version: '3'

services:
  node1:
    image: ubuntu:latest
    container_name: node1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.2
    command: /bin/bash -c "apt update && apt install -y openssh-server python3 && service ssh start && tail -f /dev/null"
    expose:
      - "22"

  node2:
    image: ubuntu:latest
    container_name: node2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.3
    command: /bin/bash -c "apt update && apt install -y openssh-server python3 && nc -l -p 80 & nc -l -p 443 & service ssh start && tail -f /dev/null"
    expose:
      - "22"
      - "80"
      - "443"

networks:
  ansible_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
```

3️⃣ **Démarrer les conteneurs** :  
```bash
docker-compose up -d
docker ps
```

📌 **💡 Output attendu** :  
```bash
CONTAINER ID   IMAGE           STATUS         PORTS                 NAMES
1a2b3c4d5e6f   ubuntu:latest   Up X minutes  22/tcp                 node1
6f5e4d3c2b1a   ubuntu:latest   Up X minutes  22/tcp, 80/tcp, 443/tcp node2
```

### **💬 Question 5 :**  
Analysez la sortie de `docker ps`. Tous les conteneurs sont-ils **"Up"** ? Y a-t-il des erreurs ?  
...........................................................................................................................  

### **💬 Question 6 :**  
Si un ou plusieurs conteneurs ne fonctionnent pas, proposez **les commandes nécessaires** pour diagnostiquer le problème et expliquez-les de manière concise, en mettant l’accent sur leur utilité dans l’analyse et la résolution du dysfonctionnement.

...........................................................................................................................  

### **💬 Question 7 :**  
Si un ou plusieurs conteneurs ne fonctionnent pas, expliquez au moins **deux solutions** que vous proposez pour résoudre le problème. Donnez les commandes associées et expliquez-les de manière concise, en mettant l’accent sur leur utilité dans l’analyse et la résolution du dysfonctionnement.

**Solution 1**  
...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  

**Solution 2**  
...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  

---

## Partie 3 : Configuration de l’Accès SSH pour Ansible  

- **But** : Configurer l’accès SSH pour permettre à Ansible de gérer les conteneurs.

1️⃣ Générez une clé SSH :  
```bash
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa
```

2️⃣ Copiez la clé publique dans chaque conteneur :  
```bash
for i in {1..2}; do
  docker exec -it node$i mkdir -p /root/.ssh
  docker cp ~/.ssh/id_rsa.pub node$i:/root/.ssh/authorized_keys
  docker exec -it node$i chmod 600 /root/.ssh/authorized_keys
done
```

3️⃣ Vérifiez la connexion SSH :  
```bash
for i in {1..2}; do
  IP=172.20.0.$((i+1))
  ssh -o StrictHostKeyChecking=no root@$IP exit
done
```

📌 **💡 Output attendu** :  
Aucune erreur, la commande **ne doit pas** afficher de messages d’erreur.

### **💬 Question 8 :**  
Que faire si la connexion SSH est refusée malgré la clé ajoutée ?  
Par exemple, si j’ai cette erreur :  

```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
```

Que faire ? Donnez toutes les commandes clés pour résoudre le problème et expliquez-les de manière concise, en mettant l’accent sur leur utilité dans l’analyse et la résolution du dysfonctionnement.
...........................................................................................................................  

### **💬 Question 9 :**  
Donnez toutes les commandes si je désire supprimer toutes les clés publiques et privées de ma machine master.                    
...........................................................................................................................  

### **💬 Question 10 :**  
Donnez toutes les commandes si je désire supprimer toute la liste des `authorized_keys`. Dois-je les supprimer du master ou des conteneurs (workers) ?  
...........................................................................................................................  

### **💬 Question 11 :**  
Donnez toutes les commandes si je désire supprimer toute la liste des `known_hosts`. Dois-je les supprimer du master ou des conteneurs (workers) ?  
...........................................................................................................................  

### **💬 Question 12 :**  
Donnez la commande pour ajouter une clé publique à authorized_keys. C'est quoi l'utilité d'ajouter une clé publique à la liste d'authorized_key ?
...........................................................................................................................  


### **💬 Question 13 :**  
Donnez la commande pour afficher et vérifier les hôtes connus (known_hosts) . C'est quoi l'utilité ?
...........................................................................................................................  

### **💬 Question 14 :**  
Donnez la commande pour supprimer un hôte connu si l’empreinte change. C'est quoi l'utilité ?
...........................................................................................................................  



### **💬 Question 15 :**  

Finalement, c'est quoi la cause la plus probable de cette erreure dans le contexte de notre travail ? Comparez avec votre réponse initiale de la question 8 !
Expliquez vos commandes de manière concise, en mettant l’accent sur leur utilité dans l’analyse et la résolution du dysfonctionnement.

```bash
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
```

...........................................................................................................................  




## Partie 4 : Exécuter et Corriger des Playbooks Ansible 



1️⃣ Créez un fichier `configure_apache.yml` :  
```bash
nano configure_apache.yml
```

2️⃣ Ajoutez le contenu suivant (avec une erreur) :  

```yaml
- name: Install Apache on Ubuntu servers
  hosts: node_containers
  become: yes
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Start Apache
      service:
        name: httpd 
        state: started
```



### **💬 Question 16 :**  

Écrivez le fichier d’inventaire. À noter que `node_containers` contient tous les nœuds. Appelez les deux nœuds workers `node1` et `node2` respectivement  
...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  





3️⃣ Exécutez le playbook :  
```bash
ansible-playbook -i inventory.ini configure_apache.yml
```



📌 **💡 Output (Sortie désirée)
```bash
TASK [Install Apache] ********************************************************************
ok: [node1]
ok: [node2]

TASK [Start Apache] ********************************************************************
changed: [node1]
changed: [node2]
```





### **💬 Question 17:**  
Y a-t-il des erreurs ? Si oui, proposez les commandes nécessaires pour diagnostiquer le problème. Ensuite, proposez une solution pour obtenir la sortie attendue ci-dessus  
...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  





### **💬 Question 18 :**  
Exécutez un playbook pour **ajouter plusieurs utilisateurs** à l'aide d'une boucle sur une liste de dictionnaires.

#### Étape 1 : Créer un fichier `add-users.yml`  
```bash
nano add-users.yml
```

#### Étape 2 : Ajouter le contenu suivant :  
```yaml
---
- name: Ajouter Plusieurs Utilisateurs
  hosts: database
  become: yes
  vars:
    db_users:
      - username: "alice"
        password: "password1"
        uid: 1001
      - username: "bob"
        password: "password2"
        uid: 1002
      - username: "charlie"
        password: "password3"
        uid: 1003
  tasks:
    - name: Créer les Utilisateurs
      user:
        name: "{{ item.username }}"
        uid: "{{ item.uid }}"
        password: "{{ item.password | password_hash('sha512') }}"
        shell: "/bin/bash"
        state: present
      loop: "{{ db_users }}"
```

**💡 Explications :**  
- `db_users` : Liste de dictionnaires, contenant les informations des utilisateurs.  
- `user` : Module Ansible utilisé pour gérer les comptes utilisateurs.  
- `password_hash('sha512')` : Hash du mot de passe pour plus de sécurité.  
- `loop: "{{ db_users }}"` : Boucle sur chaque utilisateur défini dans `db_users`.  

#### Étape 3 : Enregistrez et quittez l’éditeur.

#### Étape 4 : Exécutez le playbook :  
```bash
ansible-playbook -i inventory.ini add-users.yml
```


### **💬 Question 18-1 :**  

Donnez la commande permettant de vérifier si les utilisateurs ont été créés.  

...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  

### **💬 Question 18-2 :**  

Y a-t-il des erreurs ? Si oui, analysez le message d'erreur et proposez les commandes nécessaires pour diagnostiquer le problème.  

...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  

### **💬 Question 18-3 :**  

En cas d'erreurs, corrigez le problème et modifiez le playbook pour utiliser une méthode alternative.  
Appliquez une **correction adaptée**, donnez le nouveau code corrigé et expliquez la correction appliquée.  

...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  



### **💬 Question 18-4 :**  

En cas d'erreurs, corrigez le problème et modifiez le playbook pour utiliser une méthode alternative, **à condition que le mot de passe reste sécurisé**.  
Appliquez une **correction adaptée**, donnez le nouveau code corrigé et expliquez la correction appliquée.  

...........................................................................................................................  
...........................................................................................................................  
...........................................................................................................................  







### **💬 Question 19 : Utilisation des Registres (`register`) et Actions Conditionnelles**  
Créez un **playbook Ansible** qui exécute une **commande système** sur les nœuds, enregistre la sortie dans un **registre (`register`)**, et déclenche une **action conditionnelle** basée sur cette sortie.

- Fournissez les **commandes nécessaires** pour exécuter le playbook que vous avez créé.  
- Exécutez votre playbook et **fournissez une capture d’écran** prouvant son exécution réussie.  

📌 **Livrables attendus :**  
1. **Code du playbook**  
2. **Commande d’exécution du playbook**  
3. **Capture d’écran prouvant la bonne exécution du playbook**  




*Je vous propose la liste de tâches à réaliser sous forme de pseudo-code :*

```plaintext
Définir un playbook Ansible qui :
- Exécute une commande système sur tous les nœuds
- Capture la sortie dans un registre (`register`)
- Vérifie si la sortie contient un mot-clé spécifique (ex : "apache")
- Si le mot-clé "apache" est présent :
    - Afficher un message indiquant qu’Apache est installé
    - Ne rien faire d’autre
- Si "apache" est absent :
    - Installer Apache automatiquement
    - Démarrer le service Apache
- Sauvegarder la sortie dans un fichier de log

Créer et exécuter le playbook
Analyser la sortie et expliquer l'exécution
```

```yaml
...........................................................
...........................................................
...........................................................
...........................................................
...........................................................
```


### **💬 Question 20 : Exploitation des Faits (`ansible_facts`)**  

- Créez un **playbook Ansible** qui récupère des **informations système (`facts`)**, les affiche et les **enregistre dans un fichier de log**.  
- Fournissez les **commandes nécessaires** pour exécuter le playbook que vous avez créé.  
- Exécutez votre playbook et **fournissez une capture d’écran** prouvant son exécution réussie.  

📌 **Livrables attendus :**  
1. **Code du playbook**  
2. **Commande d’exécution du playbook**  
3. **Capture d’écran prouvant la bonne exécution du playbook**  



*Je vous propose la liste de tâches à réaliser sous forme de pseudo-code :*

```plaintext
Définir un playbook Ansible qui :
- Récupère les `facts` des hôtes distants
- Vérifie et affiche :
    - La version du système d'exploitation
    - Le nombre de CPU
    - L'espace disque disponible
- Si la version d'OS est Ubuntu 22.04 :
    - Afficher un message de confirmation
- Si l'espace disque est inférieur à 10 Go :
    - Afficher un avertissement
    - Sauvegarder les facts dans un fichier JSON local
    - Envoyer une alerte dans un fichier de log

Créer et exécuter le playbook
Analyser la sortie et expliquer le fonctionnement
```

```yaml
...........................................................
...........................................................
...........................................................
...........................................................
...........................................................
```





