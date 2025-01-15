---
title: "Chapitre 9 - Exemple exhaustif inventory.ini"
description: "Je vous fournis un exemple exhaustif avec toutes les possibilités (clé SSH, mot de passe, variables avancées) pour gérer efficacement vos configurations Ansible."
emoji: "📖" 
---

# **Exemple exhaustif d'un fichier `inventory.ini`** 



Le fichier `inventory.ini` ci-dessous illustre les différentes possibilités d'authentification (mot de passe, clé SSH), les paramètres avancés, ainsi que la configuration de plusieurs systèmes d'exploitation (Ubuntu, RedHat, Windows).



## 📋 Description des Sections de l'Inventaire

Découvrons ensemble les sections de cet exemple complet de fichier `inventory.ini` qui illustre toutes les possibilités de configuration, notamment *l'authentification par clé SSH, par mot de passe, ainsi que la gestion des variables avancées*.

### 1. Section [web] 
Cette section regroupe les serveurs web avec différentes configurations :
- **Serveurs Ubuntu** (web-ubuntu-01, web-ubuntu-02)
  - Authentification par clé SSH et mot de passe
  - Configuration des ports et droits sudo
- **Serveurs RedHat** (web-redhat-01, web-redhat-02) 
  - Authentification mixte (clé SSH et mot de passe)
  - Configuration de l'interpréteur Python
- **Serveurs Windows** (web-windows-01)
  - Configuration WinRM pour l'accès Windows
  - Authentification par mot de passe

### 2. Section [database] 
Cette section contient les serveurs de bases de données :
- **Serveur Ubuntu** (db-ubuntu-01)
  - Authentification par clé SSH
  - Configuration Python spécifique
- **Serveur RedHat** (db-redhat-01)
  - Authentification par mot de passe
  - Élévation de privilèges configurée
- **Serveur Windows** (db-windows-01)
  - Configuration WinRM sécurisée
  - Authentification administrateur

### 3. Section [mail]
Cette section regroupe les serveurs de messagerie :
- **Serveur Ubuntu** (mail-ubuntu-01)
  - Authentification simple par clé SSH
  - Configuration standard pour serveur mail
- **Serveur RedHat** (mail-redhat-01)
  - Configuration complète avec élévation de privilèges
  - Paramètres de sécurité renforcés
- **Serveur Windows** (mail-windows-01)
  - Configuration WinRM standard
  - Authentification administrateur dédiée

### 4. Section [windows]
Section dédiée aux serveurs Windows spécifiques :
- **Serveurs Web Windows** (win-web-01)
  - Configuration WinRM basique
  - Transport sécurisé basic
- **Serveurs Base de Données Windows** (win-db-01)
  - Configuration WinRM avec transport NTLM
  - Authentification renforcée

### 5. Groupes Implicites
#### Section [all]
- Groupe créé automatiquement par Ansible
- Contient tous les hôtes de l'inventaire
- Permet d'appliquer des configurations globales

#### Section [ungrouped]
- Contient les hôtes qui n'appartiennent à aucun autre groupe
- Créé automatiquement par Ansible
- Utile pour gérer les serveurs non catégorisés

### 6. Groupes Composites Suggérés
#### Section [all_web_and_db:children]
Cette section permet de regrouper logiquement les serveurs web et base de données :
- Inclut tous les membres du groupe [web]
- Inclut tous les membres du groupe [database]
- Facilite l'exécution de tâches communes

#### Section [linux_servers:children]
Regroupe tous les serveurs Linux :
- Inclut les serveurs Ubuntu
- Inclut les serveurs RedHat
- Permet d'appliquer des configurations spécifiques Linux

#### Section [windows_servers:children]
Regroupe tous les serveurs Windows :
- Inclut tous les serveurs Windows de chaque section
- Facilite la gestion des configurations WinRM
- Permet d'appliquer des politiques Windows communes

### 7. Variables et Héritages
- Variables globales définies au niveau [all]
- Variables spécifiques à chaque groupe
- Héritage et surcharge possible entre les groupes
- Priorité suivant la hiérarchie des groupes

Cette structure d'inventaire complète permet une organisation claire et une gestion efficace de tous les types de serveurs, avec une hiérarchie logique et des groupements fonctionnels.




---

```ini
# =============================================================
# Inventaire Ansible complet - Authentification mixte
# Serveurs Linux avec clé SSH et mot de passe, Windows avec WinRM
# =============================================================

# -------- Serveurs Web --------
[web]
# Ubuntu - Authentification par clé SSH
web-ubuntu-01 ansible_host=192.168.10.11 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa ansible_port=22
web-ubuntu-02 ansible_host=192.168.10.12 ansible_user=ubuntu ansible_password=UbuntuPass123 ansible_port=22 ansible_become=True ansible_become_method=sudo

# RedHat - Authentification mixte
web-redhat-01 ansible_host=192.168.10.13 ansible_user=root ansible_python_interpreter=/usr/bin/python3 ansible_ssh_private_key_file=~/.ssh/id_rsa
web-redhat-02 ansible_host=192.168.10.14 ansible_user=root ansible_password=RedHatPass123 ansible_python_interpreter=/usr/bin/python3 ansible_become=True ansible_become_method=sudo ansible_become_user=root

# Windows - Authentification WinRM avec mot de passe
web-windows-01 ansible_host=192.168.10.15 ansible_user=Administrator ansible_password=Password123 ansible_connection=winrm ansible_winrm_transport=basic ansible_winrm_server_cert_validation=ignore

# -------- Serveurs de Base de Données --------
[database]
# Ubuntu - Clé SSH
db-ubuntu-01 ansible_host=192.168.20.11 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa ansible_python_interpreter=/usr/bin/python3

# RedHat - Mot de passe
db-redhat-01 ansible_host=192.168.20.12 ansible_user=root ansible_password=DbRedHatPass ansible_python_interpreter=/usr/bin/python3 ansible_become=True

# Windows - WinRM avec mot de passe
db-windows-01 ansible_host=192.168.20.13 ansible_user=Administrator ansible_password=DbPassw0rd ansible_connection=winrm ansible_winrm_transport=basic ansible_winrm_server_cert_validation=ignore

# -------- Serveurs Mail --------
[mail]
# Ubuntu
mail-ubuntu-01 ansible_host=192.168.30.11 ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

# RedHat
mail-redhat-01 ansible_host=192.168.30.12 ansible_user=root ansible_password=MailRedHatPass123 ansible_python_interpreter=/usr/bin/python3 ansible_become=True

# Windows
mail-windows-01 ansible_host=192.168.30.13 ansible_user=Administrator ansible_password=MailPass123 ansible_connection=winrm ansible_winrm_transport=basic ansible_winrm_server_cert_validation=ignore

# -------- Serveurs Windows spécifiques --------
[windows]
# Serveurs Windows Web
win-web-01 ansible_host=192.168.40.10 ansible_user=Administrator ansible_password=AdminPassw0rd ansible_connection=winrm ansible_winrm_transport=basic ansible_winrm_server_cert_validation=ignore
# Serveurs Windows Base de Données
win-db-01 ansible_host=192.168.40.11 ansible_user=Administrator ansible_password=DbAdminPass ansible_connection=winrm ansible_winrm_transport=ntlm

# -------- Serveurs RedHat spécifiques --------
[redhat]
redhat-01 ansible_host=192.168.50.10 ansible_user=root ansible_ssh_private_key_file=~/.ssh/id_rsa ansible_python_interpreter=/usr/bin/python3
redhat-02 ansible_host=192.168.50.11 ansible_user=root ansible_password=RedHatServerPass ansible_become=True ansible_become_method=sudo ansible_become_user=root ansible_python_interpreter=/usr/bin/python3

# -------- Groupes combinés pour orchestration globale --------
[all_web_and_db:children]
web
database

[all_mail_and_windows:children]
mail
windows

# -------- Variables globales pour tous les hôtes --------
[all:vars]
ansible_ssh_common_args='-o StrictHostKeyChecking=no'
ansible_connection=ssh
ansible_httpapi_use_ssl=True
ansible_httpapi_validate_certs=False
ansible_become=True
ansible_become_method=sudo
ansible_shell_type=bash
```

---

# Explications des éléments clés :

Ce fichier d'inventaire Ansible présente une configuration complète et détaillée pour gérer une infrastructure mixte comprenant :

- Des serveurs web (Ubuntu, RedHat, Windows)
- Des serveurs de base de données (Ubuntu, RedHat, Windows) 
- Des serveurs de messagerie (Ubuntu, RedHat, Windows)
- Des serveurs Windows et RedHat spécifiques

Il illustre différentes méthodes d'authentification (clé SSH, mot de passe), de connexion (SSH, WinRM) et de configuration (variables globales, groupes) couramment utilisées en entreprise.

Les serveurs sont organisés de manière logique en groupes fonctionnels, permettant une gestion granulaire ou globale selon les besoins. Les variables sont définies au niveau des hôtes et des groupes pour une configuration flexible et maintenable.


# 1. **Authentification par clé SSH :**
   - Utilise la variable `ansible_ssh_private_key_file` pour spécifier le fichier de clé privée.
   ```ini
   web-ubuntu-01 ansible_host=192.168.10.11 ansible_ssh_private_key_file=~/.ssh/id_rsa
   ```

# 2. **Authentification par mot de passe :**
   - Utilise la variable `ansible_password` pour spécifier le mot de passe de connexion.
   ```ini
   db-redhat-01 ansible_password=DbRedHatPass
   ```

# 3. **Connexion Windows avec WinRM :**
   - Variables spécifiques à WinRM :
     - `ansible_connection=winrm` : Utilise WinRM pour Windows.
     - `ansible_winrm_transport=basic/ntlm` : Type de transport (basic pour mot de passe simple, ntlm pour Kerberos/NTLM).
     - `ansible_winrm_server_cert_validation=ignore` : Ignorer la validation du certificat.
   ```ini
   web-windows-01 ansible_connection=winrm ansible_winrm_transport=basic
   ```

# 4. **Paramètres globaux (`[all:vars]`) :**
   - `ansible_become=True` : Active l'élévation des privilèges par défaut pour tous les hôtes.
   - `ansible_ssh_common_args='-o StrictHostKeyChecking=no'` : Ignore la vérification des clés SSH lors de la connexion.

---

# **Exécution des commandes :**
- **Ping sur tous les serveurs Linux :**
  ```bash
  ansible -i inventory.ini web,redhat,mail -m ping
  ```

- **Vérification des informations système sur tous les serveurs Windows :**
  ```bash
  ansible -i inventory.ini windows -m win_command -a "systeminfo"
  ```

---
# **Conclusion**
Ce fichier présente un exemple concret d'infrastructure mixte où l'on gère à la fois des serveurs Linux (Ubuntu, RedHat) via authentification par clé SSH ou mot de passe, ainsi que des serveurs Windows utilisant le protocole WinRM. Cette configuration reflète un environnement d'entreprise typique avec différents systèmes d'exploitation et méthodes d'authentification.