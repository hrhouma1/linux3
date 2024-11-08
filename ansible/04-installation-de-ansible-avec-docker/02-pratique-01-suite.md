## 🖥️ Schéma 

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
    │Ubuntu ││Debian ││AlmaLnx││CentOS ││Ubuntu ││Ubuntu │
    └───────┘└───────┘└───────┘└───────┘└───────┘└───────┘
```

---

# Configurer Ansible et l'utiliser avec des conteneurs Docker pour une solution plus légère



## 🌍 Étape 1 : Installer Docker et Docker-Compose


```bash
su
apt update
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release git
git clone https://github.com/hrhouma/install-docker.git
cd install-docker/
chmod +x install-docker.sh
./install-docker.sh
#ou sh install-docker.sh
docker version
docker compose version
apt-install docker-compose
```


### 2. Démarrer les conteneurs

2.1. Nous allons créer deux conteneurs Docker Ubuntu et les configurer pour accepter les connexions SSH. Ansible se connectera à ces conteneurs pour les gérer.

```bash
mkdir ansible1
cd ansible1
nano docker-compose.yaml
```







---

## 🗄️ Étape 2 : Créer et démarrer les conteneurs

### 2.1. Créer un répertoire de travail

```bash
mkdir ansible_project
cd ansible_project
```

### 2.2. Créer le fichier `docker-compose.yaml`

```bash
nano docker-compose.yaml
```

Ajoutez le contenu suivant pour configurer six conteneurs avec différentes distributions :

```yaml
version: '3'
services:
  node1:
    image: ubuntu:latest
    container_name: node1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.2
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

  node2:
    image: debian:latest
    container_name: node2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.3
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

  node3:
    image: almalinux:latest
    container_name: node3
    networks:
      ansible_network:
        ipv4_address: 172.20.0.4
    command: /bin/bash -c "yum update -y && yum install -y openssh-server passwd && echo 'root:root' | chpasswd && /usr/sbin/sshd -D"
    expose:
      - "22"

  node4:
    image: centos:latest
    container_name: node4
    networks:
      ansible_network:
        ipv4_address: 172.20.0.5
    command: /bin/bash -c "yum update -y && yum install -y openssh-server passwd && echo 'root:root' | chpasswd && /usr/sbin/sshd -D"
    expose:
      - "22"

  node5:
    image: ubuntu:latest
    container_name: node5
    networks:
      ansible_network:
        ipv4_address: 172.20.0.6
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

  node6:
    image: ubuntu:latest
    container_name: node6
    networks:
      ansible_network:
        ipv4_address: 172.20.0.7
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

networks:
  ansible_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
```

### 2.3. Démarrer les conteneurs

```bash
docker-compose up -d
```

---

## 🔑 Étape 3 : Configurer l'accès SSH pour Ansible

### 3.1. Générer une clé SSH (si elle n'existe pas)

```bash
ssh-keygen -t rsa -b 2048
```

Appuyez sur **Entrée** pour accepter les emplacements par défaut.

### 3.2. Copier la clé publique vers chaque conteneur

Pour les conteneurs basés sur **Ubuntu** et **Debian** :

```bash
for i in 1 2 5 6; do
  docker exec -it node$i mkdir -p /root/.ssh
  docker cp ~/.ssh/id_rsa.pub node$i:/root/.ssh/authorized_keys
done
```

Pour les conteneurs basés sur **AlmaLinux** et **CentOS** :

```bash
for i in 3 4; do
  docker exec -it node$i mkdir -p /root/.ssh
  docker cp ~/.ssh/id_rsa.pub node$i:/root/.ssh/authorized_keys
  docker exec -it node$i chmod 600 /root/.ssh/authorized_keys
done
```

### 3.3. Vérifier la connexion SSH pour chaque conteneur

```bash
for i in {1..6}; do
  IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node$i)
  ssh -o StrictHostKeyChecking=no root@$IP exit
done
```

---

## 📜 Étape 4 : Créer l'inventaire Ansible

Créez un fichier `inventory.ini` dans votre dossier de travail avec les adresses IP des conteneurs.

Exemple d'inventaire :

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

## 🎯 Étape 5 : Écrire le playbook Ansible

Nous allons écrire un playbook qui installe Apache et le démarre sur chaque conteneur.

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
        content: "<h1>Bienvenue sur votre serveur web Ubuntu/Debian dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html

- name: Configure Apache on AlmaLinux and CentOS Containers
  hosts: node3,node4
  become: yes
  tasks:
    - name: Update YUM package manager
      yum:
        name: '*'
        state: latest

    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Start httpd Service
      service:
        name: httpd
        state: started
        enabled: true

    - name: Create index.html
      copy:
        content: "<h1>Bienvenue sur votre serveur web AlmaLinux/CentOS dans un conteneur Docker !</h1>"
        dest: /var/www/html/index.html
```

---

## 🚀 Étape 6 : Exécuter le playbook

Lancez le playbook pour configurer Apache dans les conteneurs :

```bash
ansible-playbook -i inventory.ini playbook.yml
```

---

## 🔎 Vérifier le déploiement

Pour vérifier que le serveur web fonctionne, obtenez les IP de chaque conteneur et essayez de les atteindre via un navigateur :

```bash
for i in {1..6}; do
  docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node$i
done
```

Accédez à `http://<IP_du_conteneur>` dans votre navigateur. Vous devriez voir le message correspondant au conteneur.
