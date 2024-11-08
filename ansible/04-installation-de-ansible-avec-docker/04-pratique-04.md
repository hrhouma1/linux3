# 🎓 Chapitre 4 : Pratique avancée avec les playbooks Ansible

### Introduction

Dans cette pratique, vous allez apprendre à automatiser des tâches sur vos conteneurs Docker en utilisant **Ansible** avec des playbooks. Nous allons commencer par des tâches simples avant de passer à des opérations plus avancées. Assurez-vous d’avoir bien configuré Docker, Docker-Compose et Ansible avant de débuter.

---

## 📂 Étape Préparatoire : Créer un dossier de travail pour ce projet

1. **Créer un dossier pour le projet Ansible** :
   
   ```bash
   mkdir ansible_project
   ```
   
   Cela crée un dossier nommé `ansible_project` dans votre répertoire actuel. Ce dossier servira de conteneur pour tous vos fichiers et playbooks Ansible.

2. **Naviguer dans le dossier nouvellement créé** :

   ```bash
   cd ansible_project
   ```
   
   Cette commande vous place dans le dossier `ansible_project` où vous allez effectuer toutes les prochaines étapes.

---

## 🗂️ Étape Préparatoire : Configurer l'inventaire

L'inventaire Ansible est un fichier essentiel pour définir les nœuds gérés par Ansible. Il est utilisé pour spécifier les adresses IP et d'autres détails des machines distantes. 

1. **Créer un fichier `inventory.ini`** :
   
   ```bash
   nano inventory.ini
   ```
   
   Cette commande ouvre un éditeur de texte dans le terminal, où vous pourrez ajouter les informations de chaque nœud.

2. **Ajouter les informations des nœuds dans `inventory.ini`** :

   Copiez et collez le texte suivant dans `inventory.ini` :

   ```ini
   [node_containers]
   node1 ansible_host=172.20.0.2 ansible_user=root
   node2 ansible_host=172.20.0.3 ansible_user=root
   node3 ansible_host=172.20.0.4 ansible_user=root
   node4 ansible_host=172.20.0.5 ansible_user=root
   node5 ansible_host=172.20.0.6 ansible_user=root
   node6 ansible_host=172.20.0.7 ansible_user=root

   [web]
   node1
   node5

   [database]
   node2
   node3

   [mail]
   node4
   node6

   [ungrouped]
   node3
   ```

   **Explications** :
   - **[node_containers]** : Groupe principal contenant tous les conteneurs.
   - `ansible_host` : Spécifie l'adresse IP de chaque nœud.
   - `ansible_user` : Définit l’utilisateur pour l’accès SSH, ici `root` pour tous les nœuds.
   - **[web]**, **[database]**, **[mail]**, **[ungrouped]** : Groupes permettant de structurer les nœuds selon leur fonction.

3. **Enregistrer et quitter l'éditeur** :
   - Appuyez sur `Ctrl + X`, puis `Y` et `Entrée`.

### 🌐 Vérification de l’inventaire et test de la connectivité

1. **Lister les hôtes d'un groupe spécifique** :

   ```bash
   ansible web -i inventory.ini --list-hosts
   ```

2. **Lister tous les hôtes dans l’inventaire** :

   ```bash
   ansible all -i inventory.ini --list-hosts
   ```

3. **Tester la connectivité avec une commande `ping`** :

   ```bash
   ansible all -m ping -i inventory.ini
   ```

4. **Vérifier un nœud individuel (exemple avec node1)** :

   ```bash
   ansible node1 -i inventory.ini --list-hosts
   ```

5. **Exécuter une commande sur un nœud (par exemple, obtenir la date sur node1)** :

   ```bash
   ansible node1 -m command -a "date" -i inventory.ini
   ```

Ces commandes permettent de vérifier que l'inventaire est configuré et que tous les nœuds sont accessibles pour l'exécution des playbooks.

---

## Partie 1 : Créer et exécuter votre premier playbook

### 📝 Étape 1 : Écrire un playbook de base pour la création de fichiers

1. **Créer un fichier nommé `first-playbook.yml`** :
   
   ```bash
   nano first-playbook.yml
   ```
   
2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Premier Playbook de création de fichier
     hosts: all
     become: yes
     tasks:
       - name: Créer un fichier de configuration
         file:
           path: /tmp/foo.conf
           mode: '0664'
           owner: root
           state: touch
   ```

   **Explications** :
   - `hosts: all` : S'exécute sur tous les hôtes.
   - `become: yes` : Permet l'exécution avec des privilèges root.
   - **tasks** : Liste de tâches. Ici, la tâche crée un fichier `/tmp/foo.conf` avec les permissions `0664` et l’utilisateur `root`.

3. **Enregistrer et quitter l'éditeur** :
   - Appuyez sur `Ctrl + X`, puis `Y` et `Entrée`.

### 🏃‍♂️ Exécution du playbook

1. **Lancer le playbook** :

   ```bash
   ansible-playbook -i inventory.ini first-playbook.yml
   ```

2. **Vérifier que le fichier a bien été créé** :

   ```bash
   ansible all -m command -a "ls -l /tmp/foo.conf" -i inventory.ini
   ```

---

## Partie 2 : Créer un playbook multi-tâches

### 📝 Étape 1 : Écrire un playbook avec plusieurs tâches

1. **Créer un fichier nommé `multi-tasks-playbook.yml`** :
   
   ```bash
   nano multi-tasks-playbook.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Installation et archivage des logs
     hosts: all
     become: yes
     tasks:
       - name: Installer tmux sur tous les nœuds
         package:
           name: tmux
           state: present

       - name: Créer une archive des logs système
         archive:
           path: /var/log
           dest: /tmp/logs.tar.gz
           format: gz

   - name: Installation de Git sur les nœuds Ubuntu
     hosts: node1,node5,node6
     become: yes
     tasks:
       - name: Installer Git
         apt:
           name: git
           state: present
   ```

3. **Enregistrer et quitter l'éditeur**.

### 🏃‍♂️ Exécution du playbook multi-tâches

1. **Lancer le playbook** :

   ```bash
   ansible-playbook -i inventory.ini multi-tasks-playbook.yml
   ```

2. **Vérifier que l’archive a bien été créée** :

   ```bash
   ansible all -m command -a "file -s /tmp/logs.tar.gz" -i inventory.ini
   ```

---

## Partie 3 : Réutiliser des tâches via l’importation

### 📝 Étape 1 : Créer un fichier de tâches réutilisables

1. **Créer un fichier nommé `group-tasks.yml`** :

   ```bash
   nano group-tasks.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Créer le groupe developpeurs
     group:
       name: developpeurs

   - name: Créer le groupe securite
     group:
       name: securite

   - name: Créer le groupe finance
     group:
       name: finance
   ```

3. **Enregistrer et quitter l'éditeur**.

### 📝 Étape 2 : Importer les tâches dans un playbook principal

1. **Modifier `first-playbook.yml` pour inclure `group-tasks.yml`** :

   ```yaml
   ---
   - name: Premier Playbook de création de fichier et de groupes
     hosts: all
     become: yes
     tasks:
       - name: Créer un fichier de configuration
         file:
           path: /tmp/foo.conf
           mode: '0664'
           owner: root
           state: touch

       - name: Créer des groupes utilisateurs
         import_tasks: group-tasks.yml
   ```

2. **Lancer le playbook avec importation de tâches** :

   ```bash
   ansible-playbook -i inventory.ini first-playbook.yml
   ```

---

## Partie 4 : Utiliser les tags pour l’exécution sélective

1. **Taguer les tâches dans `multi-tasks-playbook.yml`** :

   ```yaml
   - name: Installer tmux sur tous les nœuds
     package:
       name: tmux
       state: present
     tags: install_tmux

   - name: Créer une archive des logs système


     archive:
       path: /var/log
       dest: /tmp/logs.tar.gz
       format: gz
     tags: archive_logs
   ```

2. **Exécuter des tâches spécifiques par tag** :

   - Pour installer uniquement `tmux` :

     ```bash
     ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags install_tmux
     ```

   - Pour créer l’archive uniquement :

     ```bash
     ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags archive_logs
     ```

