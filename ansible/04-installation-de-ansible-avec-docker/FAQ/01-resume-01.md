# 📋 **Configurer Ansible pour Gérer des Conteneurs Docker**

## Objectif

Ce tutoriel vous permet d'utiliser Ansible pour gérer facilement des conteneurs Docker, créant ainsi une solution flexible pour le déploiement de services comme Apache.

## Sommaire

1. [Prérequis](#1-prérequis)
2. [Installer Docker et Docker Compose](#2-installer-docker-et-docker-compose)
3. [Configurer les conteneurs comme nœuds Ansible](#3-configurer-les-conteneurs-comme-noeuds-ansible)
4. [Créer un fichier d'inventaire Ansible](#4-creer-un-fichier-d-inventaire-ansible)
5. [Écrire et exécuter le playbook Ansible pour Apache](#5-ecrire-et-executer-le-playbook-ansible-pour-apache)
6. [Vérifier le déploiement](#6-verifier-le-deploiement)
7. [Références et Ressources](#7-references-et-ressources)

---

## 1. Prérequis

- **Docker** et **Docker Compose** installés sur la machine hôte.
- **Ansible** installé sur la machine de contrôle.
- Compte utilisateur avec les droits `sudo`.

---

## 2. Installer Docker et Docker Compose

Si Docker et Docker Compose ne sont pas encore installés, vous pouvez choisir entre deux méthodes.

### Méthode 1 : Script d'installation Docker

```bash
su
apt update
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
# ou
sh install-docker.sh
docker version
docker compose version
apt install docker-compose -y
```

### Méthode 2 : Installation manuelle de Docker

```bash
# Mettez à jour le système
sudo apt update

# Installez les paquets nécessaires pour Docker
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

# Ajoutez la clé GPG de Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Ajoutez le dépôt Docker
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Installez Docker
sudo apt update
sudo apt install docker-ce -y
```

### Installation de Docker Compose

```bash
# Téléchargez Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Donnez les permissions nécessaires
sudo chmod +x /usr/local/bin/docker-compose

# Vérifiez l’installation
docker-compose --version
```

---

## 3. Configurer les conteneurs comme nœuds Ansible

Nous allons créer un réseau Docker pour que les conteneurs puissent communiquer entre eux, puis démarrer des conteneurs Ubuntu configurés pour accepter les connexions SSH.

### 1. Créer un réseau Docker

```bash
docker network create ansible_network
```

### 2. Démarrer les conteneurs

Créez un fichier `docker-compose.yml` avec le contenu suivant pour définir deux conteneurs Docker.

```yaml
version: '3'
services:
  node1:
    image: ubuntu:latest
    container_name: node1
    networks:
      - ansible_network
    volumes:
      - ./ansible_node1:/ansible_node
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

  node2:
    image: ubuntu:latest
    container_name: node2
    networks:
      - ansible_network
    volumes:
      - ./ansible_node2:/ansible_node
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

networks:
  ansible_network:
    driver: bridge
```

Ensuite, démarrez les conteneurs avec la commande :

```bash
docker-compose up -d
```

Cela crée et démarre deux conteneurs (`node1` et `node2`) avec SSH activé pour les gérer via Ansible.

### 3. Configurer l’accès SSH pour Ansible

Pour qu'Ansible se connecte aux conteneurs, configurez l’accès SSH avec des clés.

1. **Générer une clé SSH** (si elle n'existe pas déjà) sur la machine hôte :

   ```bash
   ssh-keygen -t rsa -b 2048
   ```

2. **Copier la clé publique vers chaque conteneur** :

   ```bash
   docker exec -it node1 bash -c "mkdir -p /root/.ssh && echo '$(cat ~/.ssh/id_rsa.pub)' >> /root/.ssh/authorized_keys"
   docker exec -it node2 bash -c "mkdir -p /root/.ssh && echo '$(cat ~/.ssh/id_rsa.pub)' >> /root/.ssh/authorized_keys"
   ```

3. **Vérifier la connexion SSH** pour chaque conteneur :

   ```bash
   ssh root@$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1)
   ssh root@$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2)
   ```

---

## 4. Créer un fichier d'inventaire Ansible

Créez un fichier `inventory.ini` dans votre dossier de travail pour lister les adresses IP des conteneurs.

Exemple d'inventaire :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root
```

> Remplacez les adresses IP par celles de `node1` et `node2`, obtenues avec la commande `docker inspect`.

---

## 5. Écrire et exécuter le playbook Ansible pour Apache

Nous allons écrire un playbook qui installe et configure Apache sur les conteneurs.

### Créer le playbook `playbook.yml`

```yaml
---
- name: Configure Apache on Docker Containers
  hosts: node_containers
  become: yes
  tasks:
    - name: Update APT package manager
      apt:
        update_cache: yes

    - name: Install Apache2
      apt:
        name: apache2
        state: present

    - name: Start Apache Service
      service:
        name: apache2
        state: started
        enabled: true

    - name: Create index.html
      copy:
        content: "<h1>Bienvenue sur votre serveur web dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html
```

### Exécuter le playbook

Lancez le playbook pour configurer Apache dans les conteneurs :

```bash
ansible-playbook -i inventory.ini playbook.yml
```

---

## 6. Vérifier le déploiement

Pour vérifier que le serveur web Apache fonctionne, récupérez les adresses IP de chaque conteneur et accédez-y depuis un navigateur.

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2
```

Accédez à `http://<IP_du_conteneur>` dans votre navigateur. Vous devriez voir le message **“Bienvenue sur votre serveur web dans un conteneur Docker !”**

---

## 7. Références et Ressources

Pour approfondir votre compréhension de l'automatisation avec Ansible et Docker, voici une sélection de ressources :

1. **[Automation Using Ansible to Configure Docker & Deploy](https://www.linkedin.com/pulse/automation-using-ansible-configure-docker-deploy-ashish-wakchaure/)** - Un guide pratique pour la configuration de Docker avec Ansible.
2. **[Install Docker and Portainer in a VM Using Ansible](https://dev.to/rimelek/install-docker-and-portainer-in-a-vm-using-ansible-21ib)** - Guide pour installer Docker et son interface de gestion.
3. **[How to Use Ansible to Install and Set Up Docker on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-install-and-set-up-docker-on-ubuntu-20-04)** - Tutoriel de DigitalOcean.
4. **[Automate Docker Installation and Configuration with Ansible (YouTube Video)](https://www.youtube.com/watch?v=-sLjgApgWOM&ab_channel=LinuxR)** - Une vidéo de démonstration pour voir Ansible en action.

---

En suivant ce guide, vous avez mis en place une infrastructure légère avec Docker et Ansible, où vous pouvez gérer des cont

