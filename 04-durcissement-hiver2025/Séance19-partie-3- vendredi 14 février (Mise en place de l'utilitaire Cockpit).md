
### **Séance 19 - Mise en place de l’utilitaire Cockpit**
**Date** : Vendredi 14 février  
**Objectif** : Installer, configurer et exploiter **Cockpit** pour administrer un serveur Linux via une interface web en LOCAL.  

Ce cours fournit une approche détaillée pour comprendre et exploiter Cockpit efficacement sur **Ubuntu Server 22.04**.

---

## **1. Présentation de Cockpit**

### **1.1. Qu'est-ce que Cockpit ?**  
Cockpit est un outil d'administration basé sur une interface web qui permet de gérer un serveur Linux sans nécessiter une utilisation constante de la ligne de commande. Il fournit une vue détaillée des performances du système et permet d'effectuer des tâches administratives courantes, comme la gestion des utilisateurs, des services, du stockage et des réseaux.

### **1.2. Pourquoi utiliser Cockpit ?**  
- Administration centralisée du serveur via un navigateur web  
- Surveillance des performances en temps réel  
- Gestion des services système et des mises à jour  
- Configuration simplifiée du réseau et des pare-feux  
- Possibilité d'étendre les fonctionnalités avec des modules supplémentaires  

### **1.3. Prérequis et environnement**  
Dans cette séance, l’installation et la configuration seront effectuées sur **Ubuntu Server 22.04 LTS**. Cependant, Cockpit est compatible avec plusieurs distributions, notamment :  
- Ubuntu 18.04, 20.04, 22.04  
- Debian 10 et 11  
- CentOS 7 et 8  
- Fedora  
- RHEL 7 et 8  

Un accès avec un compte **administrateur (sudo)** est nécessaire pour installer et gérer Cockpit.  

---

## **2. Installation de Cockpit sur Ubuntu Server 22.04**

### **2.1. Mise à jour du système**  
Avant d'installer Cockpit, il est recommandé de mettre à jour le système pour s'assurer que toutes les dépendances sont à jour.  
Exécutez les commandes suivantes :  

```bash
sudo apt update && sudo apt upgrade -y
```

### **2.2. Installation du package Cockpit**  
Par défaut, Cockpit est inclus dans les dépôts officiels d’Ubuntu. Pour l’installer, exécutez :  

```bash
sudo apt install cockpit -y
```

### **2.3. Vérification de l’installation**  
Après l’installation, vérifiez la version installée avec :  

```bash
cockpit --version
```

Si la commande ne renvoie rien, cela signifie que Cockpit n’est pas encore disponible dans le **PATH** de l'utilisateur.  

---

## **3. Activation et démarrage de Cockpit**

### **3.1. Activer et démarrer le service**  
Cockpit fonctionne comme un service sous **systemd**. Pour l’activer et le démarrer immédiatement, utilisez :  

```bash
sudo systemctl enable --now cockpit.socket
```

### **3.2. Vérifier que le service fonctionne**  
Vous pouvez vérifier l’état du service avec la commande suivante :  

```bash
sudo systemctl status cockpit
```

Si Cockpit est bien actif, vous verrez un message indiquant qu'il est **active (running)**.  

---

## **4. Configuration du pare-feu pour autoriser Cockpit**  

Si un pare-feu est activé sur votre serveur, vous devez autoriser le **port 9090**, qui est utilisé par Cockpit pour l’interface web.  

### **4.1. Vérification du pare-feu**  
Pour vérifier si **UFW** (Uncomplicated Firewall) est activé, utilisez :  

```bash
sudo ufw status
```

Si **UFW** est actif, autorisez le service Cockpit :  

```bash
sudo ufw allow 9090/tcp
```

Puis rechargez la configuration du pare-feu :  

```bash
sudo ufw reload
```

### **4.2. Vérification du port utilisé**  
Assurez-vous que Cockpit écoute bien sur le **port 9090** :  

```bash
sudo ss -tulnp | grep 9090
```

---

## **5. Accès à l’interface web de Cockpit**  

Une fois le service actif, vous pouvez y accéder via un navigateur web en utilisant l’adresse suivante :  

```
https://[IP_DU_SERVEUR]:9090
```

Par exemple, si votre serveur a l’adresse **192.168.1.100**, utilisez :  

```
https://192.168.1.100:9090
```

### **5.1. Connexion à Cockpit**  
Lors de la première connexion, un avertissement de certificat non valide peut apparaître, car Cockpit utilise un certificat SSL auto-signé. Acceptez l’exception de sécurité pour accéder à l’interface.  

Connectez-vous avec les identifiants d’un utilisateur disposant des droits **sudo**.  

---

## **6. Utilisation de Cockpit**

### **6.1. Tableau de bord et supervision**  
Une fois connecté, l’interface Cockpit affiche plusieurs sections :  
- **Vue d’ensemble** : Informations sur l’OS, les mises à jour, l’utilisation des ressources  
- **Journal des événements** : Logs du système  
- **Stockage** : Disques et partitions  
- **Réseau** : Interfaces et connexions réseau  
- **Services** : Gestion des services et processus  

### **6.2. Gestion des utilisateurs**  
Cockpit permet d’ajouter ou supprimer des utilisateurs directement via l’interface.  

### **6.3. Gestion des services**  
Vous pouvez démarrer, arrêter et redémarrer des services via l’interface graphique.  

---

## **7. Extensions et fonctionnalités avancées**  

Cockpit propose plusieurs modules complémentaires pour ajouter des fonctionnalités supplémentaires.

### **7.1. Gestion des conteneurs avec Podman**  
Podman est une alternative à Docker pour la gestion des conteneurs.  

Pour installer le module Cockpit Podman :  
```bash
sudo apt install cockpit-podman -y
```

Après installation, un nouvel onglet **Containers** apparaîtra dans l’interface web de Cockpit.  

### **7.2. Gestion des machines virtuelles**  
Si vous utilisez KVM pour la virtualisation, installez le module de gestion des machines virtuelles avec :  

```bash
sudo apt install cockpit-machines -y
```

---

## **8. Sécurisation et administration avancée**

### **8.1. Configuration du certificat SSL**  
Par défaut, Cockpit utilise un certificat auto-signé, ce qui peut poser un problème de sécurité. Vous pouvez installer un certificat SSL valide avec Let’s Encrypt :

```bash
sudo apt install certbot
sudo certbot certonly --standalone -d [votre_domaine]
```

Ensuite, configurez Cockpit pour utiliser ce certificat.  

### **8.2. Gestion des accès utilisateurs**  
Pour accorder l’accès Cockpit uniquement à certains utilisateurs, ajoutez-les au groupe **cockpit** :  

```bash
sudo usermod -aG cockpit [nom_utilisateur]
```

---

## **9. Exercices pratiques**  

1. **Installer Cockpit sur Ubuntu Server 22.04 et vérifier son fonctionnement.**  
2. **Accéder à Cockpit via un navigateur et explorer les options disponibles.**  
3. **Créer un nouvel utilisateur via Cockpit et lui attribuer des droits d’administration.**  
4. **Gérer un service système (exemple : Apache ou Nginx) via l’interface web.**  
5. **Installer un module complémentaire (ex. : Podman) et le tester.**  

---

## **10. Conclusion**  

Cockpit est un excellent outil pour simplifier la gestion d’un serveur Linux sans avoir à maîtriser toutes les commandes en ligne de commande. Il est léger, extensible et facile à utiliser, offrant un contrôle en temps réel sur les performances et la configuration du système.  

### **Prochaines étapes possibles :**  
- Intégration de Cockpit avec **Ansible** pour l'automatisation  
- Sécurisation avancée avec **certificats SSL** et gestion des accès  
- Monitoring avancé avec **Cockpit et Prometheus**  



