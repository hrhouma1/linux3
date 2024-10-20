# 🚀 **Guide d'installation de l'architecture Master-Worker** 🌐

### *Le guide ultime pour configurer votre réseau Master-Worker avec Ubuntu, CentOS et Windows* 💻🔧

---

Bienvenue dans ce guide détaillé où vous apprendrez à configurer une **architecture réseau Master-Worker** qui vous permettra de contrôler et gérer facilement vos machines virtuelles avec style ! 🌟 Vous y trouverez tout ce dont vous avez besoin pour configurer vos interfaces réseau, accéder à vos machines via SSH, et orchestrer vos Workers avec une fluidité sans pareil ! 🔥

---

## 🛠 **Prêt à construire votre réseau ? Plongeons ensemble dans l'aventure !** 👇




# Sommaire

1. [Introduction](#1-introduction)
2. [Architecture](#2-architecture)
3. [Configuration réseau](#3-configuration-réseau)
   - [Interfaces réseau : NAT et Bridge Adapter](#interfaces-réseau-nat-et-bridge-adapter)
4. [Configuration de chaque machine (Master et Workers)](#4-configuration-de-chaque-machine-master-et-workers)
   - [Master Node (Ubuntu Server)](#master-node-ubuntu-server)
   - [Worker Node 01 (Ubuntu)](#worker-node-01-ubuntu-server)
   - [Worker Node 02 (CentOS)](#worker-node-02-centos-7)
   - [Worker Node 03 (Ubuntu)](#worker-node-03-ubuntu-server)
   - [Windows Desktop (Windows 10)](#windows-desktop-windows-10)
5. [Connexion SSH](#5-connexion-ssh)
6. [Vérification de la connectivité](#6-vérification-de-la-connectivité)
7. [Commandes utiles](#7-commandes-utiles)
   - [Mettre les interfaces down et up](#mettre-les-interfaces-down-et-up)
8. [Conclusion](#8-conclusion)

---

# 1. Introduction

L'objectif de ce document est de configurer une architecture maître-esclave en utilisant des machines virtuelles. Le **Master Node** est un serveur Ubuntu qui contrôle plusieurs **Worker Nodes** (Ubuntu, CentOS et Windows Desktop). Nous allons configurer deux interfaces réseau pour chaque machine : **Bridge Adapter** pour l'accès SSH et **NAT Network** pour la communication interne.

[Retour en haut](#sommaire)

---



# 2. Architecture

### Important : Il est conseillé de nommer les machines en fonction de leur rôle et de leur adresse IP pour faciliter la reconnaissance et la gestion dans votre infrastructure.

```
+-------------------------------------------------------------------------------------------------+
|                                           Master Node                                           |
|                                      master-node-10.0.2.10                                      |
|                                 NAT (enp0s8) : 10.0.2.10                                        |
|                                 Bridge (enp0s3) : 192.168.2.110                                 |
|                                    (Ubuntu Server - Master)                                     |
+-------------------------------------------------------------------------------------------------+
                                                 |
                                                 |
                       +-------------------------+------------------------+------------------------+
                       |                         |                        |                        |
+----------------------------------+ +--------------------------------+ +--------------------------------+ +--------------------------------+
|        node01-10.0.2.12          | |         node02-10.0.2.13       | |         node03-10.0.2.14       | |        win-node-10.0.2.15      |
|      NAT (enp0s8) : 10.0.2.12    | |      NAT (enp0s8) : 10.0.2.13  | |      NAT (enp0s8) : 10.0.2.14  | |      NAT (enp0s8) : 10.0.2.15  |
|    Bridge (enp0s3) : 192.168.2.112| |  Bridge (enp0s3) : 192.168.2.113| |  Bridge (enp0s3) : 192.168.2.114| |  Bridge (Ethernet) : 192.168.2.115|
|       (Ubuntu Server - Worker)   | |       (CentOS 7 - Worker)      | |       (Ubuntu Server - Worker) | |      (Windows 10 - Worker)     |
+----------------------------------+ +--------------------------------+ +--------------------------------+ +--------------------------------+
```

### Détails des noms professionnels attribués :

- **Master Node** : `master-node-10.0.2.10` – Ubuntu Server qui contrôle les autres nœuds.
- **Worker Node 01** : `node01-10.0.2.12` – Ubuntu Server pour les tâches de calculs.
- **Worker Node 02** : `node02-10.0.2.13` – CentOS 7 pour les opérations distribuées.
- **Worker Node 03** : `node03-10.0.2.14` – Ubuntu Server pour l'exécution des workloads.
- **Windows Desktop** : `win-node-10.0.2.15` – Machine Windows servant de Worker dans l'infrastructure.

Cette nomenclature rend plus claire la reconnaissance des machines et améliore la gestion de l'architecture.


### Légende :
- Chaque machine a deux interfaces réseau :
  - **NAT (enp0s8)** pour la communication interne entre les machines, avec des adresses IP dans la plage **10.0.2.x**.
  - **Bridge (enp0s3)** pour l'accès SSH, avec des adresses IP dans la plage **192.168.2.x**.

[Retour en haut](#sommaire)

---

# 3. Configuration réseau

Chaque machine aura deux interfaces réseau configurées :

- **enp0s3 (Bridge Adapter)** : Pour permettre l'accès SSH avec une adresse IP statique. Ces machines sont également connectées à Internet via cette interface.
- **enp0s8 (NAT Network)** : Pour la communication interne avec des adresses IP dans la plage **10.0.2.x**.

### Interfaces réseau : NAT et Bridge Adapter

| **Interface**        | **Plage d’adresses**         |
|----------------------|------------------------------|
| Bridge Adapter        | 192.168.2.110 – 192.168.2.115|
| NAT Network           | 10.0.2.10 – 10.0.2.15        |

[Retour en haut](#sommaire)

---

# 4. Configuration de chaque machine (Master et Workers)

### Master Node (Ubuntu Server)

1. **Installation d’Ubuntu Server** :
   - Installe Ubuntu Server pour le Master Node.

2. **Configurer les interfaces réseau** :
   - Modifie le fichier `/etc/netplan/00-installer-config.yaml` :

```yaml
network:
  version: 2
  ethernets:
    enp0s3:                          # Interface Bridge Adapter
      addresses:
        - 192.168.2.110/24            # Adresse IP pour SSH
      gateway4: 192.168.2.1           # Gateway pour l'accès Internet
      nameservers:
        addresses:
          - 8.8.8.8                   # DNS Google
      dhcp4: no
    enp0s8:                          # Interface NAT Network
      addresses:
        - 10.0.2.10/24                # Adresse IP interne pour NAT Network
      dhcp4: no
```

3. **Appliquer la configuration** :
   - Applique les modifications :
   ```bash
   sudo netplan apply
   ```

[Retour en haut](#sommaire)

---

### Worker Node 01 (Ubuntu Server)

1. **Installation d’Ubuntu Server** :
   - Installe Ubuntu Server pour Worker Node 01.

2. **Configurer les interfaces réseau** :
   - Modifie le fichier `/etc/netplan/00-installer-config.yaml` :

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
        - 192.168.2.112/24            # Adresse IP pour SSH
      gateway4: 192.168.2.1
      nameservers:
        addresses:
          - 8.8.8.8
      dhcp4: no
    enp0s8:
      addresses:
        - 10.0.2.12/24                # Adresse IP interne pour NAT Network
      dhcp4: no
```

3. **Appliquer la configuration** :
   - Applique les modifications :
   ```bash
   sudo netplan apply
   ```

[Retour en haut](#sommaire)

---

### Worker Node 02 (CentOS 7)

1. **Installation de CentOS 7** :
   - Installe CentOS 7 pour Worker Node 02.

2. **Configurer les interfaces réseau** :
   - Modifie le fichier `/etc/sysconfig/network-scripts/ifcfg-enp0s3` pour l'interface Bridge :

```bash
DEVICE=enp0s3
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.2.113
NETMASK=255.255.255.0
GATEWAY=192.168.2.1
DNS1=8.8.8.8
```

   - Modifie ensuite le fichier pour l'interface NAT Network (`/etc/sysconfig/network-scripts/ifcfg-enp0s8`) :

```bash
DEVICE=enp0s8
BOOTPROTO=none
ONBOOT=yes
IPADDR=10.0.2.13
NETMASK=255.255.255.0
```

3. **Redémarrer le service réseau** :
   ```bash
   sudo systemctl restart network
   ```

[Retour en haut](#sommaire)

---

### Worker Node 03 (Ubuntu Server)

1. **Installation d’Ubuntu Server** :
   - Installe Ubuntu Server pour Worker Node 03.

2. **Configurer les interfaces réseau** :
   - Modifie le fichier `/etc/netplan/00-installer-config.yaml` :

```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      addresses:
        - 192.168.2.114/24            # Adresse IP pour SSH
      gateway4: 192.168.2.1
      nameservers:
        addresses:
          - 8.8.8.8
      dhcp4: no
    enp0s8:
      addresses:
        - 10.0.2.14/24                # Adresse IP interne pour NAT Network
      dhcp4: no
```

3. **Appliquer la configuration** :
   - Applique les modifications :
   ```bash
   sudo netplan apply
   ```

[Retour en haut](#sommaire)

---

### Windows Desktop (Windows 10)

1. **Configurer l’interface réseau (Bridge Adapter)** :
   - Ouvre les **Paramètres réseau** dans le **Panneau de configuration**.
   - Modifie l'adresse IP pour `Ethernet`

 (Bridge Adapter) :
     - IP : `192.168.2.115`
     - Masque de sous-réseau : `255.255.255.0`
     - Passerelle : `192.168.2.1`
     - DNS : `8.8.8.8`

2. **Configurer l’interface NAT** :
   - Aucune configuration manuelle requise, VirtualBox attribue automatiquement une adresse NAT, mais tu peux forcer une IP comme `10.0.2.15`.

[Retour en haut](#sommaire)

---

# 5. Connexion SSH

Une fois toutes les machines configurées, tu peux te connecter via SSH en utilisant les adresses IP de l'interface **enp0s3** (Bridge Adapter). Voici des exemples de commandes SSH pour chaque machine :

- **Master Node** :
  ```bash
  ssh root@192.168.2.110
  ```

- **Worker Node 01** :
  ```bash
  ssh root@192.168.2.112
  ```

- **Worker Node 02** :
  ```bash
  ssh root@192.168.2.113
  ```

- **Worker Node 03** :
  ```bash
  ssh root@192.168.2.114
  ```

- **Windows Desktop** (via **Remote Desktop** si activé).

[Retour en haut](#sommaire)

---

# 6. Vérification de la connectivité

Pour vérifier que toutes les machines peuvent communiquer entre elles via l'interface **NAT Network (enp0s8)**, tu peux utiliser la commande **ping** depuis n'importe quelle machine :

- Depuis le **Master Node** :
  ```bash
  ping 10.0.2.12    # Pinger Worker Node 01
  ping 10.0.2.13    # Pinger Worker Node 02
  ping 10.0.2.14    # Pinger Worker Node 03
  ping 10.0.2.15    # Pinger Windows Desktop
  ```

[Retour en haut](#sommaire)

---

# 7. Commandes utiles

### Mettre les interfaces down et up

Voici les commandes pour **mettre une interface réseau en mode down** (désactivée) et **en mode up** (activée) sur Ubuntu et CentOS.

#### Sur Ubuntu (utilisant `enp0s3` et `enp0s8`) :

- **Mettre une interface down** :
  ```bash
  sudo ip link set enp0s3 down
  sudo ip link set enp0s8 down
  ```

- **Mettre une interface up** :
  ```bash
  sudo ip link set enp0s3 up
  sudo ip link set enp0s8 up
  ```

#### Sur CentOS (utilisant `ifdown` et `ifup`) :

- **Mettre une interface down** :
  ```bash
  sudo ifdown enp0s3
  sudo ifdown enp0s8
  ```

- **Mettre une interface up** :
  ```bash
  sudo ifup enp0s3
  sudo ifup enp0s8
  ```

Ces commandes permettent de réinitialiser les interfaces sans redémarrer la machine. Tu peux tester les nouvelles configurations après avoir mis les interfaces up.

[Retour en haut](#sommaire)

---

# 8. Conclusion

Avec cette configuration, tu as un réseau NAT pour la communication interne entre les machines, ainsi qu'un accès SSH via le Bridge Adapter avec des adresses IP statiques. Cette configuration est utile pour orchestrer des tâches entre un **Master Node** et plusieurs **Worker Nodes** dans un environnement contrôlé.

[Retour en haut](#sommaire)

