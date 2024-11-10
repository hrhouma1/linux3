# 🎓 Chapitre 3 : Pratique Avancée avec les Playbooks Ansible

Dans ce chapitre, nous allons approfondir notre utilisation d'Ansible en écrivant des playbooks plus complexes, en réutilisant des tâches, et en utilisant des fonctionnalités avancées comme les tags pour l'exécution sélective. Nous nous assurerons également que toutes les configurations sont correctes et que les playbooks fonctionnent sans erreur.

---

## 📋 Table des Matières

1. [Introduction](#introduction)
2. [Étape Préparatoire : Créer un Dossier de Travail](#etape-preparatoire)
3. [Configurer l'Inventaire Ansible](#configurer-inventaire)
4. [Vérifier l'Inventaire et la Connectivité](#verifier-inventaire)
5. [Partie 1 : Créer et Exécuter votre Premier Playbook](#partie1)
6. [Partie 2 : Créer un Playbook Multi-Tâches](#partie2)
7. [Partie 3 : Réutiliser des Tâches via l'Importation](#partie3)
8. [Partie 4 : Utiliser les Tags pour l'Exécution Sélective](#partie4)
9. [Conclusion](#conclusion)

---

<a name="introduction"></a>
## 📝 Introduction

Dans cette pratique, vous allez apprendre à automatiser des tâches sur vos conteneurs Docker en utilisant **Ansible** avec des playbooks. Nous commencerons par des tâches simples avant de passer à des opérations plus avancées. Assurez-vous d’avoir bien configuré Docker, Docker Compose et Ansible avant de débuter.

---

<a name="etape-preparatoire"></a>
## 📂 Étape Préparatoire : Créer un Dossier de Travail

1. **Créer un dossier pour le projet Ansible** :

   ```bash
   mkdir ansible_project
   ```

   Cela crée un dossier nommé `ansible_project` dans votre répertoire actuel. Ce dossier servira de conteneur pour tous vos fichiers et playbooks Ansible.

2. **Naviguer dans le dossier nouvellement créé** :

   ```bash
   cd ansible_project
   ```

---

<a name="configurer-inventaire"></a>
## 🗂️ Configurer l'Inventaire Ansible

L'inventaire Ansible est un fichier essentiel pour définir les hôtes gérés par Ansible. Il spécifie les adresses IP, les utilisateurs, et peut organiser les hôtes en groupes.

1. **Créer un fichier `inventory.ini`** :

   ```bash
   nano inventory.ini
   ```

2. **Ajouter les informations des nœuds dans `inventory.ini`** :

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
   ```

   **Explications** :

   - **[node_containers]** : Groupe principal contenant tous les conteneurs.
   - `ansible_host` : Spécifie l'adresse IP de chaque nœud.
   - `ansible_user` : Définit l’utilisateur pour l’accès SSH, ici `root` pour tous les nœuds.
   - **[web]**, **[database]**, **[mail]** : Groupes permettant de structurer les nœuds selon leur fonction.

3. **Enregistrer et quitter l'éditeur** :
   - Appuyez sur `Ctrl + X`, puis `Y` et `Entrée`.

---

<a name="verifier-inventaire"></a>
## 🌐 Vérifier l'Inventaire et la Connectivité

Avant d'exécuter des playbooks, il est important de vérifier que l'inventaire est correctement configuré et que la connectivité SSH est opérationnelle.

1. **Lister les hôtes d'un groupe spécifique** :

   ```bash
   ansible web -i inventory.ini --list-hosts
   ```

2. **Lister tous les hôtes dans l'inventaire** :

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

**Note :** Si vous rencontrez des avertissements concernant l'interpréteur Python, vous pouvez spécifier l'interpréteur Python dans l'inventaire pour chaque hôte :

```ini
node1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
```

---

<a name="partie1"></a>
## 📝 Partie 1 : Créer et Exécuter votre Premier Playbook

### Étape 1 : Écrire un Playbook de Base pour la Création de Fichiers

1. **Créer un fichier nommé `first-playbook.yml`** :

   ```bash
   nano first-playbook.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Premier Playbook de Création de Fichier
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

   - `hosts: all` : Le playbook s'exécute sur tous les hôtes définis dans l'inventaire.
   - `become: yes` : Exécute les tâches avec les privilèges élevés (root).
   - **tasks** : Liste des tâches à exécuter.
   - Le module `file` est utilisé pour gérer les fichiers et répertoires.

3. **Enregistrer et quitter l'éditeur** :
   - Appuyez sur `Ctrl + X`, puis `Y` et `Entrée`.

### Étape 2 : Exécuter le Playbook

1. **Lancer le playbook** :

   ```bash
   ansible-playbook -i inventory.ini first-playbook.yml
   ```

2. **Vérifier que le fichier a bien été créé** :

   ```bash
   ansible all -m command -a "ls -l /tmp/foo.conf" -i inventory.ini
   ```

---

<a name="partie2"></a>
## 📝 Partie 2 : Créer un Playbook Multi-Tâches

### Étape 1 : Écrire un Playbook avec Plusieurs Tâches

1. **Créer un fichier nommé `multi-tasks-playbook.yml`** :

   ```bash
   nano multi-tasks-playbook.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Installation et Archivage des Logs
     hosts: all
     become: yes
     tasks:
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
           excludes:
             - '/var/log/wtmp'
             - '/var/log/btmp'
         tags: archive_logs

   - name: Installation de Git sur les nœuds Ubuntu
     hosts: node1,node5,node6
     become: yes
     tasks:
       - name: Mettre à jour le cache APT
         apt:
           update_cache: yes
         tags: update_cache

       - name: Installer Git
         apt:
           name: git
           state: present
         tags: install_git
   ```

   **Explications** :

   - Les tags ont été ajoutés pour permettre une exécution sélective des tâches.
   - L'exclusion de certains fichiers lors de l'archivage des logs pour éviter d'inclure des fichiers sensibles ou volumineux.
   - Mise à jour du cache APT avant l'installation de Git sur les nœuds Ubuntu.

3. **Enregistrer et quitter l'éditeur**.

### Étape 2 : Exécuter le Playbook Multi-Tâches

1. **Lancer le playbook** :

   ```bash
   ansible-playbook -i inventory.ini multi-tasks-playbook.yml
   ```

2. **Vérifier que l’archive a bien été créée** :

   ```bash
   ansible all -m command -a "ls -lh /tmp/logs.tar.gz" -i inventory.ini
   ```

3. **Vérifier que `tmux` est installé** :

   ```bash
   ansible all -m shell -a "tmux -V" -i inventory.ini
   ```

4. **Vérifier que `git` est installé sur les nœuds Ubuntu** :

   ```bash
   ansible node1,node5,node6 -m shell -a "git --version" -i inventory.ini
   ```

---

<a name="partie3"></a>
## 📝 Partie 3 : Réutiliser des Tâches via l'Importation

### Étape 1 : Créer un Fichier de Tâches Réutilisables

1. **Créer un fichier nommé `group-tasks.yml`** :

   ```bash
   nano group-tasks.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Créer le groupe développeurs
     group:
       name: developpeurs

   - name: Créer le groupe sécurité
     group:
       name: securite

   - name: Créer le groupe finance
     group:
       name: finance
   ```

3. **Enregistrer et quitter l'éditeur**.

### Étape 2 : Importer les Tâches dans un Playbook Principal

1. **Modifier `first-playbook.yml` pour inclure `group-tasks.yml`** :

   ```yaml
   ---
   - name: Premier Playbook de Création de Fichier et de Groupes
     hosts: all
     become: yes
     tasks:
       - name: Créer un fichier de configuration
         file:
           path: /tmp/foo.conf
           mode: '0664'
           owner: root
           state: touch

       - name: Importer les tâches pour créer des groupes
         import_tasks: group-tasks.yml
   ```

2. **Lancer le playbook avec importation de tâches** :

   ```bash
   ansible-playbook -i inventory.ini first-playbook.yml
   ```

3. **Vérifier que les groupes ont été créés** :

   ```bash
   ansible all -m command -a "getent group developpeurs securite finance" -i inventory.ini
   ```

---

<a name="partie4"></a>
## 📝 Partie 4 : Utiliser les Tags pour l'Exécution Sélective

Les tags permettent de cibler des tâches spécifiques lors de l'exécution d'un playbook.

1. **Taguer les tâches dans `multi-tasks-playbook.yml`** (si ce n'est pas déjà fait) :

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
       excludes:
         - '/var/log/wtmp'
         - '/var/log/btmp'
     tags: archive_logs

   - name: Mettre à jour le cache APT
     apt:
       update_cache: yes
     tags: update_cache

   - name: Installer Git
     apt:
       name: git
       state: present
     tags: install_git
   ```

2. **Exécuter des Tâches Spécifiques par Tag** :

   - Pour installer uniquement `tmux` :

     ```bash
     ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags install_tmux
     ```

   - Pour créer l’archive uniquement :

     ```bash
     ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags archive_logs
     ```

   - Pour installer uniquement `git` sur les nœuds Ubuntu :

     ```bash
     ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags install_git
     ```

   - Pour exécuter toutes les tâches sauf celles d'un tag spécifique :

     ```bash
     ansible-playbook -i inventory.ini multi-tasks-playbook.yml --skip-tags install_tmux
     ```

---

<a name="conclusion"></a>
## 🎯 Conclusion

Dans ce chapitre, vous avez appris à :

- Écrire des playbooks Ansible avec des tâches simples et multiples.
- Utiliser des modules Ansible appropriés pour gérer les paquets, les fichiers, et les archives.
- Réutiliser des tâches via l'importation de fichiers de tâches.
- Utiliser les tags pour exécuter sélectivement des parties de vos playbooks.

Ces pratiques vous permettront d'automatiser efficacement la gestion de vos conteneurs Docker et de vos serveurs, tout en maintenant vos playbooks organisés et maintenables.

---

## 🛠️ Conseils Supplémentaires

- **Utiliser les Rôles Ansible** : Pour des projets plus complexes, envisagez d'utiliser des rôles pour organiser vos playbooks et vos tâches.
- **Variables et Templates** : Utilisez des variables pour rendre vos playbooks plus flexibles, et des templates Jinja2 pour générer des fichiers de configuration dynamiques.
- **Gestion des Différences de Plateforme** : Utilisez des conditions `when` pour gérer les différences entre les systèmes d'exploitation.
- **Vérification des Erreurs** : Toujours vérifier les messages d'erreur lors de l'exécution des playbooks pour corriger les problèmes potentiels.

---

## 📚 Ressources Utiles

- [Documentation Ansible - Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html)
- [Ansible Best Practices](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html)
- [Ansible Module Index](https://docs.ansible.com/ansible/latest/collections/index_module.html)
- [Ansible Tags](https://docs.ansible.com/ansible/latest/user_guide/playbooks_tags.html)

---

## 🙌 Félicitations !

Vous avez amélioré vos compétences en écriture de playbooks Ansible avancés, en réutilisant des tâches, et en utilisant des fonctionnalités puissantes comme les tags. Continuez à pratiquer et à explorer les fonctionnalités d'Ansible pour automatiser efficacement vos tâches d'administration système.

