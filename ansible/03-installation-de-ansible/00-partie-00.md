# 🚀 **Guide d'installation de l'architecture Master-Worker Ansible** 🌐

### *Configuration d'une architecture Master-Worker avec Ubuntu Server, Ubuntu Desktop, et Debian 12 pour Ansible* 💻🔧

---

## Sommaire

1. [Introduction](#1-introduction)
2. [Architecture](#2-architecture)
3. [Configuration réseau](#3-configuration-réseau)
4. [Configuration de chaque machine (Master et Workers)](#4-configuration-de-chaque-machine-master-et-workers)
5. [Vérification de la connectivité et configuration SSH](#5-vérification-de-la-connectivité-et-configuration-ssh)
6. [Commandes utiles](#6-commandes-utiles)
7. [Conclusion](#7-conclusion)

---
---
---

# 1. Introduction

Ce guide explique comment configurer un réseau Master-Worker pour Ansible. Le **Master Node** exécutera Ansible pour orchestrer les tâches sur les **Worker Nodes** (Ubuntu Server, Ubuntu Desktop, Debian 12).

---
---
---

# 2. Architecture

**Chaque machine est configurée en mode "Bridge" dans VirtualBox**, ce qui signifie que chaque machine virtuelle sera vue sur votre réseau domestique comme un appareil indépendant, au même titre que votre téléphone ou votre téléviseur.

- En mode Bridge, chaque VM utilise la carte réseau Wi-Fi de votre ordinateur (dans cet exemple, un Windows 11) et obtient une adresse IP comme si elle était connectée directement au réseau domestique.
- Dans mon cas avec Videotron, des adresses IP privées en **10.0.0.X** sont utilisées. L'adresse de mon modem/routeur est **10.0.0.1**.

Exemple de la table des adresses IP (configurées en bridge) :

| **Machine**          | **Nom**      | **Adresse IP** | **Système**       |
|----------------------|--------------|----------------|-------------------|
| **Master**           | master       | 10.0.0.110    | Ubuntu Server     |
| **Node 1**           | node1        | 10.0.0.111    | Ubuntu Server     |
| **Node 2**           | node2        | 10.0.0.112    | Ubuntu Desktop    |
| **Node 3**           | node3        | 10.0.0.113    | Debian 12         |

---
---
---

# 3. Configuration réseau

Chaque machine aura une seule interface réseau configurée : 

- **enp0s3 (Bridge Adapter)** : Cela permet à chaque machine de se comporter comme un appareil unique sur le réseau, avec une adresse IP dédiée.

### Vérification de la configuration réseau

Pour vérifier la configuration de votre réseau, vous pouvez accéder à l'interface administrateur de votre routeur domistique . Dans cet exemple, l'interface est accessible via l'adresse **10.0.0.1** sur monj navigateur windows local de ma machine host windows 11 (Je suis avec vidéotron):

- **Nom d'utilisateur par défaut** : `admin`
- **Mot de passe par défaut** : `password` (ou bien le mot de passe se trouve sous le modem)

- Chaque VM sera visible sur cette interface comme un appareil distinct, tout comme vos autres appareils connectés (téléphone, TV, etc.).
- Je peux voir la liste de toutes les appreils connectés comme la télé , les téléphones, les tablettes et les vms en bridge. Le but est de vérifier les adresses IPs utilisées pour voir si 10.0.0.110 , 10.0.0.111, 10.0.0.112, et 10.0.0.113 sont libres !!! 

---
---
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
           - 10.0.0.110/24
         gateway4: 10.0.0.1
         nameservers:
           addresses:
             - 8.8.8.8
         dhcp4: no
   ```

3. **Appliquer la configuration** :
   ```bash
   sudo netplan apply
   ```

## Worker Node 01 (Ubuntu Server)

1. **Installation d’Ubuntu Server** : Installez Ubuntu Server pour Worker Node 01.
2. **Configurer l’interface réseau** : Modifiez le fichier `/etc/netplan/00-installer-config.yaml` :

   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses:
           - 10.0.0.111/24
         gateway4: 10.0.0.1
         nameservers:
           addresses:
             - 8.8.8.8
         dhcp4: no
   ```

3. **Appliquer la configuration** :
   ```bash
   sudo netplan apply
   ```

## Worker Node 02 (Ubuntu Desktop)

1. **Installation d’Ubuntu Desktop** : Installez Ubuntu Desktop pour Worker Node 02.
2. **Configurer l’interface réseau** : Modifiez le fichier `/etc/netplan/00-installer-config.yaml` :

   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         addresses:
           - 10.0.0.112/24
         gateway4: 10.0.0.1
         nameservers:
           addresses:
             - 8.8.8.8
         dhcp4: no
   ```

3. **Appliquer la configuration** :
   ```bash
   sudo netplan apply
   ```

## Worker Node 03 (Debian 12)

#### Étape 1 : Ouvrir le Fichier de Configuration Réseau

1. Ouvrez le terminal.
2. Tapez la commande suivante pour ouvrir le fichier de configuration réseau avec l’éditeur `nano` :
   ```bash
   sudo nano /etc/network/interfaces
   ```
   - **Explication** : La commande `sudo` permet d'exécuter des commandes avec des privilèges d'administrateur, et `nano` est l'éditeur de texte. Le chemin `/etc/network/interfaces` est le fichier où nous allons ajouter la configuration réseau.

---

#### Étape 2 : Ajouter la Configuration de l'Interface Réseau

1. Dans le fichier qui s'ouvre, vous verrez probablement quelque chose comme ceci :

   ```plaintext
   # This file describes the network interfaces available on your system
   # and how to activate them. For more information, see interfaces(5).

   source /etc/network/interfaces.d/*

   # The loopback network interface
   auto lo
   iface lo inet loopback
   ```

2. Descendez à la fin du fichier en utilisant les flèches de votre clavier et ajoutez les lignes suivantes pour configurer l'interface réseau (ici, nous utilisons `enp0s3` comme nom d'interface) :

   ```plaintext
   # Configuration de l'interface réseau principale
   auto enp0s3
   iface enp0s3 inet static
       address 10.0.0.113
       netmask 255.255.255.0
       gateway 10.0.0.1
       dns-nameservers 8.8.8.8
   ```

   - **Explication des lignes** :
     - `auto enp0s3` : Active l'interface `enp0s3` au démarrage.
     - `iface enp0s3 inet static` : Configure l'interface `enp0s3` avec une IP statique.
     - `address 10.0.0.113` : Spécifie l'adresse IP que vous souhaitez utiliser.
     - `netmask 255.255.255.0` : Définit le masque de sous-réseau.
     - `gateway 10.0.0.1` : Définit la passerelle par défaut pour accéder à Internet.
     - `dns-nameservers 8.8.8.8` : Définit le serveur DNS (ici, Google DNS).

3. **Vérifiez bien l'indentation** : Assurez-vous que les lignes `address`, `netmask`, `gateway`, et `dns-nameservers` sont indentées avec des espaces pour éviter les erreurs.

---

#### Étape 3 : Enregistrer et Fermer le Fichier

1. **Enregistrer les modifications** : Appuyez sur `Ctrl + O` (lettre « O ») pour enregistrer le fichier.
   - Lorsque Nano vous demande de confirmer le nom du fichier, appuyez sur `Entrée` pour confirmer.

2. **Fermer l’éditeur** : Appuyez sur `Ctrl + X` pour quitter Nano.

---

#### Étape 4 : Redémarrer le Service Réseau

1. Pour appliquer les changements, redémarrez le service réseau avec la commande suivante :
   ```bash
   sudo systemctl restart networking
   ```

---

#### Étape 5 : Vérifier que l'Adresse IP est Correctement Configurée

1. Utilisez la commande suivante pour vérifier que l'interface `enp0s3` a bien l'adresse IP `10.0.0.113` :
   ```bash
   ip addr show enp0s3
   ```

2. Recherchez `10.0.0.113` dans la sortie de la commande. Si vous la voyez, cela signifie que la configuration a été appliquée avec succès ! 🎉

---

### Si l'adresse IP n'a pas changé : Redémarrer le Système

Parfois, redémarrer la machine peut être nécessaire si les paramètres réseau ne sont pas appliqués correctement.

1. **Pour redémarrer la machine** :
   ```bash
   sudo reboot
   ```

---
---
---

# 5. Vérification de la connectivité et configuration SSH

Pour que le **Master Node** puisse se connecter aux **Worker Nodes**, nous devons installer certains outils et activer SSH sur chaque machine.

## Étapes de configuration sur chaque machine (Master et Workers)

1. **Passer en superutilisateur** :
   ```bash
   sudo su
   ```
2. **Mettre à jour la machine et installer les outils nécessaires** :
   ```bash
   apt update
   apt install openssh-server iputils-ping -y
   ```
3. **Vérifier le statut du service SSH** :
   ```bash
   systemctl status ssh
   ```
4. **Afficher l'adresse IP de la machine** :
   ```bash
   ip a
   ```

## Connexion depuis votre machine principale

Pour vous connecter à distance depuis votre machine principale (Desktop), utilisez SSH :

- **Se connecter au Master Node** :
  ```bash
  ssh eleve@10.0.0.110
  ```
- **Se connecter au Worker Node 01** :
  ```bash
  ssh eleve@10.0.0.111
  ```
- **Se connecter au Worker Node 02** :
  ```bash
  ssh eleve@10.0.0.112
  ```
- **Se connecter au Worker Node 03** :
  ```bash
  ssh eleve@10.0.0.113
  ```

Remplacez `eleve` par le nom d'utilisateur approprié si nécessaire.

---
---
---

# 6. Commandes utiles

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

## 7. Conclusion

Avec cette configuration, le **Master Node** peut accéder aux **Worker Nodes** via SSH en mode Bridge, chaque VM étant reconnue sur le réseau comme un appareil unique. Vous êtes maintenant prêt à exécuter Ansible pour automatiser les tâches sur votre réseau Master-Worker.

