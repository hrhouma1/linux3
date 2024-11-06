# 🔧 Guide Complet : Configurer Ansible pour la Gestion des Conteneurs Docker 

Ce guide vous aide à installer Docker et Docker Compose, configurer des conteneurs Docker pour être gérés via Ansible, et automatiser le déploiement de services comme Apache.

## 🛠 Prérequis

- Docker et Docker Compose installés.
- Ansible installé sur la machine de contrôle.

---

## 🌍 Étape 1 : Installation de Docker et Docker Compose

Si Docker et Docker Compose ne sont pas déjà installés, suivez l'une des deux méthodes ci-dessous pour les installer.

### Méthode 1 : Installation Automatisée avec Script

```bash
su
apt update
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
docker version
docker compose version
```

### Méthode 2 : Installation Manuelle

```bash
# Mettre à jour le système
sudo apt update

# Installer les paquets nécessaires
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

# Ajouter la clé GPG de Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Ajouter le dépôt Docker
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# Installer Docker
sudo apt update
sudo apt install docker-ce -y
```

### Installation de Docker Compose

```bash
# Télécharger Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Donner les permissions nécessaires
sudo chmod +x /usr/local/bin/docker-compose

# Vérifier l’installation
docker-compose --version
```

---

## 🧑‍💻 Étape 2 : Configurer les Conteneurs Docker comme Nœuds pour Ansible

Nous allons créer un réseau Docker pour la communication entre les conteneurs, puis démarrer les conteneurs que nous gèrerons avec Ansible.

### 1. Créer un Réseau Docker

```bash
docker network create ansible_network
```

### 2. Démarrer les Conteneurs

Créez un fichier `docker-compose.yml` avec la configuration suivante pour démarrer deux conteneurs Ubuntu avec SSH.

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

Exécutez le fichier `docker-compose.yml` pour démarrer les conteneurs :

```bash
docker-compose up -d
```

### 3. Configurer l’Accès SSH pour Ansible

1. **Générer une Clé SSH** (si elle n'existe pas déjà) :

   ```bash
   ssh-keygen -t rsa -b 2048
   ```

2. **Copier la Clé Publique vers les Conteneurs** :

   ```bash
   docker exec -it node1 bash -c "mkdir -p /root/.ssh && echo '$(cat ~/.ssh/id_rsa.pub)' >> /root/.ssh/authorized_keys"
   docker exec -it node2 bash -c "mkdir -p /root/.ssh && echo '$(cat ~/.ssh/id_rsa.pub)' >> /root/.ssh/authorized_keys"
   ```

3. **Vérifier la Connexion SSH** pour chaque conteneur :

   ```bash
   ssh root@$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1)
   ssh root@$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2)
   ```

---

## 📜 Étape 3 : Configurer l'Inventaire Ansible

Créez un fichier `inventory.ini` dans le dossier `exercices-ansible` avec les adresses IP des conteneurs.

Exemple de fichier `inventory.ini` :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root

[web]
node1

[database]
node2
```

> Remplacez les adresses IP par celles des conteneurs obtenues avec `docker inspect`.

---

## 🎯 Étape 4 : Écrire le Playbook Ansible pour Déployer Apache

Créez un fichier `playbook.yml` pour installer et démarrer Apache sur les conteneurs.

```yaml
---
- name: Déployer Apache dans les Conteneurs Docker
  hosts: node_containers
  become: yes
  tasks:
    - name: Mettre à jour APT
      apt:
        update_cache: yes

    - name: Installer Apache2
      apt:
        name: apache2
        state: present

    - name: Démarrer le Service Apache
      service:
        name: apache2
        state: started
        enabled: true

    - name: Créer un fichier index.html
      copy:
        content: "<h1>Bienvenue sur votre serveur web dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html
```

---

## 🚀 Étape 5 : Exécuter le Playbook pour Configurer les Conteneurs

Lancez le playbook pour configurer Apache sur les conteneurs définis dans votre inventaire :

```bash
ansible-playbook -i inventory.ini playbook.yml
```

---

## 🔍 Vérification du Déploiement

1. Récupérez les adresses IP de chaque conteneur :

   ```bash
   docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1
   docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2
   ```

2. Ouvrez un navigateur et accédez à `http://<IP_du_conteneur>` pour vérifier que le serveur Apache est opérationnel. Vous devriez voir le message **“Bienvenue sur votre serveur web dans un conteneur Docker !”**.

---

## 🧰 Commandes Utiles pour la Gestion des Conteneurs Docker avec Ansible

Voici quelques commandes Ansible supplémentaires pour la gestion des conteneurs :

1. **Lister les hôtes du groupe `node_containers`** :
   ```bash
   ansible node_containers -i inventory.ini --list-hosts
   ```

2. **Exécuter une commande `uptime` sur `node1`** :
   ```bash
   ansible node1 -m command -a "uptime" -i inventory.ini
   ```

3. **Exécuter une commande `date` sur tous les nœuds** :
   ```bash
   ansible node_containers -m command -a "date" -i inventory.ini
   ```

4. **Vérifier l'état du service Apache sur `node1`** :
   ```bash
   ansible node1 -m service -a "name=apache2 state=started" -i inventory.ini
   ```

5. **Liste des modules Ansible disponibles pour Docker** :
   ```bash
   ansible-doc -l | grep docker
   ```

---

## 📚 Références Complémentaires

Pour approfondir l’automatisation avec Ansible et Docker, voici des ressources utiles :

- **[Automate Docker with Ansible](https://www.linkedin.com/pulse/automation-using-ansible-configure-docker-deploy-ashish-wakchaure/)** : guide d'automatisation Docker.
- **[Install Docker and Portainer with Ansible](https://dev.to/rimelek/install-docker-and-portainer-in-a-vm-using-ansible-21ib)** : tutoriel sur la configuration Docker avec Ansible.
- **[Ansible and Docker](https://medium.com/@Oskarr3/docker-containers-with-ansible-89e98dacd1e2)** : gestion des conteneurs Docker avec Ansible.

---

Ce guide couvre l'ensemble des étapes pour configurer Docker et Ansible pour le déploiement de services comme Apache dans un environnement conteneurisé. Vous êtes maintenant prêt à gérer vos conteneurs Docker via Ansible et automatiser vos déploiements de manière efficace ! 🎉
