# 🚀 **Guide d'installation de l'architecture Master-Worker avec Ansible** 🌐

### *Le guide ultime pour configurer Ansible sur votre réseau Master-Worker avec Ubuntu, CentOS et Windows* 💻🔧

---

Bienvenue dans ce guide conçu pour **débutants**. Chaque étape est spécifiquement décrite, y compris **où et comment exécuter chaque commande** pour installer et configurer Ansible dans votre architecture.

---

## 🛠 **Prêt à déployer Ansible ? Suivez chaque étape attentivement !** 👇

---

### **Sommaire**

1. [Installation d'Ansible sur les machines](#9-installation-dansible-sur-les-machines)
2. [Annexe : Résolution des problèmes courants](#annexe--résolution-des-problèmes-courants)
3. [Annexe : Commandes fréquentes d'Ansible](#annexe--commandes-fréquentes-dansible)

---

# 9. Installation d'Ansible sur les machines

### 9.1 Installation d'Ansible sur le **Master Node** (Ubuntu Server)

1. **Connectez-vous au Master Node** :
   - Depuis votre terminal local, connectez-vous via SSH au **Master Node** :
   ```bash
   ssh root@192.168.2.110
   ```

2. **Mettre à jour le système sur le Master Node** :
   - Exécutez les deux commandes suivantes sur le **Master Node** pour mettre à jour la liste des paquets et le système :
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

3. **Installer Ansible sur le Master Node** :
   - Toujours sur le **Master Node**, ajoutez le dépôt officiel d'Ansible et installez Ansible avec les commandes suivantes :
   ```bash
   sudo apt-add-repository --yes --update ppa:ansible/ansible
   sudo apt install ansible -y
   ```

4. **Vérifier l'installation d'Ansible sur le Master Node** :
   - Pour vous assurer qu'Ansible est bien installé, tapez :
   ```bash
   ansible --version
   ```
   Cela affichera la version d'Ansible installée.

### 9.2 Configurer l'inventaire Ansible sur le Master Node

1. **Créer un fichier d'inventaire** :
   - Toujours sur le **Master Node**, créez le fichier d'inventaire Ansible en tapant la commande suivante :
   ```bash
   sudo nano /etc/ansible/hosts
   ```

2. **Ajouter les adresses IP des Workers dans l'inventaire** :
   - Ajoutez les informations des **Workers** dans le fichier que vous venez de créer. Copiez et collez ce texte :
   ```ini
   [workers]
   node01 ansible_host=10.0.2.12
   node02 ansible_host=10.0.2.13
   node03 ansible_host=10.0.2.14
   win-node ansible_host=10.0.2.15
   ```
   - Appuyez sur `Ctrl + O` pour enregistrer, puis sur `Ctrl + X` pour quitter l'éditeur.

### 9.3 Configurer les connexions SSH sans mot de passe

**Objectif :** Permettre au **Master Node** de se connecter automatiquement à chaque **Worker Node** sans mot de passe.

1. **Générer une clé SSH sur le Master Node** :
   - Sur le **Master Node**, exécutez la commande suivante pour créer une clé SSH (laissez les champs vides) :
   ```bash
   ssh-keygen -t rsa
   ```

2. **Copier la clé publique SSH sur chaque Worker Node** :
   - Depuis le **Master Node**, copiez la clé publique sur chaque **Worker** en exécutant ces commandes :
   - Pour **node01** :
     ```bash
     ssh-copy-id root@10.0.2.12
     ```
   - Pour **node02** :
     ```bash
     ssh-copy-id root@10.0.2.13
     ```
   - Pour **node03** :
     ```bash
     ssh-copy-id root@10.0.2.14
     ```
   - Pour **Windows (win-node)**, nous utiliserons **WinRM**, donc cette étape n'est pas nécessaire pour ce nœud.

### 9.4 Installer Python sur les machines Linux Workers

**Objectif :** Installer Python sur les **Workers Linux** pour qu'Ansible puisse les gérer correctement.

1. **Installer Python sur les Workers Ubuntu** :
   - Connectez-vous à chaque **Worker Ubuntu** en utilisant les commandes suivantes dans votre terminal local :
   ```bash
   ssh root@10.0.2.12
   sudo apt install python3 -y
   ```
   Répétez cette commande pour **node03** (Ubuntu) :
   ```bash
   ssh root@10.0.2.14
   sudo apt install python3 -y
   ```

2. **Installer Python sur le Worker CentOS** :
   - Connectez-vous au **Worker CentOS** depuis votre terminal local :
   ```bash
   ssh root@10.0.2.13
   sudo yum install python3 -y
   ```

3. **Vérifier que Python est installé correctement** :
   - Depuis le **Master Node**, exécutez la commande suivante pour vérifier la connectivité avec tous les **Workers** :
   ```bash
   ansible all -m ping
   ```
   Si tout fonctionne bien, chaque machine doit répondre avec un message **pong**.

### 9.5 Configurer Ansible pour Windows Desktop (win-node)

**Objectif :** Configurer **WinRM** pour permettre à Ansible de gérer **Windows 10 Desktop**.

1. **Configurer WinRM sur Windows** :
   - Sur la machine **Windows Desktop**, ouvrez **PowerShell** en tant qu'administrateur et exécutez les commandes suivantes :
   ```powershell
   Enable-PSRemoting -Force
   Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
   Enable-WSManCredSSP -Role Server
   ```

2. **Configurer l'inventaire Ansible pour Windows** :
   - Retournez sur le **Master Node**, et éditez le fichier `/etc/ansible/hosts` pour ajouter les détails de la machine **Windows** :
   ```ini
   [windows]
   win-node ansible_host=10.0.2.15 ansible_user=Administrator ansible_password=VotreMotDePasse ansible_connection=winrm ansible_winrm_transport=credssp ansible_winrm_server_cert_validation=ignore
   ```

### 9.6 Premier Playbook Ansible

**Objectif :** Créer et exécuter un **Playbook Ansible** pour automatiser des tâches sur les **Workers**.

1. **Créer un Playbook Ansible sur le Master Node** :
   - Sur le **Master Node**, créez un fichier `playbook.yml` en tapant :
   ```bash
   nano ~/playbook.yml
   ```

2. **Exemple de Playbook pour mettre à jour tous les Workers** :
   - Copiez le contenu suivant dans le fichier `playbook.yml` :
   ```yaml
   ---
   - name: Mise à jour des Workers
     hosts: workers
     become: yes
     tasks:
       - name: Mettre à jour les paquets (Ubuntu)
         apt:
           update_cache: yes
           upgrade: dist
         when: ansible_distribution == "Ubuntu"
       - name: Mettre à jour les paquets (CentOS)
         yum:
           name: '*'
           state: latest
         when: ansible_distribution == "CentOS"
   ```

3. **Exécuter le Playbook sur le Master Node** :
   - Exécutez la commande suivante sur le **Master Node** pour exécuter le Playbook :
   ```bash
   ansible-playbook playbook.yml
   ```

---

# Annexe : Résolution des problèmes courants

### **Problème :** `ansible config file not found; using defaults`

**Solution** :
1. **Vérifiez où Ansible cherche les fichiers de configuration** :
   - Exécutez cette commande sur le **Master Node** :
   ```bash
   ansible-config dump
   ```
   Ansible va vous montrer les chemins de recherche pour les fichiers de configuration.

2. **Si aucun fichier n'existe, créez-en un** :
   ```bash
   sudo nano /etc/ansible/ansible.cfg
   ```

3. **Ajoutez une configuration minimale** :
   ```ini
   [defaults]
   inventory = /etc/ansible/hosts
   ```

---

# Annexe : Commandes fréquentes d'Ansible

### Commandes de base pour Ansible

1. **Lister l'inventaire** :
   - Exécutez cette commande sur le **Master Node** pour voir la liste des machines définies dans l'inventaire :
   ```bash
   ansible-inventory --list -i /etc/ansible/hosts
   ```

2. **Vérifier la connectivité avec les machines Workers** :
   ```bash


