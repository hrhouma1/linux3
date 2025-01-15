---
title: "Chapitre 4 - Thérorie des variables, facts et registers pour créez des playbooks intelligents"
description: "Découvrez la théorie des variables, facts et registers pour créer des playbooks plus intelligents et dynamiques."
emoji: "🔧"
---

# Chapitre 4 - Thérorie des variables, facts et registers pour créez des playbooks intelligents

<a name="table-matieres"></a>
## 📋 Table des Matières

1. [Les Variables Ansible](#variables)
   - [Pourquoi utiliser des variables](#pourquoi-variables)
   - [Exemple simple de variables](#exemple-variables)
   - [Types de variables dans Ansible](#types-variables)
   - [Portée des variables](#portee-variables)
   - [Précédence des variables](#precedence-variables)
2. [Les Facts Ansible](#facts)
   - [Qu'est-ce que les Facts ?](#definition-facts)
   - [Utilisation des Facts](#utilisation-facts)
   - [Exemple avec les Facts](#exemple-facts)
   - [Facts personnalisés](#facts-personnalises)
3. [Les Registres (Registers)](#registers)
   - [Explication des registres](#explication-registers)
   - [Structure d'un registre](#structure-registers)
   - [Exemple avec une commande ad hoc](#exemple-registers)
   - [Utilisation typique des registres](#utilisation-registers)
   - [Bonnes pratiques](#bonnes-pratiques-registers)
4. [Différences entre Registres et Facts](#differences)
   - [Tableau comparatif](#differences)
   - [Exemple pratique](#exemple-pratique)
   - [Cas d'usage](#cas-usage)
5. [Exercices Pratiques](#exercices)
   - [Manipulation des variables](#exercice-variables)
   - [Utilisation des facts](#exercice-facts)
   - [Travail avec les registres](#exercice-registers)




<a name="variables"></a>
---
# 01 - **Les Variables Ansible :**
---

Les **variables Ansible** sont des valeurs définies qui permettent de personnaliser l'exécution des playbooks. Elles permettent de rendre les playbooks plus **dynamiques**, **réutilisables** et **faciles à adapter** en fonction des besoins ou des environnements.


> Imaginez que vous préparez un gâteau. Pour faire ce gâteau, vous avez besoin d'une recette (le playbook) et d'ingrédients (les variables). 
> 🧁 **Les variables, c'est comme les ingrédients de votre recette :**
> - Au lieu de dire "mettez 100g de farine", vous pourriez dire "mettez `{{ quantite_farine }}` grammes de farine"
> - La variable `quantite_farine` pourrait être 100g pour un petit gâteau ou 200g pour un grand gâteau
> - Vous n'avez pas besoin de réécrire toute la recette, il suffit de changer la valeur de la variable !


<a name="pourquoi-variables"></a>

#### **Pourquoi utiliser des variables :**
- Éviter de coder en dur des valeurs (comme les adresses IP, noms d’hôte, chemins de fichiers).
- Réutiliser un même playbook dans plusieurs contextes en changeant simplement les valeurs des variables.



<a name="exemple-variables"></a>

##### [🔙 Retour à la table des matières](#table-matieres)
---
### 1.1. **Exemple simple de variables :**
---

```yaml
---
- name: Exemple de variables
  hosts: all
  vars:
    package_name: vim
  tasks:
    - name: Installer un package
      apt:
        name: "{{ package_name }}"
        state: present
```

Dans cet exemple :
- La variable `package_name` est définie avec la valeur `vim`.
- Ansible utilise `{{ package_name }}` pour installer le package spécifié sans coder son nom directement.

##### [🔙 Retour à la table des matières](#table-matieres)
<a name="types-variables"></a>
### 1.2. **Types de variables dans Ansible :**


1.2.1. **Variables définies dans le playbook :**  
   Définies directement dans le fichier YAML sous `vars:`.
   
1.2.2. **Variables d'inventaire :**  
   Définies dans le fichier `inventory` ou des fichiers de variables associés aux groupes ou aux hôtes individuels.

   **Exemple dans `inventory.ini` :**
   ```ini
   [web]
   node1 ansible_user=root app_port=80
   ```
  
  > 📝 **Exemple de variable d'inventaire :**
  > - `app_port` est une variable d'inventaire définie pour le groupe `web`.
  > - Elle peut être utilisée dans un playbook pour configurer le port d'application.
  > exemple d epartie de playbook qui utilise la variable d'inventaire :
  > ```yaml
  > - name: Installer un serveur web
  >   apt:
  >     name: nginx
  >     state: present
  >   vars:
  >     port: "{{ app_port }}"
  > ```

1.2.3. **Variables par rôle :**  
   Utilisées dans des rôles Ansible pour centraliser les configurations par type de service ou rôle.

   > 📝 **Exemple de variable par rôle :**
   > 
   > Un rôle dans Ansible est comme une "recette" réutilisable qui regroupe des tâches liées. Par exemple, un rôle "web" pourrait contenir tout ce qui est nécessaire pour configurer un serveur web.
   >
   > Les variables par rôle permettent de personnaliser cette "recette". Prenons un exemple simple :
   >
   > Imaginons que nous voulons installer un serveur web sur plusieurs machines, mais avec des ports différents :
   >
   > ```yaml
   > roles/
   >   web/                    # Notre rôle "web"
   >     vars/
   >       main.yml           # Fichier qui contient nos variables
   >         # Contenu : app_port: 80
   >     tasks/ 
   >       main.yml          # Fichier qui contient nos tâches
   >         # Les tâches utilisent {{ app_port }}
   > ```
   >
   > Au lieu de modifier chaque tâche individuellement, on change simplement la variable `app_port` dans le fichier de variables.
   >
   > C'est comme avoir une recette de cuisine où les quantités (les variables) sont séparées des instructions (les tâches) !


1.2.4. **Variables d'environnement :**  

Définies via des fichiers `vars.yml` ou `group_vars`/`host_vars`.

🌟 Voici un exemple  de variables d'environnement :

  
   > 📝 **Exemple de variable d'environnement :**
   > `username` et `password` sont définies dans un fichier `vars.yml`.
   > - Elles sont utilisées dans un playbook pour créer un utilisateur.
   > Voici un exemple de playbook utilisant des variables d'environnement :
   > ```yaml
   > # Dans vars.yml
   > username: admin
   > password: secret123
   >
   > # Dans le playbook
   > - name: Créer un utilisateur
   >   user:
   >     name: "{{ username }}"
   >     password: "{{ password }}"
   > ```



   Les variables d'environnement dans Ansible peuvent être définies dans trois types de fichiers différents : `vars.yml` ou `group_vars`/`host_vars`.

##### 📁 Structure de l'arborescence des variables :
  
  Une arborescence typique pour l'organisation des variables dans un projet Ansible peut être présentée comme suit :

   ```
   projet_ansible/
   ├── inventory.ini                # Inventaire des hôtes
   ├── site.yml                     # Playbook principal
   ├── vars/
   │   ├── all.yml                  # Variables globales
   │   ├── production.yml           # Variables pour l'environnement de production  
   │   └── staging.yml              # Variables pour l'environnement de staging
   ├── group_vars/
   │   ├── all.yml                  # Variables pour tous les groupes
   │   ├── web_servers.yml          # Variables spécifiques aux serveurs web
   │   └── db_servers.yml           # Variables spécifiques aux serveurs de base de données
   └── host_vars/
       ├── web01.yml                # Variables spécifiques à l'hôte web01
       └── db01.yml                 # Variables spécifiques à l'hôte db01
   ```

   Cette structure permet une organisation claire et hiérarchique des variables :
   - `/vars` : Variables générales et par environnement
   - `/group_vars` : Variables par groupe de serveurs
   - `/host_vars` : Variables par serveur individuel


 ### <u style={{color: "red"}}>  1.2.4.1. Le fichier `vars.yml` :</u>
      - C'est un simple fichier qui contient toutes vos variables
      - Comme un carnet où vous notez toutes vos configurations
      - Par exemple : le nom d'utilisateur, les mots de passe, les chemins de fichiers...

   > 📝 **Exemple de fichier vars.yml :**
   > ```yaml
   > # vars.yml
   > app_name: mon_application
   > app_version: 1.0.0
   > app_port: 8080
   > app_path: /var/www/html
   > 
   > # Variables d'environnement
   > env: production
   > debug_mode: false
   > log_level: info
   > ```
   >
   > **Exemple d'utilisation dans un playbook :**
   > ```yaml
   > ---
   > - name: Déployer l'application
   >   hosts: web_servers
   >   vars_files:
   >     - vars.yml
   >   tasks:
   >     - name: Créer le répertoire de l'application
   >       file:
   >         path: "{{ app_path }}"
   >         state: directory
   >         mode: '0755'
   >
   >     - name: Configurer l'environnement
   >       template:
   >         src: app.conf.j2
   >         dest: /etc/app.conf
   >       environment:
   >         APP_ENV: "{{ env }}"
   >         APP_DEBUG: "{{ debug_mode }}"
   >         APP_PORT: "{{ app_port }}"
   > ```



   ### <u style={{color: "red"}}>  1.2.4.2. Le dossier `group_vars` :</u>
      - Contient des fichiers de variables pour chaque groupe de serveurs
      - Par exemple : si vous avez un groupe "serveurs_web", vous aurez un fichier `group_vars/serveurs_web.yml`
      - Pratique pour avoir des configurations différentes selon les groupes de serveurs

   > 📝 **Exemple de variables d'environnement dans group_vars :**
   > ```yaml
   > # Dans group_vars/serveurs_web.yml
   > http_port: 80
   > doc_root: /var/www/html
   > php_version: 8.1
   > 
   > # Variables d'environnement spécifiques
   > env_vars:
   >   APACHE_RUN_USER: www-data
   >   APACHE_RUN_GROUP: www-data
   >   PHP_MEMORY_LIMIT: 256M
   > ```
   >
   > **Exemple d'utilisation dans un playbook :**
   > ```yaml
   > ---
   > - name: Configuration des serveurs web
   >   hosts: serveurs_web
   >   tasks:
   >     - name: Installation d'Apache
   >       apt:
   >         name: apache2
   >         state: present
   >
   >     - name: Configuration du port Apache
   >       template:
   >         src: ports.conf.j2
   >         dest: /etc/apache2/ports.conf
   >       vars:
   >         port: "{{ http_port }}"
   >
   >     - name: Configuration de PHP
   >       template:
   >         src: php.ini.j2
   >         dest: /etc/php/{{ php_version }}/apache2/php.ini
   >       environment: "{{ env_vars }}"
   > ```

   ### <u style={{color: "red"}}>  1.2.4.3. Le dossier `host_vars` :</u>
      - Similaire à `group_vars`, mais pour des serveurs individuels
      - Par exemple : si vous avez un serveur nommé "serveur1", vous aurez un fichier `host_vars/serveur1.yml`
      - Utile quand un serveur spécifique a besoin d'une configuration unique
  
   > 📝 **Exemple de variables dans host_vars :**
   > ```yaml
   > # Dans host_vars/serveur1.yml
   > server_name: serveur1.example.com
   > backup_dir: /data/backups
   > memory_limit: 4G
   > 
   > # Variables d'environnement spécifiques au serveur
   > env_vars:
   >   DB_HOST: localhost
   >   DB_PORT: 5432
   >   DB_NAME: production_db
   >   DB_USER: app_user
   > ```
   >
   > **Exemple d'utilisation dans un playbook :**
   > ```yaml
   > ---
   > - name: Configuration spécifique du serveur
   >   hosts: serveur1
   >   tasks:
   >     - name: Configuration de PostgreSQL
   >       template:
   >         src: postgresql.conf.j2
   >         dest: /etc/postgresql/postgresql.conf
   >       environment: "{{ env_vars }}"
   >
   >     - name: Configuration des sauvegardes
   >       file:
   >         path: "{{ backup_dir }}"
   >         state: directory
   >         mode: '0755'
   >
   >     - name: Configuration du serveur web
   >       template:
   >         src: vhost.conf.j2
   >         dest: /etc/nginx/sites-available/default
   >       vars:
   >         hostname: "{{ server_name }}"
   > ```



##### [🔙 Retour à la table des matières](#table-matieres)
<a name="exemple-pratique"></a>
### 1.3. **Exemple pratique :**
---

**`vars.yml` :**
```yaml
---
username: "admin"
password: "securepass"
```

**Playbook :**
```yaml
---
- name: Exemple avec des variables externes
  hosts: all
  vars_files:
    - vars.yml
  tasks:
    - name: Créer un utilisateur
      user:
        name: "{{ username }}"
        password: "{{ password }}"
```

Dans cet exemple :
- Les variables `username` et `password` sont stockées dans un fichier `vars.yml` pour plus de sécurité et de clarté.

---
#### 🔄 **Résumé des Types spéciaux de variables :**
---
- **`host_vars` :** Variables spécifiques à un hôte particulier.
- **`group_vars` :** Variables spécifiques à un groupe d'hôtes.
- **`facts` :** Ansible génère automatiquement des variables appelées "facts" sur l'état des hôtes.

---
#### 🔄 **Résumé de la théorie des variables :**  
---

Les variables Ansible sont utilisées pour personnaliser les playbooks en remplaçant des valeurs fixes par des valeurs dynamiques. Elles permettent de centraliser les configurations et de rendre les playbooks plus flexibles et réutilisables.
Les variables dans Ansible constituent un élément fondamental pour :

- **Flexibilité** : Adaptation des playbooks à différents environnements
- **Réutilisabilité** : Création de playbooks génériques et adaptables
- **Maintenabilité** : Centralisation des configurations
- **Sécurité** : Séparation des données sensibles du code
- **Organisation** : Structure claire avec `host_vars` et `group_vars`




##### [🔙 Retour à la table des matières](#table-matieres)
<a name="facts"></a>
---
# 02 - **Ansible Facts :**
---

Les **"facts"** Ansible sont des **informations système collectées automatiquement** par Ansible sur les hôtes distants. Ces facts incluent des détails sur le matériel, le système d'exploitation, les adresses IP, la mémoire, le processeur, et bien plus.

### 2.1. **Pourquoi les Facts sont importants ?**
> Imaginez que vous êtes un chef cuisinier qui doit préparer un repas dans différentes cuisines. Avant de commencer à cuisiner, vous devez :
> - Vérifier les équipements disponibles
> - Connaître la taille du réfrigérateur
> - Savoir quel type de four est installé
> - Etc.
>C'est exactement ce que font les **Facts** dans Ansible !

##### [🔙 Retour à la table des matières](#table-matieres)

### 2.2. **Analogie simple :**
Les Facts sont comme une "fiche d'identité automatique" de chaque serveur. Avant d'exécuter vos tâches, Ansible fait un "check-up complet" de la machine et vous donne toutes ces informations.

#### 💡 **Pourquoi c'est super utile ?**
1. **Pas besoin de deviner :** Ansible découvre tout automatiquement
2. **Évite les erreurs :** Vous savez exactement sur quel type de système vous travaillez
3. **Automatisation intelligente :** Vos playbooks peuvent s'adapter automatiquement selon le serveur

##### [🔙 Retour à la table des matières](#table-matieres)

### 2.3. **Exemple :**
Si vous devez installer un package :
- Sur Ubuntu, la commande est `apt`. *Le fact* ici c'est `ansible_facts['os_family']`
- Sur CentOS, la commande est `yum`. *Le fact* ici c'est `ansible_facts['os_family']`

Grâce aux Facts, Ansible sait automatiquement quelle commande utiliser !

#### 🔄 **En résumé :**
Les Facts sont comme un "assistant intelligent" qui :
- Inspecte vos serveurs
- Vous donne toutes les informations importantes
- Vous aide à éviter les erreurs
- Rend vos playbooks plus intelligents



##### [🔙 Retour à la table des matières](#table-matieres)

<a name="definition-facts"></a>
### 2.4. **Comment Ansible collecte les facts :**
- Ansible utilise le **module `setup`** (inclus par défaut) pour collecter ces informations lorsqu'un playbook est exécuté.
- Les facts sont accessibles dans les playbooks via la variable `ansible_facts`.

##### [🔙 Retour à la table des matières](#table-matieres)
<a name="exemple-facts"></a>
### 2.5. **Exemple de facts courants :**

| **Fact**                         | **Description**                        |
|-----------------------------------|-----------------------------------------|
| `ansible_facts['os_family']`      | Famille du système d'exploitation (Debian, RedHat, etc.) |
| `ansible_facts['distribution']`   | Nom de la distribution Linux (Ubuntu, CentOS, etc.)     |
| `ansible_facts['hostname']`       | Nom de l'hôte                              |
| `ansible_facts['default_ipv4']`   | Adresse IP par défaut                    |
| `ansible_facts['memtotal_mb']`    | Mémoire totale en Mo                      |


##### [🔙 Retour à la table des matières](#table-matieres)
<a name="exemple-utilisation-playbook"></a>
### 2.6. **Exemple d'utilisation dans un playbook :**

```yaml
- name: Affiche le nom du système d'exploitation
  hosts: all
  tasks:
    - name: Print OS family
      debug:
        msg: "Cette machine utilise {{ ansible_facts['os_family'] }}"
```
- Ici, Ansible affichera la famille du système d'exploitation de chaque hôte.


##### [🔙 Retour à la table des matières](#table-matieres)

<a name="desactivation-collecte-facts"></a>
### 2.7. **Désactivation de la collecte des facts :**
Si la collecte automatique n'est pas nécessaire, elle peut être désactivée dans un playbook :
```yaml
gather_facts: no
```

##### [🔙 Retour à la table des matières](#table-matieres)

<a name="utilisation-facts"></a>
### 2.8. **Utilisation des facts :**
- Les facts sont très utiles pour écrire des playbooks dynamiques et adaptés aux caractéristiques des hôtes cibles (exemple : installer des packages différents selon l'OS).
- Ils permettent également de collecter des informations de diagnostic sur les hôtes distants.


##### [🔙 Retour à la table des matières](#table-matieres)
<a name="resume-facts"></a>
### 2.9. **Résumé :**
Les facts Ansible sont des informations système collectées automatiquement pour comprendre l’état des machines cibles et personnaliser les tâches selon leurs caractéristiques (comme la famille de l'OS ou la quantité de mémoire).


##### [🔙 Retour à la table des matières](#table-matieres)
---
<a name="registers"></a>
# 03 - **Les Registres dans Ansible :**
---

Les **registres (registers)** dans Ansible sont des variables spéciales utilisées pour **stocker le résultat d'une tâche**. Cela permet de réutiliser ces résultats dans les tâches suivantes pour prendre des décisions ou effectuer des actions conditionnelles.


##### [🔙 Retour à la table des matières](#table-matieres)
<a name="pourquoi-registers"></a>
### 3.1. **Pourquoi utiliser un registre :**
- Pour capturer et réutiliser la sortie d'une commande ou d'un module.
- Pour effectuer des vérifications et exécuter des actions conditionnelles en fonction du résultat d'une tâche.


##### [🔙 Retour à la table des matières](#table-matieres)
<a name="exemple-simple"></a>
### 3.2. **Exemple simple :**

```yaml
- name: Exemple avec registre
  hosts: localhost
  tasks:
    - name: Vérifier si un fichier existe
      stat:
        path: /etc/passwd
      register: file_info

    - name: Afficher l'état du fichier
      debug:
        msg: "Le fichier existe et sa taille est {{ file_info.stat.size }} octets"
      when: file_info.stat.exists
```
> Ici, le registre `file_info` stocke les informations sur le fichier `/etc/passwd`.
Dans l'exemple ci-dessus :


3.2.1. **Première tâche - Collecte d'informations :**
   - Le module `stat` est utilisé pour obtenir des informations sur le fichier `/etc/passwd`
   - La directive `register: file_info` crée une variable nommée `file_info` qui stocke toutes les informations retournées par le module `stat`
   - Ces informations incluent : existence du fichier, taille, permissions, dates de modification, etc.

3.2.2. **Deuxième tâche - Utilisation des informations :**
   - Le module `debug` utilise les informations stockées dans `file_info`
   - La condition `when: file_info.stat.exists` vérifie si le fichier existe avant d'afficher le message
   - L'expression `{{ file_info.stat.size }}` accède à la taille du fichier stockée dans le registre

3.2.3. **Impact et avantages :**
   - Cette approche permet de prendre des décisions basées sur l'état réel du système
   - On peut réutiliser les informations collectées dans plusieurs tâches suivantes
   - Cela rend le playbook plus intelligent et adaptatif aux conditions réelles

3.2.4. **Structure du registre `file_info` :**
Le registre `file_info` créé dans l'exemple précédent aura une structure similaire à :
```yaml
file_info:
  stat:
    exists: true
    size: 1234
    owner: root
    group: root
    mode: 0644
```

##### [🔙 Retour à la table des matières](#table-matieres)
<a name="explication-registers"></a>
### 3.3. **Résumé :**
<span style={{color: 'red', textDecoration: 'underline'}}> **3.3.1. Tâche 1 :**</span>  
   - Le module `stat` vérifie l'état du fichier `/etc/passwd` et stocke les informations dans le registre `file_info`.
   - Ce registre contient des détails sur le fichier (existe-t-il, sa taille, ses permissions, etc.).

<span style={{color: 'red', textDecoration: 'underline'}}> **3.3.2. Tâche 2 :**</span>  
   - Le module `debug` affiche un message indiquant la taille du fichier si celui-ci existe.
   - La condition `when: file_info.stat.exists` vérifie si le fichier est présent.

---

<a name="structure-registers"></a>
### **Structure d'un registre :**
Un registre stocke généralement des données sous la forme d'un dictionnaire contenant :
- **`stdout`** : La sortie standard de la commande.
- **`stderr`** : La sortie d'erreur. 
- **`rc`** : Le code de retour de la commande (0 si la commande a réussi).
- **Autres détails** : Selon le module utilisé (`stat`, `command`, `copy`, etc.).

Par exemple, pour une commande `ls -l /etc/passwd` :
```yaml
result:
  stdout: |-
    -rw-r--r-- 1 root root 1234 Jan 1 12:34 /etc/passwd
  stderr: ''
  rc: 0
```
Ici, `result` est un registre qui stocke les résultats de la commande `ls -l /etc/passwd`.
> - `stdout` contient la sortie de la commande
> - `stderr` est vide car la commande n'a pas généré d'erreur
> - `rc` est 0 car la commande s'est exécutée avec succès

  
##### [🔙 Retour à la table des matières](#table-matieres)
<a name="exemple-registers"></a>
### 3.4. **Exemple avec une commande ad hoc :**

```yaml
- name: Exécuter une commande et enregistrer le résultat
  hosts: all
  tasks:
    - name: Vérifier la date
      command: date
      register: result

    - name: Afficher la sortie
      debug:
        msg: "La date actuelle sur {{ inventory_hostname }} est : {{ result.stdout }}"
```

- Ici, la commande `date` est exécutée sur chaque hôte, et la sortie est enregistrée dans `result.stdout`, puis affichée.

##### [🔙 Retour à la table des matières](#table-matieres)
<a name="exemple-simplifie-registre"></a>
### 3.5. **Exemple simplifié avec un registre :**

Imaginons que vous vouliez vérifier si un utilisateur existe sur votre système :

```yaml
- name: Vérifier si un utilisateur existe
  hosts: localhost
  tasks:
    - name: Vérifier si l'utilisateur 'admin' existe
      command: id admin
      register: user_info
```

- Si l'utilisateur existe, `user_info.rc` sera 0.
- Si l'utilisateur n'existe pas, `user_info.rc` sera 1.



##### [🔙 Retour à la table des matières](#table-matieres)
<a name="utilisation-registers"></a>
### 3.6. **Utilisation typique des registres :**
- Vérification de l'existence de fichiers ou répertoires.
- Contrôle du résultat d'une commande système (`command`, `shell`, etc.).
- Vérification des mises à jour logicielles.
- Capturer des informations pour des boucles ou des conditions.


##### [🔙 Retour à la table des matières](#table-matieres)
<a name="resume-registers"></a>
### 3.7. **Résumé :**
Les registres dans Ansible servent à stocker les résultats des tâches pour les utiliser dans des tâches suivantes. Ils permettent de rendre les playbooks plus dynamiques en fonction de la sortie des actions exécutées sur les hôtes.


##### [🔙 Retour à la table des matières](#table-matieres)
---
<a name="differences"></a>
# 04 - **Différences entre Registres et Facts dans Ansible :**
---

| **Aspect**        | **Registres (Registers)** | **Facts**                            |
|-------------------|--------------------------|--------------------------------------|
| **Définition**     | Variables qui stockent le **résultat d'une tâche** spécifique dans un playbook. | Informations **collectées automatiquement** par Ansible sur l'hôte distant (système, réseau, etc.). |
| **Source**         | Résultats générés par un module ou une commande exécutée dans une tâche Ansible. | Données collectées par le module `setup` d'Ansible lors du démarrage d'un playbook. |
| **Utilisation**    | Utilisé pour capturer des sorties de commandes (`stdout`, `rc`, etc.) afin d'exécuter des actions conditionnelles ou afficher des résultats. | Utilisé pour adapter les playbooks aux caractéristiques des hôtes distants (distribution Linux, mémoire, CPU, etc.). |
| **Exemple de module** | Modules comme `command`, `shell`, `stat`, `yum`, etc. | Module `setup` (par défaut), qui génère des variables de facts. |
| **Exemple d'accès** | `{{ result.stdout }}` (pour afficher la sortie d'une commande) | `{{ ansible_facts['os_family'] }}` (pour vérifier la famille de l'OS) |
| **Dynamique ou Statique** | Dynamique : les registres sont créés pendant l'exécution d'une tâche. | Automatique : les facts sont collectés au début de l'exécution du playbook (sauf si `gather_facts: no`). |
| **Exemple typique** | Vérifier si un fichier existe avec `stat` et enregistrer sa taille : `register: file_info`. | Adapter l'installation d'un package en fonction de la distribution Linux : `when: ansible_facts['distribution'] == 'Ubuntu'`. |

---

### **Exemple pratique pour illustrer la différence :**

**4.1. Registres :**
   ```yaml
   - name: Vérifier si un fichier existe
     stat:
       path: /tmp/testfile.txt
     register: file_info

   - name: Afficher la taille du fichier
     debug:
       msg: "Taille : {{ file_info.stat.size }} octets"
     when: file_info.stat.exists
   ```
   - Ici, `file_info` est un registre qui stocke le résultat du module `stat`.

**4.2. Facts :**
   ```yaml
   - name: Afficher la distribution Linux
     debug:
       msg: "Cette machine utilise {{ ansible_facts['distribution'] }}"
   ```
   - Ici, `ansible_facts['distribution']` est une fact générée automatiquement par Ansible pour identifier la distribution de l'hôte.


##### [🔙 Retour à la table des matières](#table-matieres)
### 4.3. **Résumé :**
- Les **registres** stockent les **résultats dynamiques** des tâches exécutées pendant le playbook.
- Les **facts** sont des **informations système collectées automatiquement** sur les hôtes avant l'exécution des tâches pour adapter le playbook aux spécificités de chaque machine.

