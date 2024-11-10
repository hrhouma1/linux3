# 🖥️ Déployer une Infrastructure Docker avec Ansible – Tutoriel Complet Corrigé

Ce tutoriel vous guidera pas à pas dans la configuration d'une infrastructure Docker contenant plusieurs conteneurs de différentes distributions Linux (Ubuntu, Debian, AlmaLinux). Vous apprendrez à utiliser Ansible pour automatiser l'installation et la configuration d'Apache sur ces conteneurs, tout en évitant les erreurs courantes. Toutes les erreurs précédemment rencontrées ont été corrigées pour assurer un déploiement fluide.

---

## 📋 Table des Matières

1. [Schéma de l'Infrastructure](#schema)
2. [Étape 1 : Installer Docker et Docker Compose](#etape1)
3. [Étape 2 : Créer et Démarrer les Conteneurs](#etape2)
4. [Étape 3 : Configurer l'Accès SSH pour Ansible](#etape3)
5. [Étape 4 : Créer l'Inventaire Ansible](#etape4)
6. [Étape 5 : Écrire le Playbook Ansible](#etape5)
7. [Étape 6 : Exécuter le Playbook](#etape6)
8. [Étape 7 : Vérifier le Déploiement](#etape7)
9. [Chapitre 2 : Gestion Avancée avec Ansible](#chapitre2)
10. [Conclusion](#conclusion)

---

<a name="schema"></a>
## 🖥️ Schéma de l'Infrastructure

```plaintext
                             Ubuntu Desktop
                           ┌─────────────────┐
                           │  CONTROL NODE   │
                           │ (Ansible Master)│
                           └────────┬────────┘
                                    │
        ┌────────┬────────┬────────┬────────┬────────┬────────┐
        │        │        │        │        │        │        │
    ┌───┴───┐┌───┴───┐┌───┴───┐┌───┴───┐┌───┴───┐┌───┴───┐
    │ Node1 ││ Node2 ││ Node3 ││ Node4 ││ Node5 ││ Node6 │
    │Ubuntu ││Debian ││AlmaLnx││AlmaLnx││Ubuntu ││Ubuntu │
    └───────┘└───────┘└───────┘└───────┘└───────┘└───────┘
```

---

<a name="etape1"></a>
## 🌍 Étape 1 : Installer Docker et Docker Compose

Sur votre machine de contrôle (Ubuntu Desktop), exécutez les commandes suivantes pour installer Docker et Docker Compose :

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release git

# Ajouter la clé GPG officielle de Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Ajouter le dépôt de Docker aux sources APT
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Installer Docker Engine et Docker Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Vérifier l'installation
docker version
docker compose version
```

**Ajouter votre utilisateur au groupe docker :**

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

<a name="etape2"></a>
## 🗄️ Étape 2 : Créer et Démarrer les Conteneurs

### 2.1. Créer un Répertoire de Travail

Créez un nouveau répertoire pour votre projet et accédez-y :

```bash
mkdir ansible_project
cd ansible_project
```

### 2.2. Créer le Fichier `docker-compose.yml`

Créez le fichier `docker-compose.yml` qui définira les services Docker :

```bash
nano docker-compose.yml
```

### 2.3. Contenu du Fichier `docker-compose.yml`

Copiez et collez le contenu suivant dans le fichier, en veillant à ce que la configuration pour les conteneurs AlmaLinux soit correcte :

```yaml
version: '3'

services:
  node1:
    image: ubuntu:latest
    container_name: node1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.2
    command: /bin/bash -c "apt update && apt install -y openssh-server apache2 && service ssh start && service apache2 start && tail -f /dev/null"
    expose:
      - "22"
      - "80"

  node2:
    image: debian:latest
    container_name: node2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.3
    command: /bin/bash -c "apt update && apt install -y openssh-server python3 apache2 && service ssh start && service apache2 start && tail -f /dev/null"
    expose:
      - "22"
      - "80"

  node3:
    image: almalinux:latest
    container_name: node3
    networks:
      ansible_network:
        ipv4_address: 172.20.0.4
    command: /bin/bash -c "yum update -y && yum install -y openssh-server passwd httpd && echo 'root:root' | chpasswd && ssh-keygen -A && /usr/sbin/sshd && /usr/sbin/httpd -D FOREGROUND"
    expose:
      - "22"
      - "80"

  node4:
    image: almalinux:latest
    container_name: node4
    networks:
      ansible_network:
        ipv4_address: 172.20.0.5
    command: /bin/bash -c "yum update -y && yum install -y openssh-server passwd httpd && echo 'root:root' | chpasswd && ssh-keygen -A && /usr/sbin/sshd && /usr/sbin/httpd -D FOREGROUND"
    expose:
      - "22"
      - "80"

  node5:
    image: ubuntu:latest
    container_name: node5
    networks:
      ansible_network:
        ipv4_address: 172.20.0.6
    command: /bin/bash -c "apt update && apt install -y openssh-server apache2 && service ssh start && service apache2 start && tail -f /dev/null"
    expose:
      - "22"
      - "80"

  node6:
    image: ubuntu:latest
    container_name: node6
    networks:
      ansible_network:
        ipv4_address: 172.20.0.7
    command: /bin/bash -c "apt update && apt install -y openssh-server apache2 && service ssh start && service apache2 start && tail -f /dev/null"
    expose:
      - "22"
      - "80"

networks:
  ansible_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
```

**Explication des Modifications :**

- **Pour tous les conteneurs Ubuntu/Debian :**
  - Installation de `apache2` directement dans le conteneur.
  - Démarrage du service `apache2` avec `service apache2 start`.
- **Pour les conteneurs AlmaLinux :**
  - Démarrage de `httpd` en premier plan avec `/usr/sbin/httpd -D FOREGROUND`.

### 2.4. Démarrer les Conteneurs

Exécutez la commande suivante pour démarrer tous les conteneurs en arrière-plan :

```bash
docker-compose up -d
```

Vérifiez que les conteneurs sont en cours d'exécution :

```bash
docker ps
```

---

<a name="etape3"></a>
## 🔑 Étape 3 : Configurer l'Accès SSH pour Ansible

### 3.1. Générer une Clé SSH (si elle n'existe pas)

Générez une clé SSH sans phrase de passe :

```bash
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa
```

### 3.2. Copier la Clé Publique vers Chaque Conteneur

Pour les conteneurs basés sur **Ubuntu** et **Debian** (`node1`, `node2`, `node5`, `node6`) :

```bash
for i in 1 2 5 6; do
  docker exec -it node$i mkdir -p /root/.ssh
  docker cp ~/.ssh/id_rsa.pub node$i:/root/.ssh/authorized_keys
  docker exec -it node$i chmod 600 /root/.ssh/authorized_keys
done
```

Pour les conteneurs basés sur **AlmaLinux** (`node3`, `node4`) :

```bash
for i in 3 4; do
  docker exec -it node$i mkdir -p /root/.ssh
  docker cp ~/.ssh/id_rsa.pub node$i:/root/.ssh/authorized_keys
  docker exec -it node$i chmod 600 /root/.ssh/authorized_keys
done
```

### 3.3. Vérifier la Connexion SSH pour Chaque Conteneur

**Supprimer les anciennes entrées d'hôtes connus pour éviter les conflits :**

```bash
ssh-keygen -R 172.20.0.2
ssh-keygen -R 172.20.0.3
ssh-keygen -R 172.20.0.4
ssh-keygen -R 172.20.0.5
ssh-keygen -R 172.20.0.6
ssh-keygen -R 172.20.0.7
```

Ensuite, vérifiez la connexion SSH :

```bash
for i in {2..7}; do
  IP=172.20.0.$i
  ssh -o StrictHostKeyChecking=no root@$IP exit
done
```

---

<a name="etape4"></a>
## 📜 Étape 4 : Créer l'Inventaire Ansible

Créez un fichier `inventory.ini` dans votre répertoire de travail :

```bash
nano inventory.ini
```

Ajoutez le contenu suivant :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root
node3 ansible_host=172.20.0.4 ansible_user=root
node4 ansible_host=172.20.0.5 ansible_user=root
node5 ansible_host=172.20.0.6 ansible_user=root
node6 ansible_host=172.20.0.7 ansible_user=root
```

---

<a name="etape5"></a>
## 🎯 Étape 5 : Écrire le Playbook Ansible

Créez un fichier `playbook.yml` :

```bash
nano playbook.yml
```

Ajoutez le contenu suivant :

```yaml
---
- name: Configure Apache on Ubuntu and Debian Containers
  hosts: node1,node2,node5,node6
  become: yes
  tasks:
    - name: Create index.html
      copy:
        content: "<h1>Bienvenue sur votre serveur web Ubuntu/Debian dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html
      notify:
        - Restart Apache

  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted

- name: Configure Apache on AlmaLinux Containers
  hosts: node3,node4
  become: yes
  tasks:
    - name: Create index.html
      copy:
        content: "<h1>Bienvenue sur votre serveur web AlmaLinux dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html
      notify:
        - Restart Apache

  handlers:
    - name: Restart Apache
      service:
        name: httpd
        state: restarted
```

**Explications :**

- **Pour les conteneurs Ubuntu/Debian :**
  - Nous créons le fichier `index.html`.
  - Nous utilisons un handler pour redémarrer le service `apache2` après la modification.
- **Pour les conteneurs AlmaLinux :**
  - Même processus, mais le service est `httpd`.

---

<a name="etape6"></a>
## 🚀 Étape 6 : Exécuter le Playbook

Lancez le playbook pour configurer Apache dans les conteneurs :

```bash
ansible-playbook -i inventory.ini playbook.yml
```

**Note :** Si vous rencontrez des avertissements concernant le chemin de l'interpréteur Python, vous pouvez spécifier l'interpréteur Python pour chaque hôte dans votre inventaire :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node2 ansible_host=172.20.0.3 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node3 ansible_host=172.20.0.4 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node4 ansible_host=172.20.0.5 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node5 ansible_host=172.20.0.6 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node6 ansible_host=172.20.0.7 ansible_user=root ansible_python_interpreter=/usr/bin/python3
```

---

<a name="etape7"></a>
## 🔎 Étape 7 : Vérifier le Déploiement

### 7.1. Obtenir les Adresses IP des Conteneurs

Exécutez la commande suivante pour afficher les adresses IP de tous les conteneurs :

```bash
for i in {2..7}; do
  echo "node$(($i-1)) IP: 172.20.0.$i"
done
```

### 7.2. Vérifier l'Accès aux Serveurs Web

Vous pouvez utiliser `curl` pour vérifier que les serveurs web répondent correctement :

```bash
for i in {2..7}; do
  IP=172.20.0.$i
  echo "Testing node$(($i-1)) at $IP:"
  curl http://$IP
  echo -e "\n------------------------\n"
done
```

### 7.3. Accéder aux Serveurs Web via un Navigateur

Ouvrez votre navigateur et accédez aux adresses suivantes :

- **Pour node1 à node6** :

  ```
  http://172.20.0.2
  http://172.20.0.3
  http://172.20.0.4
  http://172.20.0.5
  http://172.20.0.6
  http://172.20.0.7
  ```

**Note Importante :** Assurez-vous que votre machine hôte peut accéder au réseau `172.20.0.0/24`. Si ce n'est pas le cas, vous devrez peut-être configurer le port forwarding ou utiliser les adresses IP de la machine hôte pour accéder aux conteneurs.

### 7.4. Résultats Attendus

- **Pour les conteneurs Ubuntu et Debian (`node1`, `node2`, `node5`, `node6`)** :

  Vous devriez voir la page avec le message :

  ```html
  <h1>Bienvenue sur votre serveur web Ubuntu/Debian dans un conteneur Docker !</h1>
  ```

- **Pour les conteneurs AlmaLinux (`node3`, `node4`)** :

  Vous devriez voir la page avec le message :

  ```html
  <h1>Bienvenue sur votre serveur web AlmaLinux dans un conteneur Docker !</h1>
  ```

---

<a name="chapitre2"></a>
# 📘 Chapitre 2 : Gestion Avancée avec Ansible

Dans ce chapitre, nous allons approfondir l'utilisation d'Ansible en organisant nos conteneurs en groupes dans l'inventaire, en exécutant des commandes ciblées, et en effectuant des tâches spécifiques sur ces groupes. Cela nous permettra de gérer efficacement notre infrastructure et de pratiquer avec des commandes Ansible supplémentaires.

---

## 📝 Étape 1 : Créer un Inventaire Avancé avec des Groupes

Nous allons modifier notre fichier `inventory.ini` pour ajouter des groupes.

```bash
nano inventory.ini
```

Contenu mis à jour de `inventory.ini` :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node2 ansible_host=172.20.0.3 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node3 ansible_host=172.20.0.4 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node4 ansible_host=172.20.0.5 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node5 ansible_host=172.20.0.6 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node6 ansible_host=172.20.0.7 ansible_user=root ansible_python_interpreter=/usr/bin/python3

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

---

## 📝 Étape 2 : Lister les Hôtes par Groupe

### 2.1. Lister les Hôtes du Groupe `web`

```bash
ansible web -i inventory.ini --list-hosts
```

### 2.2. Lister les Hôtes du Groupe `mail`

```bash
ansible mail -i inventory.ini --list-hosts
```

### 2.3. Lister Tous les Hôtes Définis

```bash
ansible all -i inventory.ini --list-hosts
```

### 2.4. Lister les Détails d'un Hôte Spécifique (`node1`)

```bash
ansible node1 -i inventory.ini --list-hosts
```

---

## 📝 Étape 3 : Tester la Connectivité et Exécuter des Commandes

### 3.1. Tester la Connectivité avec `ping` pour Tous les Conteneurs

```bash
ansible all -m ping -i inventory.ini
```

### 3.2. Afficher la Date Actuelle sur `node1`

```bash
ansible node1 -m command -a "date" -i inventory.ini
```

### 3.3. Afficher la Date sur Tous les Conteneurs

```bash
ansible all -m command -a "date" -i inventory.ini
```

---

## 📝 Étape 4 : Exécuter des Actions Spécifiques sur des Groupes

### 4.1. Redémarrer le Service Apache sur le Groupe `web`

Nous allons utiliser une condition pour gérer les différents noms de service selon la distribution.

**Créer un playbook `restart_apache.yml` :**

```bash
nano restart_apache.yml
```

Contenu du playbook :

```yaml
---
- name: Restart Apache Service on Web Servers
  hosts: web
  become: yes
  tasks:
    - name: Restart apache2 on Debian-based systems
      service:
        name: apache2
        state: restarted
      when: ansible_facts['os_family'] == 'Debian'

    - name: Restart httpd on RedHat-based systems
      service:
        name: httpd
        state: restarted
      when: ansible_facts['os_family'] == 'RedHat'
```

**Exécuter le playbook :**

```bash
ansible-playbook -i inventory.ini restart_apache.yml
```

**Note :** Dans notre cas, le groupe `web` contient `node1` et `node5`, qui sont des conteneurs Ubuntu, donc seule la tâche pour les systèmes basés sur Debian sera exécutée.

### 4.2. Vérifier l'Uptime sur le Groupe `mail`

```bash
ansible mail -m command -a "uptime" -i inventory.ini
```

**Note :** Si vous rencontrez une erreur sur `node4` (AlmaLinux) indiquant que la commande `uptime` n'existe pas, c'est parce que le paquet `procps` n'est pas installé.

**Installer `procps` sur les conteneurs AlmaLinux :**

```bash
ansible mail -m yum -a "name=procps-ng state=present" -i inventory.ini --limit node4
```

**Exécuter à nouveau la commande uptime :**

```bash
ansible mail -m command -a "uptime" -i inventory.ini
```

### 4.3. Vérifier l'Utilisation du Disque sur le Groupe `database`

```bash
ansible database -m command -a "df -h" -i inventory.ini
```

---

## 📝 Étape 5 : Exercices Pratiques Supplémentaires

### 5.1. Installer un Paquet sur un Groupe Spécifique

**Objectif :** Installer `vim` sur tous les conteneurs du groupe `database`.

**Créer un playbook `install_vim.yml` :**

```bash
nano install_vim.yml
```

Contenu du playbook :

```yaml
---
- name: Install Vim on Database Servers
  hosts: database
  become: yes
  tasks:
    - name: Install vim on Debian-based systems
      apt:
        name: vim
        state: present
      when: ansible_facts['os_family'] == 'Debian'

    - name: Install vim-enhanced on RedHat-based systems
      yum:
        name: vim-enhanced
        state: present
      when: ansible_facts['os_family'] == 'RedHat'
```

**Exécuter le playbook :**

```bash
ansible-playbook -i inventory.ini install_vim.yml
```

### 5.2. Créer un Utilisateur sur Tous les Conteneurs

**Objectif :** Créer un utilisateur nommé `deploy` avec le shell `/bin/bash`.

```bash
ansible all -m user -a "name=deploy shell=/bin/bash state=present" -i inventory.ini
```

### 5.3. Modifier les Permissions d'un Fichier sur le Groupe `web`

**Objectif :** Modifier les permissions du fichier `/var/www/html/index.html` pour qu'il soit accessible en lecture et écriture par le propriétaire uniquement.

```bash
ansible web -m file -a "path=/var/www/html/index.html mode=600" -i inventory.ini
```

---

<a name="conclusion"></a>
## 🎯 Conclusion

Dans ce tutoriel, nous avons :

- **Installé Docker et Docker Compose** sur votre machine de contrôle Ubuntu.
- **Créé et démarré plusieurs conteneurs Docker** avec différentes distributions Linux.
- **Configuré l'accès SSH** pour permettre à Ansible de se connecter à chaque conteneur.
- **Écrit un inventaire Ansible** pour gérer les conteneurs.
- **Écrit et exécuté des playbooks Ansible** pour installer et configurer Apache sur les conteneurs.
- **Organisé les hôtes en groupes** dans l'inventaire Ansible.
- **Exécuté des commandes ciblées** sur des groupes d'hôtes.

---

## 🛠️ Conseils Supplémentaires

- **Gestion des Services dans les Conteneurs Docker** : Dans un conteneur Docker, il est préférable d'exécuter un seul processus principal. Si vous avez besoin d'exécuter plusieurs services, envisagez d'utiliser des images de base conçues pour cela ou d'utiliser des gestionnaires de processus comme `supervisord`.
- **Sécurité** : N'oubliez pas de sécuriser vos conteneurs en changeant les mots de passe par défaut et en utilisant des clés SSH sécurisées.
- **Nettoyage** : Pour arrêter et supprimer les conteneurs et les réseaux créés, utilisez :

  ```bash
  docker-compose down
  ```

- **Logs et Dépannage** : Utilisez `docker logs <container_name>` pour consulter les journaux des conteneurs en cas de problème.

---

## 📚 Ressources Utiles

- [Documentation Docker](https://docs.docker.com/)
- [Documentation Ansible](https://docs.ansible.com/)
- [Meilleures Pratiques pour les Conteneurs Docker](https://docs.docker.com/develop/dev-best-practices/)

---

## 💡 Conseils Pratiques

- **Utiliser le Mode Verbose** : Ajoutez `-v`, `-vv`, ou `-vvv` pour augmenter la verbosité et obtenir plus d'informations lors de l'exécution des commandes.
- **Tester en Mode Simulation** : Utilisez l'option `--check` pour effectuer une simulation des changements sans les appliquer réellement.
- **Gérer les Différences de Plateforme** : Utilisez des conditions (`when`) dans vos playbooks pour gérer les différences entre les distributions Linux.

---

## 🙌 Félicitations !

Vous avez maintenant une infrastructure Docker fonctionnelle, automatisée avec Ansible, sans aucune erreur. Vous pouvez continuer à développer cet environnement en ajoutant plus de services, en explorant des rôles Ansible, ou en intégrant cette configuration dans des pipelines CI/CD.

