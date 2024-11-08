# 🎓 Chapitre 4 : Pratique avec les Variables, Facts et Registers dans Ansible

### Introduction

Dans cette pratique avancée, nous allons explorer les **variables**, les **facts**, et les **registers** dans Ansible, éléments qui permettent de créer des playbooks plus dynamiques et personnalisés. Vous apprendrez à définir des variables, à utiliser des facts pour récupérer des informations sur vos nœuds gérés, et à capturer les résultats des tâches avec des registers.

---

## 📂 Étape Préparatoire : Création du Dossier de Travail pour cette Pratique

1. **Créer un dossier pour le projet Ansible** :
   
   ```bash
   mkdir ansible_project_variables
   ```
   
   Ce dossier `ansible_project_variables` contiendra vos fichiers et playbooks Ansible.

2. **Naviguer dans ce dossier** :

   ```bash
   cd ansible_project_variables
   ```
   
   Assurez-vous de travailler dans ce dossier pour toutes les étapes suivantes.

---

## 🗂️ Étape Préparatoire : Configurer l'Inventaire

1. **Créer un fichier `inventory.ini`** :

   ```bash
   nano inventory.ini
   ```

2. **Ajouter les informations des nœuds dans `inventory.ini`** :

   Copiez le contenu suivant dans `inventory.ini` :

   ```ini
   [node_containers]
   node1 ansible_host=172.20.0.2 ansible_user=root
   node2 ansible_host=172.20.0.3 ansible_user=root
   node3 ansible_host=172.20.0.4 ansible_user=root
   node4 ansible_host=172.20.0.5 ansible_user=root
   node5 ansible_host=172.20.0.6 ansible_user=root
   node6 ansible_host=172.20.0.7 ansible_user=root
   ```

3. **Enregistrer et quitter l'éditeur** :
   - Appuyez sur `Ctrl + X`, puis `Y` et `Entrée` pour sauvegarder.

---

## Partie 1 : Utilisation des Variables dans un Playbook

### 📝 Étape 1 : Définir et Référencer une Variable

1. **Créer un fichier `variables-playbook.yml`** :

   ```bash
   nano variables-playbook.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Démonstration des variables
     hosts: node1
     vars:
       fav_color: "bleu"
     tasks:
       - name: Afficher la couleur préférée
         debug:
           msg: "Ma couleur préférée est {{ fav_color }}."
   ```

   **Explications** :
   - `vars` : Permet de définir des variables locales au playbook.
   - `debug` : Utilisé ici pour afficher la valeur de `fav_color` dans la sortie du playbook.

3. **Enregistrer et exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini variables-playbook.yml
   ```

   Vous devriez voir le message : "Ma couleur préférée est bleu."

---

### 📝 Étape 2 : Travailler avec des Listes et des Dictionnaires

1. **Mettre à jour le fichier `variables-playbook.yml`** pour ajouter des listes et des dictionnaires :

   ```yaml
   ---
   - name: Utilisation de listes et de dictionnaires
     hosts: node1
     vars:
       port_nums: [21, 22, 80, 443]
       users:
         bob:
           username: "bob"
           uid: 1001
           shell: "/bin/bash"
         alice:
           username: "alice"
           uid: 1002
           shell: "/bin/zsh"
     tasks:
       - name: Afficher le deuxième port
         debug:
           msg: "Le deuxième port est {{ port_nums[1] }}"

       - name: Afficher l'UID de Bob
         debug:
           msg: "L'UID de Bob est {{ users.bob.uid }}"
   ```

2. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini variables-playbook.yml
   ```

   **Explications** :
   - `port_nums[1]` : Accède au deuxième élément de la liste, ici `22`.
   - `users.bob.uid` : Accède à l’UID de l’utilisateur `bob` dans le dictionnaire `users`.

---

## Partie 2 : Utilisation des Fichiers de Variables Externes

1. **Créer un fichier de variables externe `myvars.yml`** :

   ```bash
   nano myvars.yml
   ```

2. **Ajouter des variables dans `myvars.yml`** :

   ```yaml
   ---
   port_nums: [21, 22, 80, 443]
   users:
     bob:
       username: "bob"
       uid: 1001
       shell: "/bin/bash"
     alice:
       username: "alice"
       uid: 1002
       shell: "/bin/zsh"
   ```

3. **Mettre à jour `variables-playbook.yml` pour inclure `myvars.yml`** :

   ```yaml
   ---
   - name: Utilisation de variables depuis un fichier externe
     hosts: node1
     vars_files:
       - myvars.yml
     tasks:
       - name: Afficher le premier port
         debug:
           msg: "Le premier port est {{ port_nums[0] }}"

       - name: Afficher le shell de Alice
         debug:
           msg: "Le shell de Alice est {{ users.alice.shell }}"
   ```

4. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini variables-playbook.yml
   ```

---

## Partie 3 : Utiliser les Facts Ansible

Les **facts** permettent de récupérer des informations dynamiques sur les nœuds, comme l'adresse IP ou le système d'exploitation.

1. **Créer un playbook `show-facts.yml`** pour afficher quelques facts :

   ```bash
   nano show-facts.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Afficher des facts sur le système
     hosts: node1
     tasks:
       - name: Afficher l'adresse IP par défaut
         debug:
           msg: "L'adresse IP par défaut est {{ ansible_facts['default_ipv4']['address'] }}"

       - name: Afficher la distribution OS
         debug:
           msg: "La distribution du système est {{ ansible_facts['distribution'] }}"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini show-facts.yml
   ```

   **Explications** :
   - `ansible_facts['default_ipv4']['address']` : Récupère l'adresse IP par défaut du nœud.
   - `ansible_facts['distribution']` : Affiche la distribution Linux du nœud.

---

## Partie 4 : Capturer des Résultats avec les Registers

Les **registers** permettent de stocker les résultats d'une tâche pour les utiliser plus tard.

1. **Créer un fichier `register-playbook.yml`** :

   ```bash
   nano register-playbook.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Utiliser les registers pour capturer la sortie d'une commande
     hosts: node1
     tasks:
       - name: Exécuter la commande uptime
         command: uptime
         register: server_uptime

       - name: Afficher le résultat de uptime
         debug:
           msg: "L'uptime du serveur est : {{ server_uptime.stdout }}"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini register-playbook.yml
   ```

   **Explications** :
   - `register: server_uptime` : Stocke le résultat de la commande `uptime` dans `server_uptime`.
   - `server_uptime.stdout` : Contient la sortie standard de la commande `uptime`.
