# 🚀 **Guide d'installation d'Ansible dans la première proposition** 🌐

### *Le guide ultime pour configurer Ansible sur votre réseau Master-Worker avec Ubuntu, CentOS et Windows* 💻🔧

---

Bienvenue dans ce guide. Chaque étape est spécifiquement décrite, y compris **où et comment exécuter chaque commande** pour installer et configurer Ansible dans votre architecture.

---

## 🛠 **Prêt à déployer Ansible ? Suivez chaque étape attentivement !** 👇

---

### **Sommaire**

1. [Installation d'Ansible sur les machines](#9-installation-dansible-sur-les-machines)
2. [Annexe : Résolution des problèmes courants](#annexe--résolution-des-problèmes-courants)
3. [Annexe : Commandes fréquentes d'Ansible](#annexe--commandes-fréquentes-dansible)


---------------
# 9. Installation d'Ansible sur les machines
---------------

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


---------------
# Annexe 1: Résolution des problèmes courants
---------------

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

---------------
# Annexe 2: Commandes fréquentes d'Ansible
---------------

### Commandes de base pour Ansible

1. **Lister l'inventaire** :
   - Exécutez cette commande sur le **Master Node** pour voir la liste des machines définies dans l'inventaire :
   ```bash
   ansible-inventory --list -i /etc/ansible/hosts
   ```


2. **Vérifier la connectivité avec les machines Workers** :
   - Depuis le **Master Node**, exécutez la commande suivante pour vérifier que toutes les machines **Workers** sont accessibles :
   ```bash
   ansible all -m ping
   ```
   Si tout fonctionne, vous recevrez une réponse **pong** de chaque machine.

3. **Exécuter une commande sur un Worker** :
   - Exécutez une commande à distance sur un **Worker Node**. Par exemple, pour lister le contenu du répertoire `/root` de **node01** :
   ```bash
   ansible node01 -m shell -a 'ls /root' --become
   ```

4. **Exécuter un playbook Ansible** :
   - Pour exécuter un playbook que vous avez écrit, comme celui pour mettre à jour tous les **Workers** :
   ```bash
   ansible-playbook playbook.yml
   ```

5. **Installer un paquet sur un Worker** :
   - Par exemple, pour installer **nginx** sur **node01**, utilisez la commande suivante :
   ```bash
   ansible node01 -m apt -a 'name=nginx state=latest' --become
   ```

### Historique des commandes pour des erreurs fréquentes

Si vous rencontrez des problèmes lors de l'exécution de commandes, voici quelques exemples d'exécutions et corrections qui peuvent vous aider à diagnostiquer les erreurs :

```bash
   ansible node4 -m raw -a 'sudo apt-get install python -y' -i inventory
   ansible node4 -m command -i inventory -a 'ls /root'
   ansible-playbook playbook_nmap_node4_create_groups.yaml -i inventory --ask-become-pass
```

---

[Retour en haut](#sommaire)






---------------
# Annexe 3 : Commandes fréquentes d'Ansible et exemples de playbooks
---------------

## 1. **Lister les machines définies dans l'inventaire**

Depuis le **Master Node**, exécutez cette commande pour afficher toutes les machines définies dans votre fichier d'inventaire :
```bash
ansible-inventory --list -i /etc/ansible/hosts
```
Cela affichera une liste des nœuds et de leurs adresses IP configurées dans l'inventaire.

---

## 2. **Vérifier la connectivité avec les machines Workers**

Toujours depuis le **Master Node**, utilisez cette commande pour tester la connectivité entre Ansible et tous les **Workers**. Cette commande utilise le module `ping` d'Ansible :
```bash
ansible all -m ping
```
Si la configuration est correcte, chaque **Worker Node** renverra un message **pong**.

---

## 3. **Exécuter une commande à distance**

Vous pouvez exécuter une commande sur un **Worker Node** spécifique directement depuis le **Master Node**. Par exemple, pour lister les fichiers dans le répertoire `/root` sur **node01** :
```bash
ansible node01 -m shell -a 'ls /root' --become
```
L'option `--become` permet d'exécuter la commande avec des privilèges administratifs (root).

---

## 4. **Installer un paquet sur un Worker**

Ansible permet d'installer des logiciels sur les **Workers** de manière automatisée. Par exemple, pour installer **nginx** sur **node01** :
```bash
ansible node01 -m apt -a 'name=nginx state=latest' --become
```
Pour les **CentOS**, utilisez le module `yum` :
```bash
ansible node02 -m yum -a 'name=httpd state=latest' --become
```

---

## 5. **Écrire et exécuter un playbook Ansible**

### Exemple de playbook simple : Mise à jour des paquets sur tous les Workers

Un **playbook** Ansible est un fichier YAML contenant des instructions pour automatiser des tâches sur plusieurs machines. Voici un exemple pour mettre à jour tous les paquets sur vos **Workers**.

1. **Créer le fichier playbook.yml** sur le **Master Node** :
   - Tapez cette commande pour créer le fichier :
   ```bash
   nano ~/playbook.yml
   ```

2. **Copier le contenu suivant** dans le fichier `playbook.yml` :
   ```yaml
   ---
   - name: Mise à jour des Workers
     hosts: workers
     become: yes
     tasks:
       - name: Mettre à jour les paquets sur Ubuntu
         apt:
           update_cache: yes
           upgrade: dist
         when: ansible_distribution == "Ubuntu"
       - name: Mettre à jour les paquets sur CentOS
         yum:
           name: '*'
           state: latest
         when: ansible_distribution == "CentOS"
   ```

   Ce **playbook** mettra à jour les paquets sur tous les **Workers**. Il vérifie le type de distribution (Ubuntu ou CentOS) avant d'exécuter les commandes adaptées.

3. **Enregistrer le fichier** :
   - Appuyez sur `Ctrl + O` pour enregistrer, puis `Ctrl + X` pour quitter.

4. **Exécuter le playbook** :
   - Tapez cette commande pour exécuter le playbook sur tous les **Workers** :
   ```bash
   ansible-playbook ~/playbook.yml
   ```

---

## 6. **Créer un playbook pour installer Apache sur tous les Workers**

Voici un autre exemple de **playbook** qui installe et démarre **Apache** sur tous les **Workers**.

1. **Créer un nouveau fichier playbook** :
   - Toujours sur le **Master Node**, créez un fichier nommé `install_apache.yml` :
   ```bash
   nano ~/install_apache.yml
   ```

2. **Ajouter le contenu suivant** dans le fichier `install_apache.yml` :
   ```yaml
   ---
   - name: Installer Apache sur tous les Workers
     hosts: workers
     become: yes
     tasks:
       - name: Installer Apache sur Ubuntu
         apt:
           name: apache2
           state: present
         when: ansible_distribution == "Ubuntu"
       - name: Installer Apache sur CentOS
         yum:
           name: httpd
           state: present
         when: ansible_distribution == "CentOS"
       - name: Démarrer le service Apache
         service:
           name: "{{ ansible_distribution == 'Ubuntu' | ternary('apache2', 'httpd') }}"
           state: started
   ```

3. **Exécuter le playbook** :
   - Sauvegardez le fichier puis exécutez le playbook avec la commande suivante :
   ```bash
   ansible-playbook ~/install_apache.yml
   ```

   Ce **playbook** installera **Apache** sur tous les **Workers**, que ce soit **Ubuntu** ou **CentOS**, puis démarrera le service.

---

## 7. **Exécuter des commandes avec `ansible.cfg` personnalisé**

Parfois, vous pouvez vouloir utiliser un fichier de configuration **Ansible** spécifique. Voici comment le faire :

1. **Créer un fichier de configuration personnalisé** :
   - Créez un fichier `ansible.cfg` dans votre répertoire actuel :
   ```bash
   nano ~/ansible.cfg
   ```

2. **Ajouter le contenu suivant** au fichier `ansible.cfg` :
   ```ini
   [defaults]
   inventory = /etc/ansible/hosts
   ```

3. **Exécuter une commande en utilisant ce fichier de configuration** :
   - Pour forcer Ansible à utiliser ce fichier, utilisez la commande suivante :
   ```bash
   ANSIBLE_CONFIG=~/ansible.cfg ansible all -m ping
   ```

---

## 8. **Écrire un playbook pour redémarrer les machines Workers**

Un autre exemple de playbook utile est celui permettant de **redémarrer** les machines **Workers** :

1. **Créer un fichier `reboot.yml`** :
   ```bash
   nano ~/reboot.yml
   ```

2. **Ajouter le contenu suivant** au fichier `reboot.yml` :
   ```yaml
   ---
   - name: Redémarrer tous les Workers
     hosts: workers
     become: yes
     tasks:
       - name: Redémarrer la machine
         reboot:
           msg: "Redémarrage initié par Ansible"
           pre_reboot_delay: 5
   ```

3. **Exécuter le playbook** :
   ```bash
   ansible-playbook ~/reboot.yml
   ```

   Ce **playbook** redémarre toutes les machines **Workers** après un délai de 5 secondes.

---

# **Conclusion**

Ces commandes et exemples de **playbooks** fournissent des bases solides pour débuter avec Ansible. Tes étudiants pourront ainsi exécuter des tâches simples comme la gestion des paquets, l'installation de logiciels, ou encore la gestion des services à distance. Si tes étudiants rencontrent des problèmes lors de l'exécution des commandes, ils peuvent consulter les sections **Annexes** pour résoudre les erreurs courantes.







------------
# Références :
--------------

https://stackoverflow.com/questions/63672853/ansible-config-file-not-found-using-defaults
