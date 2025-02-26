---
sidebar_position: 1 
sidebar_label: "Projet 01"
title: "Projet 01"
description: "Projet 01"
keywords: [ssl, tls, sécurité, https, certificats]
slug: /linux3/ssl-tls/projet-01
---


# Introduction à SSL/TLS, Certbot et Let's Encrypt (Partie 01)

<a name="table-des-matieres"></a>
<br/>

---
## Table des matières
---


- [ÉTAPE 01 - Création d'un compte Azure pour les étudiants](#etape01)
- [ÉTAPE 02 - Création d'une machine virtuelle](#etape02)
- [ÉTAPE 03 - Se connecter à la machine virtuelle à distance](#etape03)
- [ÉTAPE 04 - Installation d'Apache (serveur WEB)](#etape04)
- [ÉTAPE 05 - Installation de MySQL Server (Base de données)](#etape05)
- [ÉTAPE 06 - Installation de PHP (Langage de programmation)](#etape06)
- [ÉTAPE 07 - Installation de phpMyAdmin (Interface graphique pour accéder à la BD)](#etape07)
- [ÉTAPE 08 - Vérification de la création de la base de données (via SSH)](#etape08)
- [ÉTAPE 09 - Configurer notre site web avec les informations de la BD](#etape09)
- [ÉTAPE 10 - Téléverser votre site web vers votre instance (serveur Azure) via SCP](#etape10)
- [ÉTAPE 11 - Copier les fichiers vers /var/www/html/](#etape11)
- [ÉTAPE 12 - Tester](#etape12)
- [ÉTAPE 13 - Obtenir une adresse DNS (Un nom de domaine gratuit)](#etape13)
- [ÉTAPE 14 - HTTP → HTTPS (Obtenir un certificat Certbot pour utiliser HTTPS)](#etape14)
- [ÉTAPE 15 - Suppression des ressources Azure](#etape15)
- [ÉTAPE 16 - Résumé des étapes](#etape16)


## Liens utiles

Avant de commencer ce projet, assurez-vous d'avoir accès aux ressources suivantes qui vous guideront tout au long des étapes :
Ce projet consistera à créer une machine virtuelle Azure et de l'installer avec les services suivants :
- Apache
- MySQL
- PHP
- phpmyadmin

À la fin de ce projet, vous serez en mesure de :
- Créer une machine virtuelle Azure
- Installer et configurer les services Apache, MySQL, PHP et phpmyadmin
- Tester le site web
- Obtenir une adresse DNS (Un nom de domaine gratuit)
- Configurer le site web pour utiliser HTTPS
- Travailler avec Certbot et Let's Encrypt pour la sécurisation SSL/TLS des communications



- Liens fichiers : https://drive.google.com/drive/folders/1NbxowI-FlQttXM02yuSkQdPzSCaV63ng?usp=sharing
- Liens vidéos: https://drive.google.com/drive/folders/1hwpbdhNMlprwUan49ijREOJ_IUyX4ZRR?usp=sharing


<a name="etape01"></a>
<br/>

---
## ÉTAPE 01 - Création d'un compte azure pour les étudiants
---


### Prérequis
- Une adresse courriel académique valide (formats acceptés):
  - num@adresse-entreprise.com
  - num@adresse-institution.qc.ca 
  - num@adresse-college.qc.ca

### Étapes à suivre
1. Visitez le [portail Azure for Students](https://azure.microsoft.com/en-us/free/students) et cliquez sur "Commencer gratuitement"

2. Remplissez le formulaire d'inscription avec:
   - Votre adresse courriel académique
   - Vos informations personnelles
   - Une méthode de vérification (téléphone ou carte étudiante)

3. Une fois votre compte créé, [connectez-vous au portail Azure](https://portal.azure.com/) pour commencer à utiliser les services

### Notes importantes
- Le compte Azure for Students vous donne accès à $100 de crédits gratuits
- Certains services sont gratuits pendant 12 mois
- Gardez vos informations de connexion en lieu sûr


[🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape02"></a>
<br/>

---
## ÉTAPE 02 - Création d'une machine virtuelle 
---


### *2.1- Prérequis*
- Un compte Azure actif avec des crédits disponibles
- Accès au [portail Azure](https://portal.azure.com)
### *2.2- Étapes de création - méthode 1*
1. Dans le portail Azure, cliquez sur "Créer une ressource"
2. Sélectionnez "Machine virtuelle" dans les options populaires
3. Configurez les paramètres de base:
   - Groupe de ressources: Créez-en un nouveau
   - Nom de la machine: `ubuntu2204` 
   - Région: Choisissez une région proche de chez vous (par exemple: Canada Est)
   - Image: Ubuntu Server 22.04 LTS
   - Taille: Standard_B1s (1 vCPU, 1 Go RAM)
4. Configuration de l'authentification:
   - Type d'authentification: Clé publique SSH
   - Nom d'utilisateur: azureuser
   - Source de la clé publique SSH: Générer nouvelle paire de clés
   - Nom de la paire de clés: key1
5. Ports d'entrée:
   - Ouvrir les ports 22 (SSH), 80 (HTTP) et 443 (HTTPS)
6. Vérifiez et créez la VM:
   - Examinez la configuration
   - Cliquez sur "Créer"
   - Téléchargez la clé privée (.pem)
### *2.3- Configuration post-création*
1. Notez l'adresse IP publique de votre VM
2. Sécurisez votre clé privée:
### *2.4- Étapes de création - méthode 2*
Nous allons créer deux scripts:
- 01-script1_setup_azure.sh
- 02-script2_create_master.sh

:::info Rôle des scripts
*Ces deux scripts nous permettront de créer une machine virtuelle et de la configurer avec les paramètres souhaités.*
:::

### 2.4.1 - Création du script 01-script1_setup_azure.sh
:::warning 2.4.1
Créer le script 01-script1_setup_azure.sh
:::

```bash
nano 01-script1_setup_azure.sh
```
### 2.4.2 - Contenu du script 01-script1_setup_azure.sh
:::warning 2.4.2
Copiez le code ci-bas dans le fichier 01-script1_setup_azure.sh
:::

```bash
#!/bin/bash

# Configuration des variables
RESOURCE_GROUP="WebServer"
LOCATION="westus"
VNET_NAME="WebServerVNet"
SUBNET_MASTER="WebServerSubnetMaster"
SUBNET_WORKER="WebServerSubnetWorker"
NSG_NAME="WebServerMasterNSG"
NIC_NAME="WebServerMasterNIC"

# Création du groupe de ressources
az group create --name $RESOURCE_GROUP --location $LOCATION

# Création du réseau virtuel et des sous-réseaux
az network vnet create --resource-group $RESOURCE_GROUP --name $VNET_NAME --address-prefix 10.0.0.0/16 \
    --subnet-name $SUBNET_MASTER --subnet-prefix 10.0.1.0/24

az network vnet subnet create --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME \
    --name $SUBNET_WORKER --address-prefix 10.0.2.0/24

# Création du groupe de sécurité réseau (NSG) pour le master
az network nsg create --resource-group $RESOURCE_GROUP --name $NSG_NAME --location $LOCATION

# Création de la règle NSG pour ouvrir le port 6443 (Kubernetes API Server)
# Règle pour le port 80 (HTTP)
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $NSG_NAME \
    --name AllowHTTP --priority 1000 --direction Inbound --access Allow \
    --protocol Tcp --destination-port-range 80 --destination-address-prefix "*" \
    --source-address-prefix "*" --source-port-range "*"

# Règle pour le port 443 (HTTPS) 
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $NSG_NAME \
    --name AllowHTTPS --priority 1001 --direction Inbound --access Allow \
    --protocol Tcp --destination-port-range 443 --destination-address-prefix "*" \
    --source-address-prefix "*" --source-port-range "*"

# Règle pour le port 22 (SSH)
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $NSG_NAME \
    --name AllowSSH --priority 1002 --direction Inbound --access Allow \
    --protocol Tcp --destination-port-range 22 --destination-address-prefix "*" \
    --source-address-prefix "*" --source-port-range "*"

# Association du NSG à la NIC de la VM master
# Note: Cette commande suppose que la NIC existe déjà. Si la NIC n'existe pas encore,
# elle doit être créée avec la VM ou explicitement avant d'exécuter cette commande.
# Cette étape est normalement effectuée lors de la création de la VM Master dans create_master.sh
# az network nic update --resource-group $RESOURCE_GROUP --name $NIC_NAME --network-security-group $NSG_NAME

echo "Environnement Azure pour WebServer est prêt."
```
 

### 2.4.3 - Création du script 02-script2_create_master.sh
:::warning 2.4.3
Exécuter le script ci-bas dans le fichier 01-script1_setup_azure.sh pour créer l'environnement Azure pour WebServer
```bash
chmod +x 01-script1_setup_azure.sh
./01-script1_setup_azure.sh
```
:::



### 2.4.4 - Création du script 02-script2_create_master.sh
:::warning 2.4.4
Créer le script 02-script2_create_master.sh


```bash
nano 02-script2_create_master.sh
```
:::

### 2.4.5 - Contenu du script 02-script2_create_master.sh
:::warning 2.4.5
Copiez le code ci-bas dans le fichier 02-script2_create_master.sh
:::


# 📜 Script correspondant : `02-script2_create_master.sh`


```bash
#!/bin/bash

# Configuration des variables pour le Master
RESOURCE_GROUP="WebServer"
VM_NAME="WebServerMaster"
VNET_NAME="WebServerVNet"
SUBNET_NAME="WebServerSubnetMaster"
VM_SIZE="Standard_B1s" # Taille de la VM (1 vCPU, 1 Go RAM)
# Un exemple de taille de VM plus puissante: ==> VM_SIZE="Standard_D2s_v3" # Taille de la VM (2 vCPU, 8 Go RAM)
IMAGE="Ubuntu2204" # Image de la VM (Ubuntu 22.04 LTS)
USERNAME="azureuser" # Nom d'utilisateur

# Création de la VM Master avec une taille ajustée
az vm create \
    --resource-group $RESOURCE_GROUP \
    --name $VM_NAME \
    --image $IMAGE \
    --size $VM_SIZE \
    --vnet-name $VNET_NAME \
    --subnet $SUBNET_NAME \
    --admin-username $USERNAME \
    --generate-ssh-keys

# Vérification de la création de la VM Master
az vm list --resource-group $RESOURCE_GROUP --show-details

echo "VM Master créée."
 
```


### 2.4.6 - Exécution du script 02-script2_create_master.sh
:::warning 2.4.6
Exécuter le script ci-bas dans le fichier 02-script2_create_master.sh pour créer la VM Master pour WebServer
```bash
chmod +x 02-script2_create_master.sh
./02-script2_create_master.sh
```
:::




[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape03"></a>
<br/>

---
## ÉTAPE 03 - Se connecter à la machine virtuelle à distance
---




```bash
ssh -i key1.pem azureuser@<adresse_ip_publique_de_votre_vm>
```

Par exemple:

```bash
ssh -i key1.pem azureuser@20.115.69.240
```
Si vous avez déjà ajouté la clé publique à la VM (ce qui est fait automatiquement avec le script 01-script1_setup_azure.sh via l'option --generate-ssh-keys), vous pouvez vous connecter directement avec:

```bash
ssh -o StrictHostKeyChecking=no azureuser@<adresse_ip_publique_de_votre_vm>
```

Par exemple:

```bash
ssh -o StrictHostKeyChecking=no azureuser@20.115.69.240
```
Le strictHostKeyChecking=no est utilisé pour éviter les messages de vérification de la clé publique.






[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape04"></a>
<br/>

---
## Étape 04 - Installation d'apache (serveur WEB)
---





```bash
sudo -i
sudo apt update
sudo apt install apache2
sudo ufw app list
sudo ufw allow 'Apache'
sudo systemctl status apache2
```

Allez sur votre navigateur et tapez l'adresse IP publique de votre VM:

```bash
http://<adresse_ip_publique_de_votre_vm>
```

Par exemple:
```bash
http://20.115.69.240
```

⚠️ Si la page ne s'affiche pas, vérifiez que:
1. Le service Apache2 est bien démarré (sudo systemctl status apache2)
2. Le port 80 est bien ouvert dans le pare-feu Azure (règle de sécurité réseau)
3. Votre réseau d'entreprise ne bloque pas l'accès (voir le paragraphe ci-dessous concernant l'utilisation d'une adresse DNS)


:::danger Très important
Si vous êtes connecté à un réseau protégé (par exemple par Fortinet ou un autre pare-feu d'entreprise), l'accès direct via l'adresse IP publique de votre VM pourrait être bloqué. Dans ce cas, il est recommandé d'utiliser une adresse DNS pour accéder à votre VM. Azure offre gratuitement la possibilité de créer une adresse DNS personnalisée pour votre VM. Voici comment procéder:

1. Allez dans le portail Azure et sélectionnez votre VM
2. Dans le menu de gauche, cliquez sur "Configuration" 
3. Dans la section "Étiquette du nom DNS", cliquez sur "Configurer"
4. Entrez un nom DNS unique (ex: monapp-123). Le nom doit être unique dans la région Azure.
5. Cliquez sur "Enregistrer"

Votre VM sera maintenant accessible via l'adresse:
monapp-123.[region].cloudapp.azure.com

Par exemple:
monapp-123.eastus.cloudapp.azure.com

Cette adresse DNS pourra être utilisée à la place de l'adresse IP publique pour accéder à votre application web.

Allez sur votre navigateur et tapez l'adresse IP publique de votre VM:

```bash
http://<adresse_dns_de_votre_vm>
```

Par exemple:
```bash
http://monapp-123.eastus.cloudapp.azure.com
```


:::


[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape05"></a>
<br/>

---
## ÉTAPE 05 - Installation de mysql-server (Base de données)
---




⚠️ Si vous avez déjà installé mysql-server, vous pouvez passer à l'étape suivante.
Cette étape consiste à installer MySQL Server, le système de gestion de base de données relationnelle le plus populaire au monde. MySQL est un composant essentiel pour de nombreuses applications web, permettant de stocker et de gérer efficacement les données.

Les commandes ci-dessous permettront d'installer MySQL, de créer un utilisateur avec les privilèges nécessaires, et de configurer l'authentification. Nous créerons deux comptes:
- Un utilisateur 'eleve' avec tous les privilèges
- Le compte 'root' dont le mot de passe sera modifié

Les commandes suivantes doivent être exécutées:

```bash
ssh -i key1.pem azureuser@<adresse_ip_publique_de_votre_vm>
sudo -i
apt-get install mysql-server 
sudo systemctl status mysql
mysql
mysql> show databases;
mysql> CREATE USER 'eleve'@'localhost' IDENTIFIED BY 'eleve';
 mysql> GRANT ALL PRIVILEGES ON *.* TO 'eleve'@'localhost' WITH GRANT OPTION;
 mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH 'mysql_native_password' BY 'eleve';
mysql> FLUSH PRIVILEGES; 
mysql> \! clear;
mysql> DESCRIBE mysql.user; 
mysql> SELECT User, Host FROM mysql.user;
mysql> SELECT user, authentication_string, plugin, host FROM mysql.user;
mysql> FLUSH PRIVILEGES;
mysql> exit
mysql -u eleve -p'eleve'
mysql -u root -p'eleve'
(sans espace après le p)
```


[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape06"></a>
<br/>

---
## ÉTAPE 06 - Installation de php (Language de programmation)
---





⚠️ Si vous avez déjà installé php, vous pouvez passer à l'étape suivante.
Cette étape consiste à installer PHP, le langage de programmation le plus populaire au monde. PHP est un composant essentiel pour de nombreuses applications web, permettant de générer dynamiquement du contenu HTML. Les commandes ci-dessous permettront d'installer PHP et de vérifier que l'installation est correcte. Les commandes suivantes doivent être exécutées:


```bash
sudo apt install php libapache2-mod-php php-mysql
php -v
cd /var/www/html
touch info.php
nano info.php
<?php
phpinfo();
CTL+X ==> Y ==> ENTRÉÉ
cat info.php
```

Allez sur votre navigateur et tapez l'adresse IP publique de votre VM:

```bash
http://20.115.69.240/info.php
```
ou si le réseau d'entreprise bloque l'accès à l'adresse IP publique, utilisez l'adresse DNS:

```bash
http://<adresse_dns_de_votre_vm>/info.php
```

- Si vous voyez la page phpinfo(), c'est que PHP est bien installé.
- Sinon, il faut vérifier que le service Apache2 est bien démarré (sudo systemctl status apache2) et que le port 80 est bien ouvert dans le pare-feu Azure (règle de sécurité réseau).


[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape07"></a>
<br/>

---
## ÉTAPE 07 - Installation de phpmyadmin (Interface graphique pour accéder à la BD)
---






⚠️ Si vous avez déjà installé phpmyadmin, vous pouvez passer à l'étape suivante.
Cette étape consiste à installer phpmyadmin, une interface graphique pour accéder à la base de données.

Les commandes suivantes doivent être exécutées:

```bash
cd -
sudo apt update
sudo apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl
```

Allez sur votre navigateur et tapez l'adresse IP publique de votre VM:

```bash
http://20.115.69.240/phpmyadmin.php
```

ou si le réseau d'entreprise bloque l'accès à l'adresse IP publique, utilisez l'adresse DNS:

```bash
http://<adresse_dns_de_votre_vm>/phpmyadmin.php
```


:::danger Si vous n'arrivez pas à accéder à phpmyadmin
⚠️ Si vous n'arrivez pas à accéder à phpmyadmin, vérifiez que le service Apache2 est bien démarré (sudo systemctl status apache2) et que le port 80 est bien ouvert dans le pare-feu Azure (règle de sécurité réseau).

1 - Modifier le fichier de configuration
Tapez cette commande pour ouvrir le fichier :  

```bash
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```

Si nécessaire, ajoutez la ligne suivante (ou vérifiez qu’elle est présente) :  

```bash
Alias /phpmyadmin /usr/share/phpmyadmin
```

Enregistrez et fermez le fichier.

2 - Activer la configuration et redémarrer Apache :  
Exécutez ces commandes pour activer et redémarrer le serveur Apache :  

```bash
sudo a2enconf phpmyadmin
sudo systemctl restart apache2
```

3 - Accéder à phpMyAdmin :  
Ouvrez votre navigateur et entrez l'URL suivante :  

```bash
http://20.56.153.78/phpmyadmin
```

Note : Utilisez `/phpmyadmin` et non `/phpmyadmin.php`, car l'entrée par défaut est `index.php`.

Fini ! Vous devriez maintenant avoir accès à phpMyAdmin.

:::


Ajoutez la base de données et le fichier sql:

- Aller à phpmyadmin et connectez-vous avec les identifiants suivants:
    - Utilisateur: eleve
    - Mot de passe: eleve
- Vous pouvez aussi utiliser le compte root avec le mot de passe eleve  
- Aller à phpmyadmin et créer une nouvelle base de données avec le nom mydatabase
- Ouvrir mydatabase.sql avec Notepad++ et copier le contenu
- Aller à phpmyadmin, cliquer sur l'onglet SQL, coller le contenu de mydatabase.sql et cliquer sur le bouton "Exécuter"




:::info Ajoutez la base de données et le fichier sql
- Aller à phpmyadmin et connectez-vous avec les identifiants suivants:
    - Utilisateur: eleve
    - Mot de passe: eleve
- Vous pouvez aussi utiliser le compte root avec le mot de passe eleve
- Aller à phpmyadmin et créer une nouvelle base de données avec le nom mydatabase
- Aller au drive et copier mydatabase.sql
- Aller à phpmyadmin et importer la base de données mydatabase.sql
:::




[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape08"></a>
<br/>

---
## ÉTAPE 08 - Vérification  de la création de la base de données (via SSH)
---




⚠️ Vérifiez via ssh que la base de données est bien créée.

Voici les commandes à exécuter pour vérifier la création de la base de données via SSH:

```bash
ssh -i key1.pem azureuser@<adresse_ip_publique_de_votre_vm>
sudo -i
mysql -u eleve -p'eleve'
show databases;
use mydatabase;
show tables;
select * from `members`;
```


[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape09"></a>
<br/>
---
## ÉTAPE 09 - Configurer notre site web avec les informations de la bd ($server,$username,$password) 
---

:::danger Résumé de la configuration du fichier conn.php
Vérifiez la configuration du fichier conn.php pour la connexion à la base de données mydatabase.
:::

```bash
nano conn.php
```


```php
<?php
Class Connection{
 
	private $server = "mysql:host=localhost;dbname=mydatabase";
	private $username = "eleve";
	private $password = "eleve";
	....................
}
```








[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape10"></a>
<br/>
---
## ÉTAPE 10 - Téléverser votre site web vers votre instance (serveur AZURE) via scp
---

Dans un premier terminal, téléversez les fichiers vers votre instance (serveur AZURE) via scp:
```bash
scp -r -i key1.pem ./site  azureuser@20.115.69.240:\tmp
```



[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape11"></a>
<br/>
---
## ÉTAPE 11 - Copier les fichiers vers /var/www/html/
---

Dans un second terminal, connectez-vous à votre instance (serveur AZURE):

```bash
ssh -i mysrvubuntukey.pem  azureuser@20.115.69.240
cd /tmp
ls 
cd site/
sudo -i 
cd /tmp
cd /site
ls
cp -R * /var/www/html/
cd  /var/www/html/
ls
sudo systemctl restart apache2
```




[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape12"></a>
<br/>
---
## ÉTAPE 12 - Testez
---


Allez sur votre navigateur et tapez l'adresse IP publique de votre VM:

```bash
http://<adresse_ip_publique_de_votre_vm> 
```

Par exemple:
```bash
http://20.115.69.240
```


:::warning Si vous n'arrivez pas à accéder à votre site web
supprimer le fichier index.html pour ne pas avoir de conflit avec le fichier index.php:
```bash
rm -rf index.html
```
- ⚠️ Si vous n'arrivez pas à accéder à votre site web, vérifiez que le service Apache2 est bien démarré (sudo systemctl restart apache2 et puis sudo systemctl status apache2) et que le port 80 est bien ouvert dans le pare-feu Azure (règle de sécurité réseau).
- Commande azure pour ouvrir le port 80 depuis azure shell:

```bash
az vm open-port --resource-group myResourceGroup --name myVM --port 80 --priority 1011
az vm open-port --resource-group myResourceGroup --name myVM --port 8080 --priority 1012
az vm open-port --resource-group myResourceGroup --name myVM --port 443 --priority 1013
az vm open-port --resource-group myResourceGroup --name myVM --port 22 --priority 1014
```
:::







[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape13"></a>
<br/>
---
## ÉTAPE 13 - Obtenir une adresse DNS (Un nom de domaine gratuit) 
---

> 💡 Cette étape est déjà réalisée mais c'est pour vous donner une idée de la procédure à suivre.
> C'est une procédure pour obtenir une adresse DNS (Un nom de domaine gratuit) 

:::info Cette étape est déjà réalisée
L'étape pour obtenir une adresse DNS a déjà été décrite précédemment dans le document. En effet, dans la section "Très important" de l'ÉTAPE 04, on retrouve déjà les instructions détaillées pour :

1. Aller dans le portail Azure et sélectionner la VM
2. Configurer l'étiquette du nom DNS 
3. Entrer un nom DNS unique
4. Utiliser l'adresse DNS résultante

Il n'est donc pas nécessaire de répéter ces instructions ici.
:::



> Méthode 1 pour obtenir une adresse DNS

:::warning Méthode 1 pour obtenir une adresse DNS

1. Allez dans le portail Azure et sélectionnez votre VM
2. Dans la section "Étiquette du nom DNS", cliquez sur "Configurer"
3. Entrez un nom DNS unique (ex: monapp-123). Le nom doit être unique dans la région Azure.
4. Cliquez sur "Enregistrer"
5. Revenez à la page de votre VM et copiez l'adresse DNS:

Votre VM sera maintenant accessible via l'adresse:
monapp-123.[region].cloudapp.azure.com

Par exemple:
monapp-123.eastus.cloudapp.azure.com

Cette adresse DNS pourra être utilisée à la place de l'adresse IP publique pour accéder à votre application web.

Allez sur votre navigateur et tapez l'adresse IP publique de votre VM:

```bash
http://<adresse_dns_de_votre_vm>
```

Par exemple:
```bash
http://monapp-123.eastus.cloudapp.azure.com
```

:::


> Méthode 2: Utilisez une adresse DNS obtenue de No-IP pour accéder à votre site web. Par exemple:



:::warning Méthode 2 pour obtenir une adresse DNS
Une alternative gratuite pour obtenir un nom de domaine est d'utiliser le service No-IP. Voici les étapes :

1. Créez un compte gratuit sur [No-IP](https://www.noip.com/)

2. Une fois connecté :
   - Cliquez sur "Dynamic DNS" dans le menu
   - Sélectionnez "Create Hostname" 
   - Choisissez un nom d'hôte (ex: monsite)
   - Sélectionnez un domaine gratuit dans la liste (ex: hopto.org)
   - Cliquez sur "Create Hostname"

3. Votre nom de domaine sera alors :
   ```bash
   monsite.hopto.org
   ```

4. Installez le client DUC (Dynamic Update Client) sur votre VM pour maintenir l'adresse IP à jour :
   ```bash
   cd /usr/local/src
   wget http://www.noip.com/client/linux/noip-duc-linux.tar.gz
   tar xzf noip-duc-linux.tar.gz
   cd noip-2.1.9-1
   make
   make install
   ```

5. Configurez le client avec vos identifiants No-IP :
   ```bash
   /usr/local/bin/noip2 -C
   ```

6. Démarrez le service :
   ```bash
   /usr/local/bin/noip2
   ```

Votre VM sera maintenant accessible via votre nom de domaine No-IP.

Allez sur votre navigateur et tapez l'adresse IP publique de votre VM:

```bash
http://<adresse_dns_de_votre_vm>
```
:::

> Exemple:

```bash
http://monapp-123.hopto.org
```







[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape14"></a>
<br/>
---
## ÉTAPE 14 - HTTP → HTTPS (Obtenir un certificat Certbot pour utiliser HTTPS)
---



```bash
ssh -i key1.pem azureuser@<adresse_ip_publique_de_votre_vm>
sudo -i
sudo apt update
sudo apt install certbot python3-certbot-apache
sudo ufw allow 'Apache Full'
sudo certbot --apache # Entrez votre nom de domaine lorsqu'il vous sera demandé comme ceci: haythemrehouma.eastus.cloudapp.azure.com
sudo systemctl status certbot.timer
sudo certbot renew --dry-run
```


:::danger Attention
L'utilisation de HTTP (non sécurisé) n'est pas recommandée. Assurez-vous d'utiliser HTTPS pour protéger les données transmises entre votre navigateur et le serveur.
:::

```bash
❌ http://<adresse_ip_publique_de_votre_vm>
```

```bash
❌ http://<adresse_dns_de_votre_vm>
```

```bash
❌ https://<adresse_ip_publique_de_votre_vm>
```

```bash
✅ https://<adresse_dns_de_votre_vm>
```

*Donc, pour accéder à votre site web, vous devez utiliser l'adresse DNS avec https:*  

```bash
https://<adresse_dns_de_votre_vm>
```

*Par exemple:*

```bash
https://haythemrehouma.eastus.cloudapp.azure.com
``` 





[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape15"></a>
<br/>
---
## ÉTAPE 15 - Suppression des ressources Azure
---

### *Méthode 1  (via la console portail Azure)*

- ouvrez le portail Azure
- cliquez sur le groupe de ressources WebServer
- cliquez sur "Supprimer le groupe de ressources"
- entrez le nom du groupe de ressources (WebServer)
- cliquez sur "Supprimer"

### *Méthode 2  (via le shell Azure)*

```bash
az group delete --name WebServer --yes --no-wait
```

*ou*

```bash
RESOURCE_GROUP="WebServer"
az group delete --name $RESOURCE_GROUP --yes --no-wait
```

### *Méthode 3 (via un script)*

```bash
# Copier le script 07-script7_cleanup.sh
touch 07-script7_cleanup.sh
nano 07-script7_cleanup.sh
chmod +x 07-script7_cleanup.sh
./07-script7_cleanup.sh
```


# 📜 Script correspondant : `07-script7_cleanup.sh`


```bash
#!/bin/bash

# Configuration des variables
RESOURCE_GROUP="WebServer"

# Suppression du groupe de ressources et de toutes les ressources associées
az group delete --name $RESOURCE_GROUP --yes --no-wait

echo "Azure resources are being deleted..."
```




[ 🔙 Retour à la table des matières](#table-des-matieres)
<a name="etape16"></a>
<br/>
---
## ÉTAPE 16 - Résumé des étapes
---



- [ÉTAPE 01 - Création d'un compte Azure pour les étudiants](#etape01)
- [ÉTAPE 02 - Création d'une machine virtuelle](#etape02) 
- [ÉTAPE 03 - Se connecter à la machine virtuelle à distance](#etape03)
- [ÉTAPE 04 - Installation d'Apache (serveur WEB)](#etape04)
- [ÉTAPE 05 - Installation de MySQL Server (Base de données)](#etape05)
- [ÉTAPE 06 - Installation de PHP (Langage de programmation)](#etape06)
- [ÉTAPE 07 - Installation de phpMyAdmin (Interface graphique pour accéder à la BD)](#etape07)
- [ÉTAPE 08 - Création de la base de données](#etape08)
- [ÉTAPE 09 - Vérification de la création de la base de données (via SSH)](#etape09)
- [ÉTAPE 10 - Configurer notre site web avec les informations de la BD ($server, $username, $password)](#etape10)
- [ÉTAPE 11 - Téléverser votre site web vers votre instance (serveur AZURE) via SCP](#etape11)
- [ÉTAPE 11 - Copier les fichiers vers /var/www/html/](#etape11)
- [ÉTAPE 12 - Tester](#etape12)
- [ÉTAPE 13 - Obtenir une adresse DNS (Un nom de domaine gratuit)](#etape13)
- [ÉTAPE 14 - HTTP → HTTPS (Obtenir un certificat Certbot pour utiliser HTTPS)](#etape14)
- [ÉTAPE 15 - Suppression des ressources Azure](#etape15)
- [ÉTAPE 16 - Résumé des étapes](#etape16)


#### [🏠 Retour à la table des matières](#table-des-matieres)


