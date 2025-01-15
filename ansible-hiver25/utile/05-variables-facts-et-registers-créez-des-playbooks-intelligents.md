---
title: "Chapitre 5 - Travailler avec les Variables, Facts et Registers dans Ansible"
description: "Découvrez comment utiliser les variables, facts et registers dans Ansible pour créer des playbooks plus intelligents et dynamiques."
emoji: "🔧"
---

# Chapitre 5 - Travailler avec les Variables, Facts et Registers dans Ansible - Pratique 04


<a name="table-des-matieres"></a>
## 📋 Table des Matières
1. [Introduction](#introduction)
2. [Prérequis et Étape Préparatoire](#etape-preparatoire)
3. [Résumé des Étapes Précédentes](#resume-etapes)
4. [Utilisation des Variables dans un Playbook](#utilisation-variables)
5. [Travailler avec des Listes et des Dictionnaires](#travailler-listes-dictionnaires) 
6. [Utilisation des Fichiers de Variables Externes](#utilisation-fichiers-variables)
7. [Utiliser les Facts Ansible](#utiliser-facts)
8. [Capturer des Résultats avec les Registers](#capturer-registers)
9. [Utilisation Avancée des Registers avec des Conditions](#utilisation-registers-conditions)
10. [Utiliser les Facts pour Différencier les Systèmes](#utiliser-facts-differencier)
11. [Conclusion](#conclusion)

---
<a name="introduction"></a>
# Introduction
---


Dans cette pratique avancée, nous allons explorer les **variables**, les **facts**, et les **registers** dans Ansible. Ces éléments vous permettront de créer des playbooks plus dynamiques, flexibles et personnalisés. Vous apprendrez à :

- **Définir et utiliser des variables** dans vos playbooks.
- **Inclure des variables à partir de fichiers externes**.
- **Utiliser les facts Ansible** pour récupérer des informations sur vos nœuds gérés.
- **Capturer les résultats des tâches avec des registers** pour les réutiliser.



---
<a name="etape-preparatoire"></a>
# Prérequis et Étape Préparatoire : Créer le Dossier de Travail
---

Pour réaliser cette pratique, il est *obligatoire* d'avoir effectué les étapes 1 à 4 du [Chapitre 2 - Déployer une Infrastructure Docker avec Ansible - Pratique 02](02-automatiser-avec-ansible.md).

Si vous aviez précédemment arrêté les conteneurs, vous pouvez les redémarrer avec :

```bash
docker start node1 node2 node3 node4 node5 node6
```
ou 
```bash
docker compose up -d
```

#### [🔙 Retournez à la Table des Matières](#table-des-matieres)

---
<a name="resume-etapes"></a>
## Résumé des Étapes Précédentes (Chapitre 2)
---
Dans le chapitre précédent, nous avons mis en place une infrastructure Docker complète pour nos tests Ansible :

#### 1. Installation des Prérequis
- Installation de Docker et Docker Compose sur Ubuntu 22.04
- Configuration de l'environnement de base

#### 2. Déploiement de l'Infrastructure
- Création d'une architecture avec 6 conteneurs Docker :
  - **node1, node5, node6** : Ubuntu
  - **node2** : Debian  
  - **node3, node4** : AlmaLinux
- Configuration réseau avec des IPs statiques (172.20.0.2-7)
- Exposition des ports SSH (22) et HTTP (80)

#### 3. Configuration SSH
- Génération des clés SSH
- Distribution des clés publiques aux conteneurs
- Configuration de l'accès SSH sans mot de passe
- Vérification de la connectivité

#### 4. Structure de l'Inventaire
Organisation des conteneurs en groupes fonctionnels :
- **[node_containers]** : Tous les nœuds
- **[web]** : node1, node5 
- **[database]** : node2, node3
- **[mail]** : node4, node6

Cette infrastructure nous permet maintenant de :
- Tester des playbooks sur différentes distributions Linux
- Organiser nos déploiements par groupes fonctionnels
- Automatiser des tâches de manière structurée


Architecture de l'infrastructure à créer avec docker-compose :


```
                          +----------------------+
                          |  Ansible Controller  |
                          |     Ubuntu 22.04     |
                          |  IP: 172.20.0.X      |
                          +----------------------+
                                     |
                                     |
        --------------------------------------------------------------------------
        |        |        |        |        |        |        |
   +---------+ +---------+ +---------+ +---------+ +---------+ +---------+ +---------+
   | Node1   | | Node2   | | Node3   | | Node4   | | Node5   | | Node6   | |  NodeX   |
   | Ubuntu  | | Debian  | | AlmaLin | | AlmaLin | | Ubuntu  | | Ubuntu  | |  (opt)  |
   | 172.20.0.2| 172.20.0.3| 172.20.0.4| 172.20.0.5| 172.20.0.6| 172.20.0.7|  ...    |
   +---------+ +---------+ +---------+ +---------+ +---------+ +---------+ +---------+

```



#### [🔙 Retournez à la Table des Matières](#table-des-matieres)

---
<a name="partie1"></a>
## 1 - Vérifiez la configuration de l'inventaire Ansible
---

#### 1 - **Créer un dossier pour le projet Ansible** :

   ```bash
   mkdir ansible_project_variables
   ```

   Ce dossier `ansible_project_variables` contiendra vos fichiers et playbooks Ansible pour cette pratique.

#### 2 - **Naviguer dans ce dossier** :

   ```bash
   cd ansible_project_variables
   ```


#### 3 - **Créer un fichier `inventory.ini`** :


L'inventaire Ansible est un fichier essentiel pour définir les hôtes gérés par Ansible. Il spécifie les adresses IP, les utilisateurs, et peut organiser les hôtes en groupes.

   ```bash
   nano inventory.ini
   ```

#### 4 - **Ajouter les informations des nœuds dans `inventory.ini`** :

   ```ini
   [node_containers]
   node1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   node2 ansible_host=172.20.0.3 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   node3 ansible_host=172.20.0.4 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   node4 ansible_host=172.20.0.5 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   node5 ansible_host=172.20.0.6 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   node6 ansible_host=172.20.0.7 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   ```

   **Remarque :** L'ajout de `ansible_python_interpreter=/usr/bin/python3` pour chaque hôte permet d'éviter les avertissements liés à la découverte automatique de l'interpréteur Python.

#### 5 - **Enregistrer et quitter l'éditeur** :
   - Appuyez sur `Ctrl + X`, puis `Y` et `Entrée` pour sauvegarder.

#### [🔙 Retournez à la Table des Matières](#table-des-matieres)


---
<a name="utilisation-variables"></a>
## 2 -  Utilisation des Variables dans un Playbook
---

#### 1 - **Créer un fichier `variables-playbook.yml`** :

   ```bash
   nano variables-playbook.yml
   ```

#### 2 - **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Démonstration des Variables
     hosts: node1
     become: yes
     vars:
       fav_color: "bleu"
     tasks:
       - name: Afficher la couleur préférée
         debug:
           msg: "Ma couleur préférée est {{ fav_color }}."
   ```

   **Explications** :

   - **`vars`** : Définit des variables locales au playbook ou à la play (ensemble de tâches).
   - **`debug`** : Le module `debug` est utilisé pour afficher des messages pendant l'exécution du playbook. Ici, il affiche la valeur de la variable `fav_color`.

#### 3 - **Enregistrer et quitter l'éditeur**.

#### 4 - **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini variables-playbook.yml
   ```

   **Résultat attendu :**

   Vous devriez voir le message : "Ma couleur préférée est bleu."
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---

<a name="travailler-listes-dictionnaires"></a>
## 3 - Travailler avec des Listes et des Dictionnaires
---

#### 1 - **Mettre à jour le fichier `variables-playbook.yml`** pour ajouter des listes et des dictionnaires :

   ```yaml
   ---
   - name: Utilisation de Listes et de Dictionnaires
     hosts: node1
     become: yes
     vars:
       port_nums:
         - 21
         - 22
         - 80
         - 443
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
           msg: "Le deuxième port est {{ port_nums[1] }}."

       - name: Afficher l'UID de Bob
         debug:
           msg: "L'UID de Bob est {{ users.bob.uid }}."
   ```

   **Explications** :

   - **Listes** : `port_nums` est une liste de numéros de port. On accède aux éléments avec une syntaxe d'indice, commençant à 0.
   - **Dictionnaires** : `users` est un dictionnaire contenant des sous-dictionnaires pour `bob` et `alice`.

#### 3 - **Enregistrer et quitter l'éditeur**.
Dans ce playbook, nous explorons l'utilisation des listes et des dictionnaires dans Ansible :

1. **Structure du Playbook** :
   - Le playbook cible l'hôte `node1`
   - `become: yes` permet d'exécuter les tâches avec des privilèges élevés

2. **Définition des Variables** :
   - **Liste `port_nums`** :
     - Contient une séquence de ports courants : 21 (FTP), 22 (SSH), 80 (HTTP), 443 (HTTPS)
     - L'accès se fait via l'index : `port_nums[0]` pour 21, `port_nums[1]` pour 22, etc.
   
   - **Dictionnaire `users`** :
     - Structure imbriquée contenant les informations de deux utilisateurs
     - Pour Bob :
       * username: "bob"
       * uid: 1001 (identifiant unique)
       * shell: "/bin/bash" (shell par défaut)
     - Pour Alice :
       * username: "alice"
       * uid: 1002
       * shell: "/bin/zsh"

3. **Les Tâches** :
   - Première tâche : affiche le port 22 en accédant au deuxième élément de la liste
   - Deuxième tâche : affiche l'UID de Bob en naviguant dans le dictionnaire imbriqué

La structure proposée démontre la puissance des variables structurées dans Ansible pour organiser et accéder aux données de manière efficace.

#### 4 - **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini variables-playbook.yml
   ```

   **Résultats attendus :**

   - Affichage du deuxième port : "Le deuxième port est 22."
   - Affichage de l'UID de Bob : "L'UID de Bob est 1001."
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---
<a name="utilisation-fichiers-variables"></a>
## 4 - Utilisation des Fichiers de Variables Externes
---

Pour rendre vos playbooks plus modulaires, vous pouvez stocker les variables dans des fichiers externes.

#### 1 - **Créer un fichier de variables externe `myvars.yml`** :

   ```bash
   nano myvars.yml
   ```

#### 2 - **Ajouter les variables dans `myvars.yml`** :

   ```yaml
   ---
   port_nums:
     - 21
     - 22
     - 80
     - 443
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

#### 3 - **Mettre à jour `variables-playbook.yml` pour inclure `myvars.yml`** :

   ```yaml
   ---
   - name: Utilisation de Variables depuis un Fichier Externe
     hosts: node1
     become: yes
     vars_files:
       - myvars.yml
     tasks:
       - name: Afficher le premier port
         debug:
           msg: "Le premier port est {{ port_nums[0] }}."

       - name: Afficher le shell d'Alice
         debug:
           msg: "Le shell de Alice est {{ users.alice.shell }}."
   ```

   **Explications** :

   - **`vars_files`** : Permet d'inclure des fichiers de variables externes.

#### 4 - **Enregistrer et quitter l'éditeur**.

#### 5 - **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini variables-playbook.yml
   ```

   **Résultats attendus :**

   - Affichage du premier port : "Le premier port est 21."
   - Affichage du shell d'Alice : "Le shell de Alice est /bin/zsh."
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---
<a name="utiliser-facts"></a>
## 5 - Utiliser les Facts Ansible
---

Ansible collecte automatiquement des **facts**, qui sont des informations sur les hôtes cibles, avant d'exécuter les tâches. Ces facts peuvent être utilisés dans vos playbooks pour adapter le comportement en fonction des caractéristiques du système.

1. **Créer un playbook `show-facts.yml`** :

   ```bash
   nano show-facts.yml
   ```

2. **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Afficher des Facts sur le Système
     hosts: node1
     become: yes
     tasks:
       - name: Afficher l'adresse IP par défaut
         debug:
           msg: "L'adresse IP par défaut est {{ ansible_default_ipv4.address }}."

       - name: Afficher la Distribution OS
         debug:
           msg: "La distribution du système est {{ ansible_distribution }}."

       - name: Afficher le Nom d'Hôte
         debug:
           msg: "Le nom d'hôte est {{ ansible_hostname }}."
   ```

   **Explications** :

   - `ansible_default_ipv4.address` : Adresse IP par défaut de l'hôte.
   - `ansible_distribution` : Nom de la distribution Linux (e.g., Ubuntu, Debian).
   - `ansible_hostname` : Nom de l'hôte.

3. **Enregistrer et quitter l'éditeur**.

4. **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini show-facts.yml
   ```

   **Résultats attendus :**

   Les messages afficheront les facts récupérés depuis `node1`.
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---

<a name="capturer-registers"></a>
## 6 - Capturer des Résultats avec les Registers
---

Les **registers** sont utilisés pour stocker la sortie d'une tâche dans une variable, afin de la réutiliser ultérieurement dans le playbook.

#### 1 - **Créer un fichier `register-playbook.yml`** :

   ```bash
   nano register-playbook.yml
   ```

#### 2 - **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Utiliser les Registers pour Capturer la Sortie d'une Commande
     hosts: node1
     become: yes
     tasks:
       - name: Exécuter la commande uptime
         command: uptime
         register: server_uptime

       - name: Afficher le Résultat de Uptime
         debug:
           msg: "L'uptime du serveur est : {{ server_uptime.stdout }}."
   ```

   **Explications** :

   - **`register`** : Permet de stocker la sortie de la tâche dans une variable.
   - **`server_uptime.stdout`** : Contient la sortie standard de la commande exécutée.

#### 3 - **Enregistrer et quitter l'éditeur**.


   **Expliquons l'exemple ci-haut en détail** :

   Imaginez que vous avez une boîte magique (le register) qui peut capturer et stocker le résultat d'une action. C'est exactement ce que fait `register: server_uptime` !

   ##### 3.1 **Que contient server_uptime ?**
   
   Après l'exécution de la commande `uptime`, `server_uptime` contient plusieurs informations :

   ```yaml
   server_uptime:
     changed: true                    # Indique si la tâche a modifié quelque chose
     cmd: ["uptime"]                  # La commande qui a été exécutée
     delta: "0:00:00.123456"         # Durée d'exécution
     end: "2023-12-20 10:00:00"      # Heure de fin
     failed: false                    # Si la tâche a échoué ou non
     msg: ""                         # Message d'erreur éventuel
     rc: 0                           # Code de retour (0 = succès)
     start: "2023-12-20 10:00:00"    # Heure de début
     stderr: ""                      # Erreurs éventuelles
     stderr_lines: []                # Erreurs ligne par ligne
     stdout: " 10:00:00 up 2 days"   # Résultat de la commande
     stdout_lines: [                 # Résultat ligne par ligne
       " 10:00:00 up 2 days"
     ]
   ```

   ##### 3.2 **Comment utiliser ces informations ?**

   - Pour accéder au résultat principal : `{{ server_uptime.stdout }}`
   - Pour vérifier si ça a réussi : `{{ server_uptime.rc }}` (0 = succès)
   - Pour voir les erreurs : `{{ server_uptime.stderr }}`

   ##### 3.3 **Exemple pratique supplémentaire** :

   ```yaml
   - name: Exemple d'utilisation avancée du register
     debug:
       msg: |
         Résultat brut : {{ server_uptime.stdout }}
         La commande a-t-elle modifié quelque chose ? {{ server_uptime.changed }}
         Y a-t-il eu des erreurs ? {{ server_uptime.failed }}
         Code de retour : {{ server_uptime.rc }}
   ```

   Les registers sont comme des "post-it" numériques : ils vous permettent de garder une trace des résultats pour les utiliser plus tard dans votre playbook !


#### 4 - **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini register-playbook.yml
   ```

   **Résultat attendu :**

   Le message affichera l'uptime du serveur `node1`.
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---

<a name="utilisation-registers-conditions"></a>
## 7 - Utilisation Avancée des Registers avec des Conditions
---

Vous pouvez utiliser les registers pour prendre des décisions dans vos playbooks en fonction des résultats des tâches précédentes.

#### 1 - **Mettre à jour `register-playbook.yml`** pour inclure une condition :

   ```yaml
   ---
   - name: Utiliser les Registers avec des Conditions
     hosts: node1
     become: yes
     tasks:
       - name: Vérifier si un paquet est installé (par exemple, `git`)
         command: dpkg -l git
         register: git_installed
         failed_when: git_installed.rc > 1

       - name: Installer Git si non installé
         apt:
           name: git
           state: present
         when: git_installed.rc != 0

       - name: Afficher l'état de Git
         debug:
           msg: "Git est installé."
         when: git_installed.rc == 0
   ```

   **Explications** :

   - **`failed_when: git_installed.rc > 1`** : Empêche la tâche de marquer l'hôte comme `FAILED` si le code de retour est `1` (paquet non installé).
   - **`git_installed.rc`** : Code de retour de la commande. Si `0`, la commande a réussi (le paquet est installé).
   - **`when`** : Condition pour exécuter ou non une tâche.

#### 2 - **Enregistrer et quitter l'éditeur**.

#### 3 - **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini register-playbook.yml
   ```

   **Résultat attendu :**

   - Si `git` n'est pas installé sur `node1`, Ansible l'installera.
   - Si `git` est déjà installé, Ansible affichera "Git est installé."
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---

<a name="utiliser-facts-differencier"></a>
## 8 - Utiliser les Facts pour Différencier les Systèmes
---

Vous pouvez utiliser les facts pour adapter vos playbooks en fonction du système d'exploitation ou d'autres caractéristiques.

#### 1 - **Créer un playbook `os-specific-playbook.yml`** :

   ```bash
   nano os-specific-playbook.yml
   ```

#### 2 - **Ajouter le contenu suivant** :

   ```yaml
   ---
   - name: Installer un Paquet en Fonction du Système d'Exploitation
     hosts: all
     become: yes
     tasks:
       - name: Installer htop sur les systèmes basés sur Debian
         apt:
           name: htop
           state: present
         when: ansible_os_family == "Debian"

       - name: Installer htop sur les systèmes basés sur RedHat
         yum:
           name: htop
           state: present
         when: ansible_os_family == "RedHat"
   ```

   **Explications** :

   - **`ansible_os_family`** : Fact qui indique la famille du système d'exploitation (`Debian`, `RedHat`, etc.).
   - **`when`** : Les tâches seront exécutées uniquement si la condition est vérifiée.

#### 3 - **Enregistrer et quitter l'éditeur**.

#### 4 - **Exécuter le playbook** :

   ```bash
   ansible-playbook -i inventory.ini os-specific-playbook.yml
   ```

   **Résultat attendu :**

   - Sur les nœuds basés sur Debian (Ubuntu, Debian), le paquet `htop` sera installé via `apt`.
   - Sur les nœuds basés sur RedHat (AlmaLinux), `htop` sera installé via `yum`.
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---

<a name="conclusion"></a>
## 9 - Conclusion
---

Dans ce chapitre, vous avez appris à :

- **Utiliser des variables** dans vos playbooks pour rendre vos configurations plus dynamiques.
- **Inclure des fichiers de variables externes** pour une meilleure organisation.
- **Exploiter les facts Ansible** pour adapter vos playbooks en fonction des caractéristiques des hôtes.
- **Utiliser les registers** pour capturer et réutiliser les résultats des tâches.
- **Mettre en place des conditions** basées sur les registers et les facts pour contrôler l'exécution des tâches.

Ces compétences vous permettront de créer des playbooks plus flexibles et puissants, capables de gérer des environnements hétérogènes et de s'adapter aux différentes situations.
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---

<a name="conseils-supplementaires"></a>
## 10 - Conseils Supplémentaires

- **Variables de Groupes et d'Hôtes** : Vous pouvez définir des variables spécifiques à des groupes ou des hôtes dans des répertoires `group_vars` et `host_vars`.
- **Variables d'Environnement** : Ansible permet également d'accéder aux variables d'environnement du système.
- **Templates Jinja2** : Utilisez des templates pour générer des fichiers de configuration dynamiques, en combinant variables et logique conditionnelle.
- **Ansible Vault** : Pour stocker des variables sensibles (comme des mots de passe), utilisez Ansible Vault pour les chiffrer.
#### [🔙 Retournez à la Table des Matières](#table-des-matieres)
---

<a name="ressources-utiles"></a>
## 11 - Ressources Utiles

- [Documentation Ansible sur les Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)
- [Ansible Facts](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#information-discovered-from-systems-facts)
- [Utilisation des Registers](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#registering-variables)
- [Conditions dans les Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html)

---

<a name="conclusion"></a>
## 12 - Conclusion générale

Vous avez amélioré vos compétences en Ansible en apprenant à utiliser les variables, facts et registers pour rendre vos playbooks plus dynamiques et adaptables. Continuez à explorer ces fonctionnalités pour créer des automatisations encore plus efficaces.

#### [🔙 Retournez à la Table des Matières](#table-des-matieres)