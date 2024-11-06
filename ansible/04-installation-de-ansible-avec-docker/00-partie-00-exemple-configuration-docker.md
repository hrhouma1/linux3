
#  Configurer Ansible et l'utiliser avec des conteneurs Docker pour une solution plus légère


*Ce tutoriel vous permet de gérer facilement des conteneurs avec Ansible, en créant un environnement léger et flexible pour le déploiement de services comme Apache.*

1. Installer Docker et Docker Compose pour gérer les conteneurs.
2. Configurer Ansible pour gérer les conteneurs comme des nœuds gérés.
3. Créer un playbook Ansible pour déployer un serveur web Apache dans un conteneur Docker.

### 🛠 Prérequis

- Avoir Docker et Docker Compose installés.
- Avoir Ansible installé sur la machine de contrôle.

---

## 🌍 Étape 1 : Installer Docker et Docker Compose


Si Docker et Docker Compose ne sont pas déjà installés, voici les commandes pour les installer.

# Méthode 1 - Installation de Docker

```bash
su
apt update
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
#ou sh install-docker.sh
docker version
docker compose version
apt-install docker-compose
```



# Méthode 2 - Installation de Docker

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

## 🧑‍💻 Étape 2 : Configurer les conteneurs comme nœuds Ansible

Nous allons créer un réseau Docker pour que les conteneurs puissent communiquer entre eux, puis démarrer des conteneurs que nous gérerons avec Ansible.

### 1. Créer un réseau Docker

```bash
docker network create ansible_network
```

### 2. Démarrer les conteneurs

Nous allons créer deux conteneurs Docker Ubuntu et les configurer pour accepter les connexions SSH. Ansible se connectera à ces conteneurs pour les gérer.

Créez un fichier `docker-compose.yml` avec le contenu suivant :

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

Ensuite, exécutez :

```bash
docker-compose up -d
```

Cela démarre deux conteneurs (`node1` et `node2`) et installe SSH sur chacun d’eux.

### 3. Configurer l’accès SSH pour Ansible

Pour que Ansible puisse se connecter aux conteneurs, nous devons configurer l’accès SSH avec des clés.

1. **Générer une clé SSH** sur la machine hôte (si elle n'existe pas déjà) :

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

## 📜 Étape 3 : Créer l'inventaire Ansible

Créez un fichier `inventory.ini` dans votre dossier de travail avec les adresses IP des conteneurs.

Exemple d'inventaire :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root
```

> Remplacez les adresses IP par celles de `node1` et `node2` obtenues via `docker inspect`.

---

## 🎯 Étape 4 : Écrire le playbook Ansible

Nous allons écrire un playbook qui installe Apache et le démarre sur les conteneurs.

Créez un fichier `playbook.yml` avec le contenu suivant :

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

---

## 🚀 Étape 5 : Exécuter le playbook

Lancez le playbook pour configurer Apache dans les conteneurs :

```bash
ansible-playbook -i inventory.ini playbook.yml
```

---

## 🔎 Vérifier le déploiement

Pour vérifier que le serveur web fonctionne, obtenez les IP de chaque conteneur et essayez de les atteindre via un navigateur :

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2
```

Accédez à `http://<IP_du_conteneur>` dans votre navigateur. Vous devriez voir le message “Bienvenue sur votre serveur web dans un conteneur Docker !”



# Références : 



*Pour enrichir votre compréhension de l'automatisation avec Ansible pour la configuration de Docker et le déploiement de conteneurs, voici une liste de ressources structurées, incluant des articles, tutoriels et vidéos explicatives :*

---

### 📘 Articles Techniques et Guides

1. **[Automation Using Ansible to Configure Docker & Deploy](https://www.linkedin.com/pulse/automation-using-ansible-configure-docker-deploy-ashish-wakchaure/)**  
   Cet article sur LinkedIn par Ashish Wakchaure explore comment automatiser la configuration de Docker et le déploiement de conteneurs à l'aide d'Ansible. Il couvre l'initialisation des scripts et l'intégration avec Docker.

2. **[Install Docker and Portainer in a VM Using Ansible](https://dev.to/rimelek/install-docker-and-portainer-in-a-vm-using-ansible-21ib)**  
   Ce guide sur Dev.to par Rimelek explique comment installer Docker et Portainer sur une machine virtuelle avec Ansible. Un bon point de départ pour une configuration rapide de Docker et de son interface de gestion.

3. **[How to Use Ansible to Install and Set Up Docker on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-use-ansible-to-install-and-set-up-docker-on-ubuntu-20-04)**  
   Un tutoriel de DigitalOcean qui détaille étape par étape l'installation de Docker sur Ubuntu avec Ansible. Il est parfait pour les débutants en infrastructure Ansible.

4. **[Docker Containers with Ansible](https://medium.com/@Oskarr3/docker-containers-with-ansible-89e98dacd1e2)**  
   Article sur Medium qui vous guide à travers la gestion de conteneurs Docker en utilisant Ansible. Il couvre plusieurs commandes Ansible pratiques pour manipuler Docker.

5. **[Deploying Docker Container on Remote VM Using Ansible](https://www.linkedin.com/pulse/deploying-docker-container-remote-vm-using-ansible-nilesh-mathur/)**  
   Une ressource sur LinkedIn par Nilesh Mathur pour le déploiement automatisé de conteneurs Docker sur une VM distante avec Ansible, un choix pertinent pour les infrastructures en cloud.

---

### 🎥 Vidéos et Démos Pratiques

6. **[Automate Docker Installation and Configuration with Ansible (YouTube Video)](https://www.youtube.com/watch?v=-sLjgApgWOM&ab_channel=LinuxR)**  
   Une vidéo de la chaîne LinuxR expliquant l'automatisation de l'installation et de la configuration de Docker avec Ansible. Elle est très utile pour visualiser les étapes et suivre une démonstration en temps réel.


*Ces ressources combinées offrent une bonne couverture des concepts fondamentaux d'Ansible appliqué à Docker, depuis l'installation jusqu'à la gestion des conteneurs. Elles vous permettront de maîtriser progressivement l'automatisation des configurations de Docker dans divers environnements.*
