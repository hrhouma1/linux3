# Chapitre 1 - Introduction Théorique à Ansible et Pratique 01

Dans ce chapitre, nous allons explorer l'utilisation d'Ansible pour automatiser des tâches de manière efficace. Cette introduction théorique vous permettra de :

- **Comprendre les concepts fondamentaux** de l'automatisation avec Ansible
- **Découvrir comment transformer** la gestion de votre infrastructure IT
- **Maîtriser les bases** que vous soyez débutant ou expérimenté

Ce guide rendra chaque concept simple et accessible, en vous guidant pas à pas dans votre apprentissage d'Ansible.


---

## 📋 Table des Matières 

1. [Introduction à l'Automatisation Informatique](#automatisation-informatique)  
2. [Différence entre Automatisation et Orchestration](#difference-automatisation-orchestration)  
3. [Pourquoi Choisir l'Automatisation ?](#pourquoi-choisir-automatisation)  
4. [Qu'est-ce qu'Ansible ?](#qu-est-ce-qu-ansible)  
5. [Comment Fonctionne Ansible ?](#comment-fonctionne-ansible)  
6. [Exemple Simple avec Ansible](#exemple-simple-ansible)  
7. [Cas d'Utilisation Réel : Une Multinationale Face à un Défi](#cas-utilisation-reel)  
8. [Comparaison avec d'autres Outils](#comparaison-outils)  
9. [Conclusion](#conclusion)
10. [Ressources Complémentaires](#ressources-complementaires)
11. [Annexe - exemple d'inventaire](#annexe) 
---

<a name="automatisation-informatique"></a>
## 1. Introduction à l'Automatisation Informatique  

L'**automatisation informatique** consiste à utiliser des outils et des scripts pour exécuter des tâches répétitives sans intervention humaine. Cela inclut :  

- **L'installation automatique** de logiciels (ex. : installer Apache sur plusieurs serveurs).  
- **La gestion des configurations** pour standardiser les paramétrages.  
- **La mise à jour simultanée** de centaines de serveurs en une seule commande.  

### 🔴 Problèmes rencontrés dans la gestion traditionnelle  

- **Lenteur** : Chaque tâche doit être réalisée manuellement sur chaque machine.  
- **Incohérences** : Un fichier oublié ou une commande mal tapée peut provoquer des erreurs en production.  
- **Complexité** : Gérer plusieurs centaines de serveurs devient rapidement ingérable.  

✅ **Grâce à l'automatisation**, ces problèmes sont résolus de manière rapide et fiable.

#### [↑ Retour à la Table des Matières](#table-des-matières)
---

<a name="difference-automatisation-orchestration"></a>
## 2. Différence entre Automatisation et Orchestration  

Ces deux termes sont souvent confondus, mais ils ont des significations distinctes.

### **Automatisation**  
L'automatisation consiste à exécuter une **tâche individuelle de manière automatique**.  
- **Exemple :** Installer Apache sur un serveur donné.  

### **Orchestration**  
L'orchestration combine plusieurs tâches automatisées pour exécuter un **processus complexe**.  
- **Exemple :** Déployer une application complète nécessitant l’installation d’un serveur web, la configuration de la base de données et l’intégration de certificats SSL.

> **🌟 Analogie :**  
> - Automatisation = préparer un café.  
> - Orchestration = préparer un petit-déjeuner complet (café, toast et jus d'orange).  

####  [↑ Retour à la Table des Matières](#table-des-matières)
---

<a name="pourquoi-choisir-automatisation"></a>
## 3. Pourquoi Choisir l'Automatisation ?  

### **Avantages principaux :**  

1. **⏳ Gain de temps** : Une seule commande permet de configurer des centaines de machines.  
2. **📋 Standardisation** : Les mêmes configurations sont appliquées sur tous les serveurs.  
3. **🔒 Réduction des erreurs** : Moins d'interventions manuelles signifie moins de risques de fautes.  
4. **⚙️ Évolutivité** : Ajoutez facilement des ressources sans multiplier la charge de travail.  
5. **🔍 Conformité** : Maintenir une infrastructure conforme aux politiques de sécurité devient simple.

#### [↑ Retour à la Table des Matières](#table-des-matières)

---

<a name="qu-est-ce-qu-ansible"></a>
## 4. Qu'est-ce qu'Ansible ?  

**Ansible** est un outil d'automatisation open source permettant de gérer, configurer et orchestrer des infrastructures informatiques de manière simple.

### **Caractéristiques principales :**  
- **Sans agent** : Pas besoin d'installer un logiciel sur les machines gérées. Ansible utilise **SSH** pour se connecter.  
- **Lisibilité** : Les playbooks sont écrits en **YAML**, un langage clair et lisible.  
- **Idempotence** : Une tâche ne s'exécute que si elle est nécessaire. Si un logiciel est déjà installé, Ansible ne le réinstalle pas.  
- **Modularité** : Ansible utilise des **modules** pour des tâches précises comme l'installation de paquets, la gestion des utilisateurs ou la configuration réseau.

####  [↑ Retour à la Table des Matières](#table-des-matières)

---

<a name="comment-fonctionne-ansible"></a>
## 5. Comment Fonctionne Ansible ?  

L'architecture d'Ansible est simple :

- **Node de contrôle (Control Node)** : La machine depuis laquelle les commandes Ansible sont exécutées.  
- **Nodes gérés (Managed Nodes)** : Les serveurs ou conteneurs sur lesquels Ansible applique les configurations.  
- **Inventaire** : Un fichier contenant la liste des serveurs à gérer (identifiés par une **adresse IP** ou un **nom DNS**).  
- **Playbook** : Un fichier YAML contenant une **liste de tâches** (ex. : installer Apache, configurer des fichiers, redémarrer un service).  
- **Module** : Composant exécutant une tâche spécifique (ex. `apt` pour Ubuntu/Debian, `yum` pour RedHat/AlmaLinux).

#### [↑ Retour à la Table des Matières](#table-des-matières)

---


<a name="exemple-simple-ansible"></a>
## 6. Exemple Simple avec Ansible  

### **Exemple de Fichier d'Inventaire :**  

Le fichier `inventory.ini` décrit les machines à gérer par Ansible. Voici un exemple basique :

```ini
[web]
server1 ansible_host=192.168.1.10 ansible_user=ubuntu
server2 ansible_host=192.168.1.11 ansible_user=ubuntu

[database]
db1 ansible_host=192.168.1.20 ansible_user=admin
db2 ansible_host=192.168.1.21 ansible_user=admin
```

- **`web`** : Groupe contenant des serveurs web.
- **`database`** : Groupe contenant des bases de données.
- **`ansible_host`** : Adresse IP ou nom d’hôte de la machine cible.
- **`ansible_user`** : Nom d’utilisateur pour la connexion SSH.

---

### **Commande ad-hoc pour tester la connectivité :**  

```bash
ansible all -i inventory.ini -m ping
```
- **`all`** : Exécute la commande sur toutes les machines définies dans l'inventaire.  
- **`-m ping`** : Utilise le module `ping` pour tester la connexion aux machines.

---

### **Exemple de Playbook YAML :**  

Fichier `install-apache.yml` pour installer Apache sur des serveurs Ubuntu appartenant au groupe `web` :  

```yaml
---
- name: Installer Apache sur les serveurs web
  hosts: web
  become: yes
  tasks:
    - name: Installer le paquet Apache
      apt:
        name: apache2
        state: present
```

---

### **Exécution du Playbook :**  

Pour appliquer le playbook sur les serveurs du groupe `web` :

```bash
ansible-playbook -i inventory.ini install-apache.yml
```

#### [↑ Retour à la Table des Matières](#table-des-matières)  

---

<a name="cas-utilisation-reel"></a>
## 7. Cas d'Utilisation Réel : Une Multinationale Face à un Défi  

### **L'histoire de GlobalWeb Inc.**  

GlobalWeb Inc. est une entreprise internationale spécialisée dans les services en ligne. La direction décide de lancer une nouvelle plateforme web, avec des serveurs déployés dans :  
- **Montréal** : 1 serveur  
- **Toronto** : 1 serveur  
- **New York** : 1 serveur  
- **Mexico** : 1 serveur  

#### **Problème sans Ansible :**  
Un administrateur doit configurer manuellement chaque serveur : installer les logiciels, ajuster les configurations et redémarrer les services.  
- **⏱️ Temps estimé :** 2 heures par serveur.  
- **⚠️ Coût humain :** En cas d'erreur, il faut tout reprendre.  

#### **Avec Ansible : Une Révolution !**  

Grâce à Ansible, l'administrateur crée un **playbook** unique avec toutes les étapes nécessaires et exécute une seule commande pour tous les serveurs :

```yaml
---
- name: Déployer la plateforme web sur plusieurs villes
  hosts: all
  become: yes
  tasks:
    - name: Installer Apache
      apt:
        name: apache2
        state: present
    - name: Ajouter un fichier d'accueil
      copy:
        dest: /var/www/html/index.html
        content: "Bienvenue sur le serveur de {{ inventory_hostname }}"
```

```bash
ansible-playbook -i inventory.ini deploy-web.yml
```

#### **Résultat :**  

- **⏳ Temps total :** Quelques minutes pour tous les serveurs.  
- **✔️ Fiabilité :** Toutes les configurations sont uniformes.  
- **🔄 Flexibilité :** Si un serveur est ajouté à Mexico ou Toronto, il suffit de le lister dans l'inventaire.

#### [↑ Retour à la Table des Matières](#table-des-matières)

---

<a name="comparaison-outils"></a>
## 8. Comparaison avec d'autres Outils  

| Fonctionnalité        | Ansible      | Puppet       | Chef        |
|-----------------------|--------------|--------------|-------------|
| **Langage utilisé**   | YAML          | DSL Ruby     | DSL Ruby    |
| **Sans agent**        | ✅            | ❌           | ❌          |
| **Facilité d'utilisation** | ✅      | ⚠️           | ⚠️          |
| **Gestion ad-hoc**    | ✅            | ❌           | ❌          |

#### [Retour à la Table des Matières](#table-des-matières)
---







<a name="pratique-01"></a>
## 9. Pratique 01 - Installation d'Ansible  

9.1. 📦 **Installez** les dépendances nécessaires :
   ```bash
   sudo apt install software-properties-common
   ```
9.2. 🛠️ **Ajoutez le dépôt PPA** pour Ansible et mettez à jour :
   ```bash
   sudo apt-add-repository --yes --update ppa:ansible/ansible
   sudo apt update
   ```
9.3. ✅ **Installez Ansible** :
   ```bash
   sudo apt install ansible
   ```
9.4. 📝 **Vérifiez la version** pour confirmer l'installation :
   ```bash
   ansible --version
   ```
9.5. 📝 **Vérifiez si Ansible est installé et fonctionne correctement sur la machine locale** :
   ```bash
   ansible localhost -m 'ping'
   ```

La commande `ansible localhost -m ping` est une commande ad-hoc de Ansible  qui permet de vérifier si Ansible est installé et fonctionne correctement sur la machine locale (le serveur sur lequel la commande est exécutée).

Je vais faire un décryptage de chaque partie de la commande :

- **`ansible`** : le programme Ansible que vous exécutez.
- **`localhost`** : spécifie que vous voulez exécuter la commande sur la machine locale (celle où la commande est lancée).
- **`-m ping`** : utilise le module `ping` d'Ansible pour vérifier la connectivité avec l'hôte spécifié (ici `localhost`). Ce `ping` n'est pas le même que la commande réseau `ping` ; c'est un module d'Ansible qui envoie une requête pour vérifier que l'hôte est accessible et que l'installation d'Ansible est correctement configurée.

### Exemple de Résultat

Si tout est bien configuré, vous devriez voir un message de type :

```plaintext
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Cela signifie que la connexion avec l'hôte (`localhost`) est réussie et qu'Ansible fonctionne correctement.



9.6. 📡 **Exécutez la commande Ansible pour tester la connectivité avec tous les hôtes** :
   ```bash
   ansible all -m ping -i inventory
   ```




















<a name="conclusion"></a>
## 10. Conclusion  

L'histoire de GlobalWeb Inc. montre comment Ansible permet de gérer des infrastructures globales de manière uniforme et rapide. Dans le prochain chapitre, nous passerons à la pratique en utilisant Docker et Ansible pour automatiser la configuration complète d'un environnement.

#### [↑ Retour à la Table des Matières](#table-des-matières)

---

<a name="ressources-complementaires"></a>
## 📚 Ressources Complémentaires  

- [Documentation Officielle d'Ansible](https://docs.ansible.com/)  
- [Ansible Galaxy - Modules Préconstruits](https://galaxy.ansible.com/)  


<a name="annexe"></a>
## 📚 Annexe - Un exemple d'inventaire à analyser


Je vous propose dans cet annexe  un **exemple détaillé d'un fichier d'inventaire Ansible `inventory.ini`**. Cet exemple illustre une infrastructure complexe avec :

- Des serveurs regroupés par rôles (web, base de données, mail)
- Différents systèmes d'exploitation (**Windows**, **Ubuntu**, **RedHat**)
- Des configurations spécifiques pour chaque type de serveur
- Une organisation hiérarchique avec des groupes parents/enfants

Cet exemple vous permettra de comprendre comment structurer efficacement votre inventaire pour une gestion optimale de votre infrastructure.

---

## Exemple de fichier `inventory.ini`

Ce fichier `inventory.ini` est flexible et prêt pour gérer une infrastructure complexe avec des serveurs de différentes plateformes et rôles. Il illustre comment l'inventaire peut être organisé pour faciliter l'exécution d'actions ciblées ou globales.


```ini
# Serveurs Web répartis sur différentes plateformes
[web]
web-ubuntu-01 ansible_host=192.168.10.11 ansible_user=ubuntu
web-ubuntu-02 ansible_host=192.168.10.12 ansible_user=ubuntu
web-redhat-01 ansible_host=192.168.10.13 ansible_user=root ansible_python_interpreter=/usr/bin/python3
web-redhat-02 ansible_host=192.168.10.14 ansible_user=root ansible_python_interpreter=/usr/bin/python3
web-windows-01 ansible_host=192.168.10.15 ansible_user=Administrator ansible_password=Password123 ansible_connection=winrm ansible_winrm_transport=basic

# Serveurs de Base de Données
[database]
db-ubuntu-01 ansible_host=192.168.20.11 ansible_user=ubuntu
db-redhat-01 ansible_host=192.168.20.12 ansible_user=root ansible_python_interpreter=/usr/bin/python3
db-windows-01 ansible_host=192.168.20.13 ansible_user=Administrator ansible_password=DbPassw0rd ansible_connection=winrm ansible_winrm_transport=basic

# Serveurs Mail
[mail]
mail-ubuntu-01 ansible_host=192.168.30.11 ansible_user=ubuntu
mail-redhat-01 ansible_host=192.168.30.12 ansible_user=root ansible_python_interpreter=/usr/bin/python3
mail-windows-01 ansible_host=192.168.30.13 ansible_user=Administrator ansible_password=MailPass123 ansible_connection=winrm ansible_winrm_transport=basic

# Serveurs Windows spécifiques
[windows]
win-web-01 ansible_host=192.168.40.10 ansible_user=Administrator ansible_password=AdminPassw0rd ansible_connection=winrm ansible_winrm_transport=basic
win-db-01 ansible_host=192.168.40.11 ansible_user=Administrator ansible_password=AdminPassw0rd ansible_connection=winrm ansible_winrm_transport=basic

# Serveurs RedHat spécifiques
[redhat]
redhat-01 ansible_host=192.168.50.10 ansible_user=root ansible_python_interpreter=/usr/bin/python3
redhat-02 ansible_host=192.168.50.11 ansible_user=root ansible_python_interpreter=/usr/bin/python3

# Groupes combinés pour orchestration globale
[all_web_and_db:children]
web
database

[all_mail_and_windows:children]
mail
windows

```

---

## **Explications :**

### **1. Groupe `[web]` :**
Ce groupe contient des serveurs web sur **Ubuntu**, **RedHat**, et **Windows** :
- Les machines **Ubuntu** utilisent `ansible_user=ubuntu`.
- Les machines **RedHat** utilisent `ansible_user=root` et nécessitent une précision pour l'interpréteur Python (`/usr/bin/python3`).
- Les machines **Windows** nécessitent `ansible_connection=winrm` et `ansible_winrm_transport=basic` pour la connexion.

---

### **2. Groupe `[database]` :**
Ce groupe contient des serveurs pour bases de données sur **Ubuntu**, **RedHat**, et **Windows** :
- Chaque machine Windows a son mot de passe spécifique pour l'administrateur.

---

### **3. Groupe `[mail]` :**
Ce groupe contient des serveurs **mail** sur différentes plateformes :
- Exemple : `mail-ubuntu-01` pour une distribution Ubuntu et `mail-windows-01` pour une machine Windows.

---

### **4. Groupe `[windows]` :**
Ce groupe regroupe uniquement des machines sous **Windows** pour des tâches spécifiques.

---

### **5. Groupes combinés `[all_web_and_db:children]` et `[all_mail_and_windows:children]` :**
Les groupes de **children** permettent de combiner plusieurs groupes en un seul pour exécuter des tâches sur plusieurs services :
- `all_web_and_db` regroupe les serveurs **web** et **database**.
- `all_mail_and_windows` regroupe les serveurs **mail** et **Windows**.

---

## **Exemples de commandes associées :**

### **Exécuter un `ping` sur toutes les machines `web` :**

```bash
ansible web -i inventory.ini -m ping
```

### **Exécuter un playbook sur les serveurs Windows uniquement :**

```bash
ansible-playbook -i inventory.ini install-iis.yml --limit windows
```

### **Installer Apache sur tous les serveurs Web et Base de Données (groupe combiné) :**

```bash
ansible-playbook -i inventory.ini install-apache.yml --limit all_web_and_db
```


