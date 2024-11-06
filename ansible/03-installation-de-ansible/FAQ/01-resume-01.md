Voici une version améliorée et plus structurée de votre tutoriel Ansible pour une architecture Master-Worker.

---

# 🚀 **Guide d'Installation et de Configuration : Architecture Master-Worker avec Ansible** 🌐

## Sommaire

1. [Introduction](#1-introduction)
2. [Architecture](#2-architecture)
3. [Configuration Réseau](#3-configuration-reseau)
4. [Configuration des Machines (Master et Workers)](#4-configuration-des-machines)
5. [Vérification de la Connectivité et Configuration SSH](#5-verification-de-la-connectivite)
6. [Commandes Ansible Essentielles](#6-commandes-ansible-essentielles)
7. [Conclusion](#7-conclusion)

---

## 1. Introduction

Ce guide montre comment configurer une architecture Master-Worker avec Ansible. Le **Master Node** (sous Ubuntu Server) exécute Ansible pour orchestrer des tâches sur plusieurs **Worker Nodes** : Ubuntu Server, Ubuntu Desktop et Debian 12.

---

## 2. Architecture

- **Mode Bridge** dans VirtualBox : chaque VM est connectée à votre réseau domestique comme un appareil indépendant.
- Exemple d’adresses IP avec Videotron (format 10.0.0.X).

### Table des Adresses IP

| Machine   | Nom   | Adresse IP | Système       |
|-----------|-------|------------|---------------|
| Master    | master | 10.0.0.110 | Ubuntu Server |
| Node 1    | node1  | 10.0.0.111 | Ubuntu Server |
| Node 2    | node2  | 10.0.0.112 | Ubuntu Desktop|
| Node 3    | node3  | 10.0.0.113 | Debian 12     |

```
                          +--------------------------+
                          |    Modem/Routeur         |
                          |  Adresse IP : 10.0.0.1   |
                          +-----------+--------------+
                                      |
                                      |
                             +--------+--------+
                             |                 |
                        +----+----+            |
                        |  Master |            |
                        | Ubuntu  |            |
                        | Server  |            |
                        | 10.0.0.110           |
                        +---------+            |
                                              |
                       +------------+----------+-----------+
                       |            |                      |
                +------+-----+ +----+-----+          +-----+-----+
                |   Node 1   | |  Node 2   |          |  Node 3  |
                |  Ubuntu    | |  Ubuntu   |          |  Debian  |
                |  Server    | |  Desktop  |          |   12     |
                | 10.0.0.111 | | 10.0.0.112|          |10.0.0.113|
                +------------+ +-----------+          +----------+
```

---

## 3. Configuration Réseau

Chaque machine utilise une seule interface réseau **enp0s3 (Bridge Adapter)** pour une IP dédiée.

### Vérification des IPs sur le réseau

1. Accédez à l’interface de votre routeur à l’adresse **10.0.0.1**.
2. Identifiez les IPs disponibles (Vérifiez que 10.0.0.110, 10.0.0.111, etc., sont libres).

---

## 4. Configuration des Machines (Master et Workers)

### Configuration IP Statique

#### Exemple pour le Master Node (Ubuntu Server)

1. Modifiez `/etc/netplan/00-installer-config.yaml` :

   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses:
           - 10.0.0.110/24
         gateway4: 10.0.0.1
         nameservers:
           addresses:
             - 8.8.8.8
         dhcp4: no
   ```

2. Appliquez la configuration avec :
   ```bash
   sudo netplan apply
   ```

### Configuration des Workers

Suivez le même processus pour les autres nodes, en changeant seulement l'adresse IP.

---

## 5. Vérification de la Connectivité et Configuration SSH

Pour que le Master Node se connecte aux Workers, activez SSH.

1. **Installation du serveur SSH** :
   ```bash
   sudo apt install openssh-server iputils-ping -y
   ```
2. **Vérifiez le statut SSH** :
   ```bash
   systemctl status ssh
   ```
3. **Connectez-vous depuis la machine principale** :
   ```bash
   ssh eleve@10.0.0.110
   ```

---

## 6. Commandes Ansible Essentielles

### Installation d'Ansible sur le Master Node

1. Installez Ansible :
   ```bash
   sudo apt update
   sudo apt install ansible -y
   ```

2. Vérifiez l’installation :
   ```bash
   ansible --version
   ```

3. Créez un fichier d'inventaire dans `~/exercices-ansible` :
   ```bash
   mkdir ~/exercices-ansible
   cd ~/exercices-ansible
   nano inventory
   ```

### Exemple de Fichier `inventory`

```ini
master ansible_connection=local
node1 ansible_host=10.0.0.111 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node2 ansible_host=10.0.0.112 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node3 ansible_host=10.0.0.113 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve

[web]
node1
node3

[db]
node2
```

---

## 7. Tester la Connectivité des Nœuds

1. Modifiez le fichier de configuration pour éviter la vérification des clés :
   ```ini
   [defaults]
   host_key_checking = false
   ```

2. **Ping tous les nœuds pour tester** :
   ```bash
   ansible all -m ping -i inventory
   ```

---

## 8. Commandes Utiles pour Vérifier et Administrer les Nœuds

### Lister les Groupes

- **Lister les hôtes du groupe `web`** :
  ```bash
  ansible web -i inventory --list-hosts
  ```

### Exécuter des Commandes à Distance

- **Exécuter `uptime` sur `node1`** :
  ```bash
  ansible node1 -m command -a "uptime" -i inventory
  ```

- **Exécuter `date` sur `node2`** :
  ```bash
  ansible node2 -m command -a "date" -i inventory
  ```

---

## Conclusion

Ce tutoriel vous a guidé dans la création d’une architecture Ansible avec une connexion réseau en mode Bridge. Vous pouvez désormais exécuter des commandes Ansible pour automatiser des tâches sur chaque node de votre réseau.
