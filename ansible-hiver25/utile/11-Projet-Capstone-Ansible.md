---
title: "Chapitre 11 - Projet Capstone Ansible - Déploiement Automatisé Web/DB"
description: "Projet final qui combine tous les concepts appris pour déployer automatiquement une infrastructure web complète avec base de données"
emoji: "🔧"
---

---
# Projet Ansible Ultime - Déploiement Automatisé Web/DB 
---
<a name="table-des-matières"></a>
## Table des matières

- [01 - Objectif Global du Projet](#01---objectif-global-du-projet)
- [02 - Pourquoi utiliser Ansible ?](#02---pourquoi-utiliser-ansible)
- [03 - Architecture du Projet](#03---architecture-du-projet)
- [04 - Structure du Projet](#04---structure-du-projet)
- [05 - Étapes pour Démarrer](#05---étapes-pour-démarrer)
- [06 - Contenu des Fichiers Clés](#06---contenu-des-fichiers-clés)
- [07 - Gestion des Secrets avec Ansible Vault 🔒](#07---gestion-des-secrets-avec-ansible-vault)
- [08 - Commandes Utiles 💡](#08---commandes-utiles)
- [09 - Extension du Projet : Ajout du Monitoring avec Grafana 🔧](#09---extension-du-projet-ajout-du-monitoring-avec-grafana)
- [10 - Résultat Final 🎯](#10---résultat-final)
- [11 - Pourquoi Ce Projet Est Puissant ? 📈](#11---pourquoi-ce-projet-est-puissant)
- [12 - Rejoignez la Communauté 🔗](#12---rejoignez-la-communauté)
- [13 - Références utiles](#13---références-utiles)
- [14 - Documentation officielle](#14---documentation-officielle)


<a name="01---objectif-global-du-projet"></a>
# 01 - Objectif Global du Projet

Ce projet vous guidera dans la création d'une **infrastructure complète** en utilisant Ansible pour **automatiser le déploiement d'un serveur web et d'une base de données**. Nous allons :
- Automatiser l'installation et la configuration des logiciels.
- Centraliser la gestion des paramètres de configuration (ports, chemins de dossiers, mots de passe).
- Gérer plusieurs serveurs en **une seule commande**, au lieu de tout faire manuellement.

---

<a name="02---pourquoi-utiliser-ansible"></a>
# 02 - Pourquoi utiliser Ansible ?

### **Sans Ansible :**
Vous devez vous connecter manuellement à chaque machine via SSH :
```bash
ssh user@192.168.1.10
sudo apt update && sudo apt install apache2
```
Si vous avez **20 serveurs** ou plus, cela devient une tâche interminable, propice aux erreurs.

### **Avec Ansible :**
Avec **un fichier d’inventaire** et **un playbook**, la même tâche s'exécute sur tous les serveurs :
```bash
ansible-playbook -i inventory.ini site.yml
```
Une seule commande et Ansible fait tout pour vous !

#### [Retour à la table des matières](#table-des-matières)

---

<a name="03---architecture-du-projet"></a>
# 03 - Architecture du Projet
La structure des composants de ce projet Ansible est présentée ci-dessous :

```plaintext
                          +-------------------------+
                          |  Machine de Contrôle     |
                          |     (Ansible)            |
                          |    Ubuntu 22.04          |
                          +-------------------------+
                                     |
                                     |
        -----------------------------------------------------------------
        |                 |                 |                         |
+------------------+ +------------------+ +------------------+ +------------------+
| Serveur Web 1    | | Serveur Web 2    | | Serveur DB 1     | | Serveur DB 2     |
| Ubuntu 20.04     | | Ubuntu 20.04     | | Ubuntu 22.04     | | Ubuntu 22.04     |
| IP : 192.168.1.2 | | IP : 192.168.1.3 | | IP : 192.168.1.4 | | IP : 192.168.1.5 |
+------------------+ +------------------+ +------------------+ +------------------+
```

- **Machine de contrôle** : Le serveur ou PC depuis lequel vous exécutez Ansible (votre poste de travail).
- **Serveurs web (node1, node2)** : Machines sur lesquelles Apache est installé pour héberger des sites web.
- **Serveurs DB (node3, node4)** : Machines sur lesquelles une base de données est installée.

#### [Retour à la table des matières](#table-des-matières)
---

<a name="04---structure-du-projet"></a>
# 04 - Structure du Projet

```plaintext
ansible/
├── inventory.ini          # Liste des serveurs gérés par Ansible
├── site.yml               # Playbook principal qui orchestre tout
├── group_vars/            # Variables par groupe de serveurs
│   └── web.yml            # Variables pour les serveurs web
├── host_vars/             # Variables spécifiques par serveur
│   └── db-ubuntu-01.yml   # Variables pour un serveur DB particulier
├── templates/             # Fichiers de configuration modèles (templates)
│   └── apache.conf.j2     # Template de configuration Apache
├── roles/                 # Dossier des rôles Ansible
│   └── web/               # Rôle pour les serveurs web
│       ├── tasks/
│       │   └── main.yml   # Liste des tâches pour configurer le web
│       └── templates/
│           └── index.html.j2  # Fichier HTML de la page d'accueil
└── README.md              # Documentation du projet
```

### **Explications :**
- **`inventory.ini`** : Liste des serveurs à gérer.
- **`site.yml`** : Fichier principal qui exécute les rôles (comme `web`, `db`).
- **`group_vars/`** : Variables globales pour chaque type de serveur.
- **`host_vars/`** : Variables spécifiques à un serveur donné.
- **`templates/`** : Fichiers modèles pour les configurations (`.j2` pour Jinja2).
- **`roles/`** : Dossiers contenant les rôles (groupes de tâches réutilisables).

#### [Retour à la table des matières](#table-des-matières)
---

<a name="05---étapes-pour-démarrer"></a>
# 05 - Étapes pour Démarrer

### **1. Cloner le Projet**

```bash
git clone https://github.com/votre-utilisateur/projet-ansible.git
cd projet-ansible/ansible
```

#### [Retour à la table des matières](#table-des-matières)
---

### **2. Configurer l’Inventaire**

Le fichier `inventory.ini` contient la liste des serveurs. Exemple :

```ini
[web]
web-ubuntu-01 ansible_host=192.168.1.10 ansible_user=ubuntu

[db]
db-ubuntu-01 ansible_host=192.168.1.20 ansible_user=ubuntu ansible_become=true
```
#### [Retour à la table des matières](#table-des-matières)
---

<a name="06---tester-la-connexion-ssh"></a>
### **3. Tester la Connexion SSH**

```bash
ansible -i inventory.ini all -m ping
```
Sortie attendue :
```bash
web-ubuntu-01 | SUCCESS => { "ping": "pong" }
```

#### [Retour à la table des matières](#table-des-matières)
---

### **4. Exécuter le Playbook**

```bash
ansible-playbook -i inventory.ini site.yml
```

---

<a name="06---contenu-des-fichiers-clés"></a>
# 06 - Contenu des Fichiers Clés

### **1. `site.yml`**

Ce fichier orchestre le déploiement des rôles :
```yaml
- name: Déployer l’infrastructure Web et DB
  hosts: all
  become: true
  roles:
    - web
    - db
```

#### [Retour à la table des matières](#table-des-matières)

---

### **2. `roles/web/tasks/main.yml`**

Configuration du serveur Apache :
```yaml
- name: Installer Apache
  ansible.builtin.apt:
    name: apache2
    state: present

- name: Copier la configuration Apache
  ansible.builtin.template:
    src: apache.conf.j2
    dest: /etc/apache2/sites-available/000-default.conf
    mode: "0644"

- name: Activer et démarrer Apache
  ansible.builtin.service:
    name: apache2
    state: started
    enabled: true
```

#### [Retour à la table des matières](#table-des-matières)

---

### **3. `group_vars/web.yml`**

Variables globales pour tous les serveurs web :
```yaml
apache_port: 80
document_root: /var/www/html
```

#### [Retour à la table des matières](#table-des-matières)

---

<a name="07---gestion-des-secrets-avec-ansible-vault"></a>
# 07 - Gestion des Secrets avec Ansible Vault 🔒

Pour protéger les mots de passe sensibles :

1. **Créer un fichier sécurisé :**
   ```bash
   ansible-vault create secrets.yml
   ```
2. **Ajouter un mot de passe :**
   ```yaml
   vault_db_password: "supersecurise123"
   ```
3. **Exécuter le playbook avec le mot de passe :**
   ```bash
   ansible-playbook -i inventory.ini site.yml --ask-vault-pass
   ```

#### [Retour à la table des matières](#table-des-matières)

---

<a name="08---commandes-utiles"></a>
# 08 - Commandes Utiles 💡 

- **Lister les serveurs du groupe `web` :**
  ```bash
  ansible web -i inventory.ini --list-hosts
  ```
- **Exécuter une commande sur tous les serveurs :**
  ```bash
  ansible all -m command -a "df -h" -i inventory.ini
  ```

#### [Retour à la table des matières](#table-des-matières)

---

<a name="09---extension-du-projet-ajout-du-monitoring-avec-grafana"></a>
# 09 - Extension du Projet : Ajout du Monitoring avec Grafana 🔧

### **1. Créer le Dossier du Rôle Monitoring**

```bash
mkdir -p roles/monitoring/tasks
nano roles/monitoring/tasks/main.yml
```

### **2. Contenu du Fichier `main.yml`**

```yaml
- name: Installer Grafana
  ansible.builtin.apt:
    name: grafana
    state: present

- name: Démarrer le service Grafana
  ansible.builtin.service:
    name: grafana
    state: started
    enabled: true
```

#### [Retour à la table des matières](#table-des-matières)

---

<a name="10---résultat-final"></a>
# 10 - Résultat Final 🎯

Après avoir exécuté le playbook, vous pourrez :
- Accéder aux serveurs web via leur adresse IP :
  ```bash
  curl http://192.168.1.10
  ```
  **Sortie attendue :**
  ```html
  <h1>Bienvenue sur le serveur web !</h1>
  ```

#### [Retour à la table des matières](#table-des-matières)

---

<a name="11---pourquoi-ce-projet-est-puissant"></a>
# 11 - Pourquoi Ce Projet Est Puissant ? 📈

- **Automatisation complète** : déploiement de l’infrastructure en **une seule commande**.
- **Fiabilité** : mêmes configurations sur tous les serveurs, aucune incohérence.
- **Sécurité** : les secrets sont protégés avec `ansible-vault`.
- **Modularité** : possibilité d’ajouter de nouveaux services avec des rôles.

#### [Retour à la table des matières](#table-des-matières)

---

<a name="12---rejoignez-la-communauté"></a>
# 12 - Rejoignez la Communauté 🔗

💬 Rejoignez-nous sur [GitHub Discussions](https://github.com/votre-utilisateur/projet-ansible/discussions).  
⭐ **Laissez une étoile** si ce projet vous aide !

---

<a name="13---références-utiles"></a>
# 11 - Références utiles


- [Expense Project Using Ansible Roles](https://vijaycodes.hashnode.dev/expense-project-using-ansible-roles)
- [Ecrire des Roles Ansible](https://blog.stephane-robert.info/docs/infra-as-code/gestion-de-configuration/ansible/ecrire-roles/)
- [Expense Project Using Ansible Roles](https://vijaycodes.hashnode.dev/expense-project-using-ansible-roles)

# Documentation officielle
- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)
- [Ansible Playbook Documentation](https://docs.ansible.com/ansible/latest/playbook.html)
- [Ansible Vault Documentation](https://docs.ansible.com/ansible/latest/vault.html)
- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)
- [Ansible Playbook Documentation](https://docs.ansible.com/ansible/latest/playbook.html)
- [Ansible Vault Documentation](https://docs.ansible.com/ansible/latest/vault.html)