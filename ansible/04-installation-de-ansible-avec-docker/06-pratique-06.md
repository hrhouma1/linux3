# 🎓 Chapitre 6 : Utilisation des Boucles dans Ansible

### Introduction

Dans cette pratique, vous allez apprendre à utiliser les boucles dans Ansible pour exécuter une tâche plusieurs fois sans avoir à la répéter manuellement. Cette fonctionnalité est utile pour créer plusieurs utilisateurs, gérer des services, ou appliquer des configurations sur des éléments multiples.

---

## 📂 Étape Préparatoire : Création du Dossier de Travail pour cette Pratique

1. **Créer un dossier pour le projet Ansible** :
   
   ```bash
   mkdir ansible_project_loops
   ```
   
   Ce dossier `ansible_project_loops` servira à stocker tous les fichiers et playbooks nécessaires.

2. **Naviguer dans le dossier** :

   ```bash
   cd ansible_project_loops
   ```

---

## 🗂️ Étape Préparatoire : Configurer l'Inventaire

1. **Créer un fichier `inventory.ini`** pour définir les nœuds :

   ```bash
   nano inventory.ini
   ```

2. **Ajouter les informations des nœuds** :

   ```ini
   [node_containers]
   node1 ansible_host=172.20.0.2 ansible_user=root
   node2 ansible_host=172.20.0.3 ansible_user=root
   node3 ansible_host=172.20.0.4 ansible_user=root
   node4 ansible_host=172.20.0.5 ansible_user=root
   node5 ansible_host=172.20.0.6 ansible_user=root
   node6 ansible_host=172.20.0.7 ansible_user=root
   ```

3. **Enregistrer et quitter l'éditeur**.

---

## Partie 1 : Boucler sur une Liste

### 📝 Étape 1 : Écrire un Playbook pour Afficher les Éléments d'une Liste

1. **Créer un fichier nommé `print-list.yml`** :

   ```bash
   nano print-list.yml
   ```

2. **Ajouter le contenu suivant** pour afficher une liste de nombres premiers :

   ```yaml
   ---
   - name: Afficher une liste de nombres premiers
     hosts: node1
     vars:
       prime_numbers: [2, 3, 5, 7, 11]
     tasks:
       - name: Afficher les nombres premiers
         debug:
           msg: "{{ item }}"
         loop: "{{ prime_numbers }}"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini print-list.yml
   ```

   **Explications** :
   - `loop: "{{ prime_numbers }}"` : Itère sur chaque élément de la liste `prime_numbers` et affiche chaque nombre.

---

## Partie 2 : Boucler sur une Liste de Dictionnaires

### 📝 Étape 1 : Ajouter Plusieurs Utilisateurs avec une Boucle

1. **Créer un fichier `add-users.yml`** pour ajouter plusieurs utilisateurs avec des mots de passe :

   ```bash
   nano add-users.yml
   ```

2. **Ajouter le contenu suivant** pour définir une liste de dictionnaires représentant des utilisateurs :

   ```yaml
   ---
   - name: Ajouter plusieurs utilisateurs
     hosts: database
     vars:
       db_users:
         - username: alice
           password: password1
         - username: bob
           password: password2
         - username: charlie
           password: password3
     tasks:
       - name: Ajouter les utilisateurs
         user:
           name: "{{ item.username }}"
           password: "{{ item.password | password_hash('sha512') }}"
         loop: "{{ db_users }}"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini add-users.yml
   ```

   **Explications** :
   - `db_users` : Liste de dictionnaires contenant des informations sur chaque utilisateur.
   - `user` : Module Ansible pour ajouter un utilisateur.
   - `password_hash('sha512')` : Hash le mot de passe pour le rendre sécurisé.

4. **Vérification** : 

   Utilisez la commande suivante pour confirmer que les utilisateurs ont été ajoutés :

   ```bash
   ansible database -m command -a "tail -3 /etc/passwd" -i inventory.ini
   ```

---

## Partie 3 : Boucler sur un Dictionnaire

Par défaut, Ansible ne peut pas boucler directement sur un dictionnaire. Nous allons utiliser le filtre `dict2items` pour transformer un dictionnaire en liste.

### 📝 Étape 1 : Écrire un Playbook pour Afficher les Paires Clé/Valeur d'un Dictionnaire

1. **Créer un fichier nommé `print-dictionary.yml`** :

   ```bash
   nano print-dictionary.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Afficher les informations d'un employé
     hosts: node1
     vars:
       employee:
         name: "Alice"
         title: "Administrateur Système"
         company: "TechCorp"
     tasks:
       - name: Afficher le dictionnaire employé
         debug:
           msg: "{{ item.key }}: {{ item.value }}"
         loop: "{{ employee | dict2items }}"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini print-dictionary.yml
   ```

   **Explications** :
   - `dict2items` : Transforme le dictionnaire `employee` en une liste de paires clé/valeur.

---

## Partie 4 : Boucler sur une Plage de Nombres

### 📝 Étape 1 : Afficher une Plage de Nombres

1. **Créer un fichier `range-loop.yml`** :

   ```bash
   nano range-loop.yml
   ```

2. **Ajouter le contenu suivant** pour afficher les nombres de 5 à 14 :

   ```yaml
   ---
   - name: Boucle sur une plage de nombres
     hosts: node1
     tasks:
       - name: Afficher les nombres de 5 à 14
         debug:
           msg: "{{ item }}"
         loop: "{{ range(5, 15) | list }}"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini range-loop.yml
   ```

   **Explications** :
   - `range(5, 15) | list` : Crée une liste de nombres de 5 à 14.

---

## Partie 5 : Boucler sur les Inventaires

### 📝 Étape 1 : Boucler sur Tous les Nœuds de l’Inventaire

1. **Créer un fichier `loop-inventory.yml`** :

   ```bash
   nano loop-inventory.yml
   ```

2. **Ajouter le contenu suivant** pour pinguer tous les nœuds de l’inventaire :

   ```yaml
   ---
   - name: Boucler sur l'inventaire
     hosts: node1
     tasks:
       - name: Pinguer tous les hôtes
         command: ping -c 1 "{{ item }}"
         loop: "{{ groups['all'] }}"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini loop-inventory.yml
   ```

   **Explications** :
   - `groups['all']` : Itère sur tous les nœuds de l’inventaire et tente de les pinguer.

---

## Partie 6 : Utiliser une Pause dans une Boucle

### 📝 Étape 1 : Créer un Compte à Rebours avec une Pause

1. **Créer un fichier `countdown.yml`** :

   ```bash
   nano countdown.yml
   ```

2. **Ajouter le contenu suivant pour un compte à rebours de 10 secondes** :

   ```yaml
   ---
   - name: Compte à rebours de 10 secondes
     hosts: node1
     tasks:
       - name: Afficher le compte à rebours
         debug:
           msg: "{{ 10 - item }} secondes restantes..."
         loop: "{{ range(10) | list }}"
         loop_control:
           pause: 1
       - name: Afficher le message final
         debug:
           msg: "C'est l'heure !"
   ```

3. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini countdown.yml
   ```

   **Explications** :
   - `loop_control: pause: 1` : Insère une pause de 1 seconde entre chaque itération.
