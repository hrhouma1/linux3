# 🚀 **Guide d'installation de l'architecture Master-Worker Ansible** 🌐

### *Configuration d'une architecture Master-Worker avec Ubuntu Server, Ubuntu Desktop, Debian 12, et Windows 10 Desktop pour Ansible* 💻🔧

---

# Sommaire

1. [Introduction](#1-introduction)
2. [Architecture](#2-architecture)
3. [Configuration réseau](#3-configuration-réseau)
4. [Configuration de chaque machine (Master et Workers)](#4-configuration-de-chaque-machine-master-et-workers)
   - [Master Node (Ubuntu Server)](#master-node-ubuntu-server)
   - [Worker Node 01 (Ubuntu Server)](#worker-node-01-ubuntu-server)
   - [Worker Node 02 (Ubuntu Desktop)](#worker-node-02-ubuntu-desktop)
   - [Worker Node 03 (Debian 12)](#worker-node-03-debian-12)
   - [Windows Desktop (Windows 10)](#windows-desktop-windows-10)
5. [Connexion SSH et Configuration Ansible](#5-connexion-ssh-et-configuration-ansible)
6. [Vérification de la connectivité](#6-vérification-de-la-connectivité)
7. [Commandes utiles](#7-commandes-utiles)
8. [Conclusion](#8-conclusion)

---

# 1. Introduction

Ce guide explique comment configurer un réseau Master-Worker pour Ansible. Le **Master Node** exécutera Ansible pour orchestrer les tâches sur les **Worker Nodes** (Ubuntu Server, Ubuntu Desktop, Debian 12 et Windows 10 Desktop).

---

# 2. Architecture

```
+-------------------------------------------------------------------------------------------------+
|                                           Master Node                                           |
|                                      master-node                                                |
|                                 Bridge (enp0s3) : 192.168.2.110                                 |
|                                    (Ubuntu Server - Master)                                     |
+-------------------------------------------------------------------------------------------------+
                                                 |
                                                 |
                       +-------------------------+------------------------+------------------------+
                       |                         |                        |                        |
+----------------------------------+ +--------------------------------+ +--------------------------------+ +--------------------------------+
|        node01                    | |         node02               | |         node03               | |        win-node               |
|      Bridge (enp0s3) : 192.168.2.112 | |  Bridge (enp0s3) : 192.168.2.113 | |  Bridge (enp0s3) : 192.168.2.114 | |  Bridge (Ethernet) : 192.168.2.115|
|       (Ubuntu Server - Worker)   | |       (Ubuntu Desktop - Worker)| |       (Debian 12 - Worker)    | |      (Windows 10 Desktop - Worker)|
+----------------------------------+ +--------------------------------+ +--------------------------------+ +--------------------------------+
```

### Table des adresses et interfaces pour l’architecture Ansible Master-Worker

| **Machine**          | **Nom**                  | **Interface Bridge Adapter (enp0s3)** | **Adresse IP Bridge** |
|----------------------|--------------------------|---------------------------------------|-----------------------|
| **Master Node**      | master-node              | enp0s3                                | 192.168.2.110         |
| **Worker Node 01**   | node01                   | enp0s3                                | 192.168.2.112         |
| **Worker Node 02**   | node02                   | enp0s3                                | 192.168.2.113         |
| **Worker Node 03**   | node03                   | enp0s3                                | 192.168.2.114         |
| **Windows Desktop**  | win-node                 | Ethernet (Bridge)                     | 192.168.2.115         |

---

# 3. Configuration réseau

Chaque machine aura une seule interface réseau configurée :

- **enp0s3 (Bridge Adapter)** : Fournit un accès SSH avec une adresse IP statique sur le réseau local et permet la connexion entre le Master et les Workers.

---

# 4. Configuration de chaque machine (Master et Workers)

### Master Node (Ubuntu Server)

1. **Installation d’Ubuntu Server** : Installez Ubuntu Server pour le Master Node.

2. **Configurer l’interface réseau** : Modifiez le fichier `/etc/netplan/00-installer-config.yaml` :

   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:                          
         addresses:
           - 192.168.2.110/24           
         gateway4: 192.168.2.1           
         nameservers:
           addresses:
             - 8.8.8.8                   
         dhcp4: no
   ```

3. **Appliquer la configuration** :
   ```bash
   sudo netplan apply
   ```

### Worker Node 01 (Ubuntu Server)

1. **Installation d’Ubuntu Server** : Installez Ubuntu Server pour Worker Node 01.

2. **Configurer l’interface réseau** : Modifiez le fichier `/etc/netplan/00-installer-config.yaml` :

   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses:
           - 192.168.2.112/24           
         gateway4: 192.168.2.1
         nameservers:
           addresses:
             - 8.8.8.8
         dhcp4: no
   ```

3. **Appliquer la configuration** :
   ```bash
   sudo netplan apply
   ```

### Worker Node 02 (Ubuntu Desktop)

1. **Installation d’Ubuntu Desktop** : Installez Ubuntu Desktop pour Worker Node 02.

2. **Configurer l’interface réseau** : Modifiez le fichier `/etc/netplan/00-installer-config.yaml` :

   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses:
           - 192.168.2.113/24           
         gateway4: 192.168.2.1
         nameservers:
           addresses:
             - 8.8.8.8
         dhcp4: no
   ```

3. **Appliquer la configuration** :
   ```bash
   sudo netplan apply
   ```

### Worker Node 03 (Debian 12)

1. **Installation de Debian 12** : Installez Debian 12 pour Worker Node 03.

2. **Configurer l’interface réseau** : Modifiez le fichier `/etc/network/interfaces` :

   ```bash
   auto enp0s3
   iface enp0s3 inet static
       address 192.168.2.114
       netmask 255.255.255.0
       gateway 192.168.2.1
       dns-nameservers 8.8.8.8
   ```

3. **Redémarrer le service réseau** :
   ```bash
   sudo systemctl restart networking
   ```

### Windows Desktop (Windows 10)

1. **Configurer l’interface réseau (Bridge Adapter)** :
   - Ouvrez les **Paramètres réseau** dans le **Panneau de configuration**.
   - Modifiez l'adresse IP pour `Ethernet` (Bridge Adapter) :
     - IP : `192.168.2.115`
     - Masque de sous-réseau : `255.255.255.0`
     - Passerelle : `192.168.2.1`
     - DNS : `8.8.8.8`

---

# 5. Connexion SSH et Configuration Ansible

Pour permettre au **Master Node** d’accéder aux **Worker Nodes** via SSH :

1. **Générer une clé SSH sur le Master Node** :
   ```bash
   ssh-keygen -t rsa
   ```

2. **Copier la clé publique sur chaque Worker Node** :
   - Exécutez cette commande pour chaque machine cible :
     ```bash
     ssh-copy-id user@192.168.2.x
     ```
   - Remplacez `user` par le nom d'utilisateur et `192.168.2.x` par l’adresse IP de chaque Worker.

3. **Configurer Ansible** :
   - Modifiez le fichier d’inventaire `/etc/ansible/hosts` pour ajouter les machines :
     ```ini
     [workers]
     node01 ansible_host=192.168.2.112 ansible_user=user
     node02 ansible_host=192.168.2.113 ansible_user=user
     node03 ansible_host=192.168.2.114 ansible_user=user
     win-node ansible_host=192.168.2.115 ansible_user=user
     ```

---

# 6. Vérification de la connectivité

Pour tester la connectivité entre le **Master Node** et chaque **Worker Node**, vous pouvez exécuter un ping sur chaque adresse IP :

```bash
ping 192.168.2.112    # Worker Node 01 (Ubuntu Server)
ping 192.168.2.113    # Worker Node 02 (Ubuntu Desktop)
ping 192.168.2.114    # Worker Node 03 (Debian 12)
ping 192.168.2.115    # Windows

 Desktop
```

---

# 7. Commandes utiles

### Redémarrage et état des interfaces sur Ubuntu et Debian

#### Sur Ubuntu :

- **Mettre une interface down** :
  ```bash
  sudo ip link set enp0s3 down
  ```

- **Mettre une interface up** :
  ```bash
  sudo ip link set enp0s3 up
  ```

#### Sur Debian :

- **Mettre une interface down** :
  ```bash
  sudo ifdown enp0s3
  ```

- **Mettre une interface up** :
  ```bash
  sudo ifup enp0s3
  ```

---

# 8. Conclusion

Avec cette configuration, le Master Node peut accéder aux Worker Nodes via SSH sur le **Bridge Adapter**. Vous êtes maintenant prêt à exécuter Ansible pour automatiser les tâches sur votre réseau Master-Worker.

[Retour en haut](#sommaire)
