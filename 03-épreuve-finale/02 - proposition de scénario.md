# Structure

```
ansible_exam/
├── docker-compose.yml   # Configuration Docker pour déployer les conteneurs
├── inventory.ini        # Inventaire Ansible avec la liste des groupes et hôtes
├── playbooks/           # Répertoire contenant les playbooks Ansible
│   ├── setup.yml        # Playbook pour installer les services (Apache, MariaDB, Postfix)
│   ├── users.yml        # Playbook pour gérer les utilisateurs
│   ├── monitoring.yml   # Playbook pour collecter les informations système et créer un rapport
├── templates/           # Répertoire contenant les templates Jinja2 pour les rapports
│   └── report.j2        # Template HTML pour le rapport système
```

---

# **1. Préparation de l'Environnement**

#### **Créer la structure des fichiers**
```bash
mkdir ansible_exam
cd ansible_exam
touch docker-compose.yml inventory.ini
mkdir playbooks templates
touch playbooks/setup.yml playbooks/users.yml playbooks/monitoring.yml
touch templates/report.j2
```

---

# **2. Configuration Docker**

#### **Fichier `docker-compose.yml`**
```yaml
version: '3'
services:
  web1:
    image: ubuntu:latest
    container_name: web1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.2
    command: /bin/bash -c "apt update && apt install -y openssh-server python3 && service ssh start && tail -f /dev/null"
    expose:
      - "22"
      - "80"

  web2:
    image: ubuntu:latest
    container_name: web2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.3
    command: /bin/bash -c "apt update && apt install -y openssh-server python3 && service ssh start && tail -f /dev/null"
    expose:
      - "22"
      - "80"

  db1:
    image: debian:latest
    container_name: db1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.4
    command: /bin/bash -c "apt update && apt install -y openssh-server python3 && service ssh start && tail -f /dev/null"
    expose:
      - "22"
      - "3306"

  db2:
    image: debian:latest
    container_name: db2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.5
    command: /bin/bash -c "apt update && apt install -y openssh-server python3 && service ssh start && tail -f /dev/null"
    expose:
      - "22"
      - "3306"

  mail1:
    image: almalinux:latest
    container_name: mail1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.6
    command: /bin/bash -c "yum update -y && yum install -y openssh-server python3 && ssh-keygen -A && /usr/sbin/sshd -D"
    expose:
      - "22"
      - "25"

  mail2:
    image: almalinux:latest
    container_name: mail2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.7
    command: /bin/bash -c "yum update -y && yum install -y openssh-server python3 && ssh-keygen -A && /usr/sbin/sshd -D"
    expose:
      - "22"
      - "25"

networks:
  ansible_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
```

# **Démarrer les conteneurs**
```bash
docker-compose up -d
docker ps
```

---

# **3. Configuration SSH**

#### **Configurer les accès SSH**
```bash
ssh-keygen -t rsa -b 2048 -N "" -f ~/.ssh/id_rsa
rm -f ~/.ssh/known_hosts
for i in {1..6}; do
    docker exec -it $(docker ps --filter "name=node$i" -q) mkdir -p /root/.ssh
    docker cp ~/.ssh/id_rsa.pub $(docker ps --filter "name=node$i" -q):/root/.ssh/authorized_keys
    docker exec -it $(docker ps --filter "name=node$i" -q) chmod 600 /root/.ssh/authorized_keys
done
```

# **Tester les connexions SSH**
```bash
for i in {1..6}; do
    ssh -o StrictHostKeyChecking=no root@172.20.0.$((i+1)) exit
done
```

---

# **4. Configuration de l'Inventaire Ansible**

#### **Fichier `inventory.ini`**
```ini
[webservers]
web1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
web2 ansible_host=172.20.0.3 ansible_user=root ansible_python_interpreter=/usr/bin/python3

[databases]
db1 ansible_host=172.20.0.4 ansible_user=root ansible_python_interpreter=/usr/bin/python3
db2 ansible_host=172.20.0.5 ansible_user=root ansible_python_interpreter=/usr/bin/python3

[mailservers]
mail1 ansible_host=172.20.0.6 ansible_user=root ansible_python_interpreter=/usr/bin/python3
mail2 ansible_host=172.20.0.7 ansible_user=root ansible_python_interpreter=/usr/bin/python3
```

# **Vérifier l'inventaire**
```bash
ansible all -i inventory.ini -m ping
```

---

# **5. Exécution des Playbooks**

#### **Fichier `playbooks/setup.yml`**
```yaml
---
- name: Installation des services
  hosts: all
  become: yes
  tasks:
    - name: Installer Apache sur les serveurs web
      apt:
        name: apache2
        state: present
        update_cache: yes
      when: "'webservers' in group_names"

    - name: Installer MariaDB sur les bases de données
      apt:
        name: mariadb-server
        state: present
        update_cache: yes
      when: "'databases' in group_names"

    - name: Installer Postfix sur les serveurs mail
      yum:
        name: postfix
        state: present
        update_cache: yes
      when: "'mailservers' in group_names"
```

#### **Exécution du playbook**
```bash
ansible-playbook -i inventory.ini playbooks/setup.yml
```

---

# **6. Configuration des Utilisateurs**

#### **Fichier `playbooks/users.yml`**
```yaml
---
- name: Gérer les utilisateurs
  hosts: all
  become: yes
  vars:
    users:
      - name: admin
        groups: sudo
        shell: /bin/bash
      - name: dev
        groups: developers
        shell: /bin/bash
  tasks:
    - name: Ajouter les utilisateurs
      user:
        name: "{{ item.name }}"
        groups: "{{ item.groups }}"
        shell: "{{ item.shell }}"
      loop: "{{ users }}"
```

# **Exécution du playbook**
```bash
ansible-playbook -i inventory.ini playbooks/users.yml
```

---

# **7. Monitoring et Rapports**

#### **Fichier `templates/report.j2`**
```jinja2
<!DOCTYPE html>
<html>
<head><title>Rapport Système</title></head>
<body>
<h1>Informations Système</h1>
<p>Processeur: {{ ansible_processor[1] }}</p>
<p>IP: {{ ansible_default_ipv4.address }}</p>
</body>
</html>
```

#### **Fichier `playbooks/monitoring.yml`**
```yaml
---
- name: Collecte des données système
  hosts: all
  tasks:
    - name: Collecter des facts
      setup:
      register: system_info

    - name: Générer le rapport
      template:
        src: templates/report.j2
        dest: /var/www/html/report.html
```

#### **Exécution du playbook**
```bash
ansible-playbook -i inventory.ini playbooks/monitoring.yml
```

---

# **8. Nettoyage**

#### **Arrêter les conteneurs**
```bash
docker-compose down -v
```


-----------------
# Annexe : **Bonus (10 points)**
-----------------

- Cette partie introduit les concepts de rôles Ansible, d'Ansible Vault, et de tests automatisés, tout en restant accessible pour tous le monde.



```
ansible_exam/
├── roles/                # Répertoire pour les rôles Ansible
│   ├── webserver/        # Rôle pour les serveurs web
│   │   ├── tasks/        # Tâches spécifiques au rôle
│   │   │   └── main.yml  # Fichier principal des tâches
│   │   └── handlers/     # Gestion des services
│   │       └── main.yml  # Handlers pour redémarrer Apache
│   ├── database/         # Rôle pour les bases de données
│   │   └── tasks/        # Tâches spécifiques au rôle
│   │       └── main.yml  # Fichier principal des tâches
│   └── mailserver/       # Rôle pour les serveurs mail
│       └── tasks/        # Tâches spécifiques au rôle
│           └── main.yml  # Fichier principal des tâches
├── vault/                # Répertoire pour les fichiers chiffrés
│   └── secrets.yml       # Fichier contenant les données sensibles
├── tests/                # Répertoire pour les tests automatisés
│   └── test_playbook.yml # Playbook de test
```

---

### **1. Implémentation de Rôles Ansible**

#### **Création des Rôles**
```bash
ansible-galaxy init roles/webserver
ansible-galaxy init roles/database
ansible-galaxy init roles/mailserver
```

#### **Contenu des Rôles**
##### **Rôle `webserver`**
`roles/webserver/tasks/main.yml`
```yaml
---
- name: Installer Apache
  apt:
    name: apache2
    state: present
    update_cache: yes
  notify: Restart Apache
```

`roles/webserver/handlers/main.yml`
```yaml
---
- name: Restart Apache
  service:
    name: apache2
    state: restarted
```

##### **Rôle `database`**
`roles/database/tasks/main.yml`
```yaml
---
- name: Installer MariaDB
  apt:
    name: mariadb-server
    state: present
    update_cache: yes
```

##### **Rôle `mailserver`**
`roles/mailserver/tasks/main.yml`
```yaml
---
- name: Installer Postfix
  yum:
    name: postfix
    state: present
    update_cache: yes
```

#### **Playbook utilisant les rôles**
`playbooks/roles_playbook.yml`
```yaml
---
- name: Déployer les rôles
  hosts: all
  roles:
    - role: webserver
      when: "'webservers' in group_names"
    - role: database
      when: "'databases' in group_names"
    - role: mailserver
      when: "'mailservers' in group_names"
```

#### **Exécution du Playbook avec Rôles**
```bash
ansible-playbook -i inventory.ini playbooks/roles_playbook.yml
```

---

### **2. Utilisation d'Ansible Vault pour les Données Sensibles**

#### **Création d’un Fichier Vault**
```bash
mkdir vault
ansible-vault create vault/secrets.yml
```

Dans `vault/secrets.yml` :
```yaml
---
db_password: "secure_password_123"
mail_password: "secure_mail_123"
```

#### **Utilisation des Variables Vault**
Exemple dans le rôle `database` :
```yaml
- name: Configurer la base de données avec mot de passe sécurisé
  mysql_user:
    name: "admin"
    password: "{{ db_password }}"
    state: present
```

#### **Exécuter avec Vault**
```bash
ansible-playbook -i inventory.ini playbooks/roles_playbook.yml --ask-vault-pass
```

---

### **3. Tests Automatisés des Playbooks**

#### **Création d’un Playbook de Test**
`tests/test_playbook.yml`
```yaml
---
- name: Vérifier les services installés
  hosts: all
  tasks:
    - name: Vérifier Apache sur les serveurs web
      shell: systemctl status apache2
      when: "'webservers' in group_names"

    - name: Vérifier MariaDB sur les bases de données
      shell: systemctl status mariadb
      when: "'databases' in group_names"

    - name: Vérifier Postfix sur les serveurs mail
      shell: systemctl status postfix
      when: "'mailservers' in group_names"

    - name: Vérifier les utilisateurs
      shell: id admin
      changed_when: false
```

#### **Exécution des Tests**
```bash
ansible-playbook -i inventory.ini tests/test_playbook.yml
```

