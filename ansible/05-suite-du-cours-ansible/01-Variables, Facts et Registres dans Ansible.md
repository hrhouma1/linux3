# Chapitre 4 : Variables, Facts et Registres dans Ansible

Ce chapitre explore en détail l'utilisation des **variables**, des **facts** et des **registres** dans Ansible. 


# Introduction

Ansible est un outil qui permet d'automatiser des tâches sur plusieurs serveurs (comme installer des logiciels ou configurer des systèmes). Pour rendre cette automatisation flexible et adaptée à chaque serveur, on utilise des **variables** (pour définir des paramètres personnalisés comme le nom d’un utilisateur ou une couleur préférée), des **facts** (qui sont des informations collectées automatiquement sur les serveurs, comme leur adresse IP ou leur système d’exploitation), et des **registres** (qui capturent la sortie des commandes exécutées, par exemple combien de mémoire est disponible sur un serveur). Ces outils rendent Ansible super puissant, car ils permettent d’adapter les tâches automatiquement en fonction de chaque serveur, sans tout écrire à la main à chaque fois. Imagine que tu veux cuisiner pour plusieurs personnes : les variables, facts et registres, c’est comme si tu savais combien de personnes viennent, ce qu’elles aiment manger et ce qu’il reste dans le frigo ; ça t’aide à cuisiner juste ce qu’il faut pour chacun !

# Partie 01 - À quoi ça sert ?

Ansible est un outil d'automatisation qui simplifie la gestion et la configuration de serveurs, même si vous en avez des dizaines ou des centaines. Il permet de s’assurer que tous vos serveurs reçoivent les mêmes instructions (comme installer un logiciel ou configurer un pare-feu) sans devoir les gérer manuellement un par un. Pour rendre cet outil encore plus puissant et adaptable, Ansible utilise **des variables**, **des facts** et **des registres**.

### 1.1. **Les Variables : quoi et pourquoi ?**
Les variables sont des valeurs que vous pouvez définir pour personnaliser ce qu’Ansible fait. Par exemple :
- Vous pourriez définir une variable pour dire que votre serveur web doit utiliser le port 8080, et un autre serveur, le port 9090.
- Les variables permettent d’écrire un seul script (appelé playbook) et de l’utiliser pour des serveurs différents, en modifiant uniquement les valeurs spécifiques.

**Exemple :**
Si vous gérez plusieurs serveurs, vous pourriez avoir une variable `server_role` pour dire si un serveur est un "proxy", un "webserver", ou un "database server". Ensuite, Ansible fait les choses adaptées à chaque rôle, automatiquement.

---

### 1.2. **Les Facts : comment connaître l’état de vos serveurs ?**
Les facts sont des informations qu’Ansible collecte automatiquement sur vos serveurs avant d’exécuter des tâches. Cela inclut des détails comme :
- L’adresse IP du serveur
- Son système d’exploitation (exemple : Ubuntu ou CentOS)
- Sa version matérielle ou logicielle.

Ces informations sont précieuses car elles permettent à Ansible de s'adapter automatiquement :
- Si Ansible découvre qu’un serveur utilise Ubuntu, il peut exécuter des commandes spécifiques à Ubuntu.
- Si le serveur a peu de mémoire, Ansible peut éviter d’y lancer des tâches gourmandes.

**Exemple :**
Imaginez que vous voulez afficher l’adresse IP de chaque serveur. Ansible utilise un fact appelé `ansible_facts.default_ipv4.address` pour le récupérer sans que vous ayez besoin de le connaître à l’avance.

---

### 1.3. **Les Registres : capturer les résultats pour agir dessus**
Quand vous exécutez une commande avec Ansible (comme vérifier combien de mémoire est libre), Ansible ne montre pas toujours la réponse immédiatement. Les **registres** permettent de capturer ces résultats pour les utiliser dans d'autres étapes.

**Exemple :**
Vous exécutez la commande `free -h` pour connaître la mémoire disponible. Avec un registre, vous pouvez capturer ce résultat et l’afficher ou même prendre des décisions : par exemple, si la mémoire libre est insuffisante, Ansible peut arrêter le déploiement d’une application gourmande.

---

### Pourquoi c’est utile ?
Sans variables, facts et registres, Ansible fonctionnerait comme un robot aveugle, exécutant la même chose partout, même si ce n’est pas adapté. Avec ces outils :
1. **Les variables** permettent d’écrire des scripts réutilisables et personnalisables.
2. **Les facts** donnent à Ansible des yeux pour "voir" et comprendre chaque serveur.
3. **Les registres** permettent de se souvenir des résultats de ce qu’il a fait pour agir en conséquence.

En résumé, ces concepts transforment Ansible en un outil intelligent et adaptable, capable de gérer vos serveurs comme un expert qui sait exactement quoi faire, où, et comment. Vous n'avez plus besoin de tout vérifier ou configurer vous-même : Ansible fait le travail pour vous, de façon précise et efficace.

---

# Partie 2

### 2.1. Travailler avec des variables

Les variables permettent de gérer les différences entre les systèmes que vous gérez avec Ansible. Les concepts abordés incluent la définition, l'accès, et l'organisation des variables dans les fichiers.

#### Définir et référencer des variables

- Vous pouvez définir des variables directement dans un playbook à l'aide du mot-clé `vars`.  
  Exemple :
  ```yaml
  ---
  - name: Travailler avec des variables
    hosts: all
    vars:
      fav_color: jaune
  ```

- Pour accéder à une variable, Ansible utilise le système de templates Jinja2. Entourez la variable avec des doubles accolades :  
  ```yaml
  Mon couleur préférée est {{ fav_color }}
  ```

#### Exemple pratique : Playbook avec une variable
Créez un fichier nommé `variables-playbook.yml` :
```yaml
---
- name: Travailler avec des variables
  hosts: node1
  vars:
    fav_color: jaune
  tasks:
    - name: Afficher la couleur préférée
      debug:
        msg: "Ma couleur préférée est {{ fav_color }}."
```

#### Exécuter le playbook
Commande :
```bash
ansible-playbook variables-playbook.yml
```

**Résultat attendu** :
```yaml
TASK [Afficher la couleur préférée] ***
ok: [node1] => {
    "msg": "Ma couleur préférée est jaune."
}
```

---

### 2.2. Créer des listes et des dictionnaires

Les variables peuvent contenir des **listes** ou des **dictionnaires** :

- **Liste** :  
  ```yaml
  vars:
    port_nums: [21, 22, 23, 80, 443]
  ```

- **Dictionnaire** :  
  ```yaml
  vars:
    users:
      bob:
        uid: 1001
        shell: /bin/bash
      alice:
        uid: 1002
        shell: /bin/sh
  ```

#### Accès aux éléments

- Liste :  
  ```yaml
  Première valeur : {{ port_nums[0] }}
  ```

- Dictionnaire :  
  ```yaml
  Shell de Bob : {{ users.bob.shell }}
  ```

---

### 2.3. Inclure des variables externes

Vous pouvez inclure des fichiers de variables externes :

- Exemple d'un fichier `myvars.yml` :
  ```yaml
  ---
  port_nums: [21, 22, 23, 80, 443]
  users:
    bob:
      uid: 1001
      shell: /bin/bash
  ```

- Inclusion dans un playbook :
  ```yaml
  vars_files:
    - myvars.yml
  ```

---

### 2.4. Demander une saisie utilisateur

Avec `vars_prompt`, vous pouvez demander à l'utilisateur de définir une variable pendant l'exécution.

Exemple :
```yaml
---
- name: Saluer l'utilisateur
  hosts: localhost
  vars_prompt:
    - name: username
      prompt: Quel est votre nom ?
      private: no
  tasks:
    - name: Dire bonjour
      debug:
        msg: "Bonjour {{ username }} !"
```

---

### 2.5. Variables par hôte et groupe (allez à l'annexe 01 svp )

#### Variables spécifiques aux hôtes
1. Créez un répertoire `host_vars`.
2. Ajoutez un fichier pour chaque hôte :
   ```bash
   echo "message: Je suis un serveur proxy" > host_vars/node1.yml
   ```

#### Variables par groupe
1. Créez un répertoire `group_vars`.
2. Ajoutez un fichier par groupe :
   ```bash
   echo "pkg: nginx" > group_vars/webservers.yml
   ```

---

### 2.6. Les Facts dans Ansible

Les **facts** sont des variables système collectées automatiquement par Ansible via le module `setup`. Exemple :

Commande :
```bash
ansible node1 -m setup
```

Exemple pour afficher des facts spécifiques :
```yaml
- name: Afficher les facts
  hosts: node1
  tasks:
    - name: Adresse IP
      debug:
        msg: "Adresse IP : {{ ansible_facts.default_ipv4.address }}"
    - name: OS
      debug:
        msg: "Système d'exploitation : {{ ansible_facts.distribution }}"
```

---

### 2.7. Créer des Facts personnalisés

#### Étapes :
1. Créez un fichier `.fact` :
   ```bash
   echo -e "[custom]\ncolor=blue\nmovie=Inception" > custom.fact
   ```
2. Copiez ce fichier sur les hôtes gérés dans `/etc/ansible/facts.d`.

Playbook pour automatiser cela :
```yaml
---
- name: Ajouter des faits personnalisés
  hosts: node1
  tasks:
    - name: Créer le répertoire facts.d
      file:
        path: /etc/ansible/facts.d
        state: directory
    - name: Copier le fichier de faits
      copy:
        src: custom.fact
        dest: /etc/ansible/facts.d/custom.fact
```

---

### 2.8. Capturer la sortie avec les Registres

Les **registres** stockent la sortie des commandes ou tâches. Exemple :

```yaml
---
- name: Capturer la sortie
  hosts: node1
  tasks:
    - name: Exécuter uptime
      command: uptime
      register: uptime_result
    - name: Afficher la sortie
      debug:
        msg: "Temps d'activité : {{ uptime_result.stdout }}"
```

---

### 2.9. Exercice

**Objectif** :
1. Exécuter la commande `free -h` et capturer la sortie.
2. Afficher l'adresse IPv4 de chaque hôte.

**Solution** :
```yaml
---
- name: Lab Chapitre 4
  hosts: all
  tasks:
    - name: Afficher la mémoire disponible
      command: free -h
      register: memory_output
    - name: Afficher la sortie mémoire
      debug:
        msg: "Mémoire : {{ memory_output.stdout }}"
    - name: Afficher l'adresse IPv4
      debug:
        msg: "Adresse IPv4 : {{ ansible_facts.default_ipv4.address }}"
```

-----------------
------------------
# Annexe 01 - Variables par Hôte et par Groupe dans Ansible : Explication

Lorsqu'on utilise Ansible, on travaille souvent avec plusieurs serveurs (appelés "hôtes"). Chaque serveur peut avoir des rôles différents ou des besoins spécifiques. Par exemple, un serveur peut être un **proxy**, un autre un **serveur web**, et un autre encore une **base de données**. Les **variables par hôte et par groupe** permettent de personnaliser ce que fait Ansible sur chaque serveur ou groupe de serveurs, sans réécrire plusieurs fois les mêmes commandes.

---

### **1. Variables Spécifiques aux Hôtes**

Les variables par hôte permettent d’attribuer des paramètres spécifiques à **un seul serveur**. Par exemple, si vous avez un serveur proxy nommé `node1`, vous pouvez lui assigner un message spécial ou une configuration qui lui est propre.

#### **Étapes :**
1. **Créer un répertoire pour les variables des hôtes :**
   Dans votre projet Ansible, créez un répertoire nommé `host_vars` :
   ```bash
   mkdir host_vars
   ```

2. **Créer un fichier pour chaque hôte :**
   Dans ce répertoire, créez un fichier avec le **nom exact** du serveur (par exemple, `node1.yml` pour un serveur nommé `node1`).
   ```bash
   echo "message: Je suis un serveur proxy" > host_vars/node1.yml
   ```

3. **Contenu du fichier :**
   - Le fichier `host_vars/node1.yml` contient des variables spécifiques à `node1`. Par exemple :
     ```yaml
     message: Je suis un serveur proxy
     ```

4. **Résultat attendu :**
   Lorsque vous exécutez un playbook, Ansible utilise ces variables uniquement pour `node1`. Par exemple, un playbook qui affiche la variable `message` affichera :
   ```text
   Je suis un serveur proxy
   ```

---

### **2. Variables Spécifiques aux Groupes**

Les variables par groupe permettent de définir des paramètres communs à **un groupe de serveurs**. Par exemple, tous les serveurs web peuvent nécessiter l’installation de **nginx**, tandis que les serveurs de base de données peuvent avoir besoin de **mariadb**.

#### **Étapes :**
1. **Créer un répertoire pour les variables des groupes :**
   Dans votre projet Ansible, créez un répertoire nommé `group_vars` :
   ```bash
   mkdir group_vars
   ```

2. **Créer un fichier pour chaque groupe :**
   Dans ce répertoire, créez un fichier avec le **nom exact** du groupe (par exemple, `webservers.yml` pour un groupe nommé `webservers`).
   ```bash
   echo "pkg: nginx" > group_vars/webservers.yml
   ```

3. **Contenu du fichier :**
   - Le fichier `group_vars/webservers.yml` contient des variables applicables à tous les serveurs du groupe `webservers`. Par exemple :
     ```yaml
     pkg: nginx
     ```

4. **Résultat attendu :**
   Lorsque vous exécutez un playbook, Ansible utilise cette variable pour **tous les serveurs du groupe `webservers`**. Par exemple, un playbook qui installe le paquet défini dans `pkg` installera automatiquement `nginx` sur tous les serveurs du groupe `webservers`.

---

### **Différence Entre Variables par Hôte et par Groupe**

| **Type**        | **Quand l’utiliser ?**                                                                                   | **Exemple**                           |
|------------------|---------------------------------------------------------------------------------------------------------|---------------------------------------|
| **Par hôte**     | Si une variable ne concerne qu’**un seul serveur**.                                                     | `node1.yml` avec `message: proxy`     |
| **Par groupe**   | Si une variable est partagée par **plusieurs serveurs d’un même type** (comme "serveurs web").          | `webservers.yml` avec `pkg: nginx`   |

---

### **Exemple Complet :**

Supposons que vous avez quatre serveurs :
- `node1` : serveur proxy
- `node2` et `node3` : serveurs web
- `node4` : serveur de base de données

#### Étape 1 : Créer les répertoires et fichiers

1. **Variables par hôte :**
   ```bash
   mkdir host_vars
   echo "message: Je suis un serveur proxy" > host_vars/node1.yml
   echo "message: Je suis un serveur de base de données" > host_vars/node4.yml
   ```

2. **Variables par groupe :**
   ```bash
   mkdir group_vars
   echo "pkg: nginx" > group_vars/webservers.yml
   echo "pkg: mariadb-server" > group_vars/dbservers.yml
   ```

#### Étape 2 : Exemple de Playbook

Voici un playbook qui utilise ces variables :
```yaml
---
- name: Configurer les serveurs
  hosts: all
  tasks:
    - name: Afficher le message spécifique à chaque hôte
      debug:
        msg: "{{ message }}"
    - name: Installer le paquet défini par le groupe
      yum:
        name: "{{ pkg }}"
        state: present
```

#### Étape 3 : Exécution

Lors de l’exécution, Ansible :
1. Affichera les messages définis par **host_vars** (`node1` dira qu’il est un proxy, `node4` dira qu’il est un serveur de base de données).
2. Installera les paquets définis par **group_vars** :
   - `nginx` pour les serveurs du groupe `webservers`.
   - `mariadb-server` pour les serveurs du groupe `dbservers`.

---

### **Conclusion**

- Les **variables par hôte** permettent de personnaliser des actions pour des serveurs spécifiques.
- Les **variables par groupe** simplifient la gestion de plusieurs serveurs ayant les mêmes besoins.
- En combinant ces deux approches, vous rendez vos playbooks flexibles et puissants.



-----------------------
-----------------------
# Annexe 02 -  mot-clé et une variable



### **Qu'est-ce qu'un mot-clé ?**
Un **mot-clé** est un terme spécifique qu'Ansible reconnaît pour effectuer une action ou définir une structure dans un fichier YAML. Il est "réservé" par Ansible, ce qui signifie que vous ne pouvez pas l'utiliser comme une variable ou pour autre chose.

#### **Exemples de mots-clés :**
- `hosts` : spécifie sur quels serveurs le playbook sera exécuté.
- `tasks` : liste les actions à effectuer.
- `vars` : déclare des variables spécifiques dans un playbook.
- `name` : donne un titre ou une description à une tâche ou un playbook.

---

### **Qu'est-ce qu'une variable ?**
Une **variable** est un conteneur qui stocke une valeur (texte, nombre, liste, etc.) que vous pouvez réutiliser dans votre playbook ou vos templates. Contrairement aux mots-clés, les noms de variables sont définis par l'utilisateur.

#### **Exemples de variables :**
- `pkg: nginx` : `pkg` est une variable qui contient la valeur `nginx`.
- `message: "Je suis un serveur proxy"` : `message` est une variable contenant un texte.

---

### **Différence entre Mots-clés et Variables**

| **Élément**      | **Mot-clé**                          | **Variable**                              |
|-------------------|--------------------------------------|-------------------------------------------|
| **Définition**    | Terme réservé par Ansible.           | Valeur définie par l'utilisateur.         |
| **Rôle**          | Structure ou action dans le playbook.| Conteneur pour stocker une valeur réutilisable. |
| **Exemples**      | `hosts`, `tasks`, `vars`, `name`     | `pkg`, `message`, `webserver_message`     |
| **Usage**         | Obligation de respecter leur syntaxe.| Peut être nommé selon les besoins, mais sans espace ni caractère spécial. |

---

### **Table ASCII pour les Exemples**

Voici une vue simplifiée pour comprendre l'interaction entre les mots-clés et les variables dans un playbook :

```plaintext
+-------------------+---------------------------+
|     MOT-CLÉ       |         VALEUR            |
+-------------------+---------------------------+
| hosts             | all                       |
| tasks             | - name: Installer Nginx   |
| vars              | pkg: nginx                |
| debug (afficher)  | msg: "{{ message }}"      |
| template (fichier)| src: index.j2             |
| name              | Créer index.html          |
+-------------------+---------------------------+
```

---

### **Exemple Concret :**

```yaml
---
- name: Exemple avec mots-clés et variables
  hosts: webservers        # MOT-CLÉ : sur quels hôtes appliquer
  vars:                    # MOT-CLÉ : déclaration des variables
    pkg: nginx             # VARIABLE : paquet à installer
    message: "Serveur Web prêt !"  # VARIABLE : message personnalisé

  tasks:                   # MOT-CLÉ : définir les actions
    - name: Installer un paquet
      yum:
        name: "{{ pkg }}"  # Utilisation de la VARIABLE
        state: present

    - name: Afficher un message
      debug:
        msg: "{{ message }}"  # Utilisation de la VARIABLE
```

#### Explication des éléments :
- **Mots-clés** :
  - `hosts` : spécifie les hôtes (ici `webservers`).
  - `vars` : annonce les variables utilisées dans le playbook.
  - `tasks` : définit la liste des tâches à exécuter.
- **Variables** :
  - `pkg` : stocke la valeur `nginx`.
  - `message` : stocke la valeur `"Serveur Web prêt !"`.

---

### **Récapitulatif :**

| **Concept**       | **Définition**                           | **Exemple**                               |
|--------------------|------------------------------------------|-------------------------------------------|
| **Mot-clé**        | Élément structurant du playbook.         | `tasks`, `vars`, `name`, `hosts`.         |
| **Variable**       | Contient une valeur réutilisable.        | `pkg: nginx`, `message: "Bonjour"`.       |

Les **mots-clés** sont essentiels pour structurer le playbook, tandis que les **variables** permettent de le rendre flexible et dynamique.


----------------------
----------------------
# Annexe 03 - glossaire simplifié et clair pour comprendre les variables par hôte et par groupe dans Ansible.

```plaintext
+-------------------+-------------------------------+-------------------------------------------+
| TYPE              | UTILISATION                  | EXEMPLE                                   |
+-------------------+-------------------------------+-------------------------------------------+
| **Variables par** | **Définir des paramètres     | Spécifiques à un seul hôte (serveur).     |
| **HÔTE**          | spécifiques à un serveur.    |                                           |
+-------------------+-------------------------------+-------------------------------------------+
| Exemple :         | - Créez le répertoire        | mkdir host_vars                           |
|                   | - Ajoutez un fichier :       | echo "message: Je suis un proxy" >        |
|                   |                               | host_vars/node1.yml                       |
+-------------------+-------------------------------+-------------------------------------------+
| Fichier           | Nom du fichier = Nom du hôte | host_vars/node1.yml                       |
|                   | Contenu :                    | message: Je suis un proxy                |
+-------------------+-------------------------------+-------------------------------------------+
| Résultat attendu  | Lors de l’exécution du       | Ansible affichera pour `node1` :          |
|                   | playbook, seules les         | "Je suis un proxy"                        |
|                   | variables de cet hôte        |                                           |
|                   | seront utilisées.            |                                           |
+-------------------+-------------------------------+-------------------------------------------+
| **Variables par** | **Définir des paramètres     | Partagées entre plusieurs serveurs (par   |
| **GROUPE**        | communs à un groupe d’hôtes. | exemple : serveurs web, base de données). |
+-------------------+-------------------------------+-------------------------------------------+
| Exemple :         | - Créez le répertoire        | mkdir group_vars                          |
|                   | - Ajoutez un fichier :       | echo "pkg: nginx" >                       |
|                   |                               | group_vars/webservers.yml                 |
+-------------------+-------------------------------+-------------------------------------------+
| Fichier           | Nom du fichier = Nom du      | group_vars/webservers.yml                 |
|                   | groupe (exemple : webservers)| Contenu : pkg: nginx                     |
+-------------------+-------------------------------+-------------------------------------------+
| Résultat attendu  | Tous les serveurs du groupe  | Ansible installera `nginx` sur les        |
|                   | utiliseront la même variable | serveurs du groupe `webservers`.          |
+-------------------+-------------------------------+-------------------------------------------+
| **Différences**   | **Par Hôte**                 | **Par Groupe**                            |
+-------------------+-------------------------------+-------------------------------------------+
| Utilisation       | Personnalisation spécifique  | Paramètres communs à plusieurs serveurs.  |
| Exemple           | node1.yml avec :             | webservers.yml avec :                     |
|                   | message: "Je suis un proxy"  | pkg: "nginx"                              |
+-------------------+-------------------------------+-------------------------------------------+
| Application       | Affecte uniquement un hôte   | Affecte tous les serveurs du groupe.      |
+-------------------+-------------------------------+-------------------------------------------+
```

---

### **Exemple d'Utilisation Complète**

#### **1. Structure des Répertoires**
```plaintext
host_vars/
  ├── node1.yml  (message: "Je suis un proxy")
  ├── node4.yml  (message: "Je suis une base de données")
group_vars/
  ├── webservers.yml  (pkg: "nginx")
  ├── dbservers.yml    (pkg: "mariadb-server")
```

#### **2. Playbook**
```yaml
---
- name: Configurer les serveurs avec variables par hôte et groupe
  hosts: all
  tasks:
    - name: Afficher le message spécifique à chaque hôte
      debug:
        msg: "{{ message }}"
    - name: Installer le paquet défini par le groupe
      yum:
        name: "{{ pkg }}"
        state: present
```

#### **3. Exécution**
```bash
ansible-playbook -i inventory.yml playbook.yml
```

#### **Résultat Attendu**
1. **Pour `node1`** :
   - Message : "Je suis un proxy"
   - Paquet installé : Aucun (pas dans `webservers` ou `dbservers`).

2. **Pour `node2` et `node3` (groupe `webservers`)** :
   - Message : Aucun message (pas dans `host_vars`).
   - Paquet installé : `nginx`.

3. **Pour `node4` (groupe `dbservers`)** :
   - Message : "Je suis une base de données".
   - Paquet installé : `mariadb-server`.

---

### **Résumé**

| **Concept**          | **Clé à Retenir**                                                        |
|-----------------------|-------------------------------------------------------------------------|
| Variables par HÔTE    | Personnalisation **unique** pour un serveur spécifique (fichier dédié). |
| Variables par GROUPE  | Paramètres **partagés** entre plusieurs serveurs d’un même type.        |

En combinant ces deux approches, vos playbooks deviennent plus puissants, flexibles et organisés.
