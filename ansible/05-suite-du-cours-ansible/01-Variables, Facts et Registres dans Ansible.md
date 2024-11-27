# Chapitre 4 : Variables, Facts et Registres dans Ansible

Ce chapitre explore en détail l'utilisation des **variables**, des **facts** et des **registres** dans Ansible. Voici une traduction complète et détaillée.


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

### 2.5. Variables par hôte et groupe

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
