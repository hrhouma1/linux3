### **Déploiement de Cockpit sur Azure avec Ubuntu 22.04**
Dans cette version, nous allons déployer **Cockpit** sur une **machine virtuelle Ubuntu 22.04** hébergée sur **Microsoft Azure**. L’installation se fera en deux étapes :
1. Création de l’infrastructure Azure (groupe de ressources, réseau, sous-réseaux, règles de sécurité).
2. Déploiement d’une machine virtuelle et installation de Cockpit.

---

## **1. Création de l’environnement Azure**
Nous allons commencer par créer les ressources nécessaires pour héberger la machine virtuelle.

### **1.1. Création du script `01-setup_azure.sh`**
Ouvrez un terminal et créez un fichier script :
```bash
nano 01-setup_azure.sh
```

### **1.2. Contenu du script `01-setup_azure.sh`**
Copiez et collez le code suivant dans le fichier :

```bash
#!/bin/bash

# Définition des variables
RESOURCE_GROUP="CockpitServer"
LOCATION="eastus"
VNET_NAME="CockpitVNet"
SUBNET_NAME="CockpitSubnet"
NSG_NAME="CockpitNSG"
NIC_NAME="CockpitNIC"

# Création du groupe de ressources
az group create --name $RESOURCE_GROUP --location $LOCATION

# Création du réseau virtuel et du sous-réseau
az network vnet create --resource-group $RESOURCE_GROUP --name $VNET_NAME --address-prefix 10.0.0.0/16 \
    --subnet-name $SUBNET_NAME --subnet-prefix 10.0.1.0/24

# Création du groupe de sécurité réseau (NSG) pour autoriser les accès nécessaires
az network nsg create --resource-group $RESOURCE_GROUP --name $NSG_NAME --location $LOCATION

# Autorisation des ports pour Cockpit (port 9090) et SSH (port 22)
az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $NSG_NAME \
    --name AllowCockpit --priority 1000 --direction Inbound --access Allow \
    --protocol Tcp --destination-port-range 9090 --destination-address-prefix "*" \
    --source-address-prefix "*" --source-port-range "*"

az network nsg rule create --resource-group $RESOURCE_GROUP --nsg-name $NSG_NAME \
    --name AllowSSH --priority 1001 --direction Inbound --access Allow \
    --protocol Tcp --destination-port-range 22 --destination-address-prefix "*" \
    --source-address-prefix "*" --source-port-range "*"

echo "Environnement Azure pour Cockpit prêt."
```

Enregistrez (`CTRL+X`, puis `Y`, puis `ENTER`).

### **1.3. Exécution du script**
Avant d’exécuter le script, assurez-vous qu’il est exécutable :
```bash
chmod +x 01-setup_azure.sh
./01-setup_azure.sh
```

---

## **2. Création de la VM et installation de Cockpit**
Nous allons maintenant créer la machine virtuelle et y installer Cockpit.

### **2.1. Création du script `02-create_vm_cockpit.sh`**
Créez un fichier script :
```bash
nano 02-create_vm_cockpit.sh
```

### **2.2. Contenu du script `02-create_vm_cockpit.sh`**
Copiez et collez le code suivant :

```bash
#!/bin/bash

# Définition des variables
RESOURCE_GROUP="CockpitServer"
VM_NAME="CockpitVM"
VNET_NAME="CockpitVNet"
SUBNET_NAME="CockpitSubnet"
VM_SIZE="Standard_B1s" # 1 vCPU, 1 Go RAM
IMAGE="Ubuntu2204"
USERNAME="azureuser"

# Création de la VM avec SSH keys auto-générées
az vm create \
    --resource-group $RESOURCE_GROUP \
    --name $VM_NAME \
    --image $IMAGE \
    --size $VM_SIZE \
    --vnet-name $VNET_NAME \
    --subnet $SUBNET_NAME \
    --admin-username $USERNAME \
    --generate-ssh-keys

# Récupération de l'adresse IP publique
VM_IP=$(az vm show -d -g $RESOURCE_GROUP -n $VM_NAME --query publicIps -o tsv)

echo "VM Cockpit créée avec succès."
echo "Adresse IP : $VM_IP"
```

Enregistrez (`CTRL+X`, puis `Y`, puis `ENTER`).

### **2.3. Exécution du script**
Rendez le script exécutable et exécutez-le :
```bash
chmod +x 02-create_vm_cockpit.sh
./02-create_vm_cockpit.sh
```

Une fois la machine virtuelle créée, l’IP publique de la VM sera affichée.

---

## **3. Connexion à la machine virtuelle et installation de Cockpit**
Maintenant que la VM est en place, connectez-vous via SSH :

```bash
ssh azureuser@VM_IP
```

(Remplacez `VM_IP` par l'adresse affichée après l’exécution du script.)

### **3.1. Mise à jour du système**
Mettez à jour votre VM avant d’installer Cockpit :
```bash
sudo apt update && sudo apt upgrade -y
```

### **3.2. Installation de Cockpit**
Installez Cockpit avec la commande suivante :
```bash
sudo apt install cockpit -y
```

### **3.3. Activation et démarrage de Cockpit**
Activez et démarrez Cockpit :
```bash
sudo systemctl enable --now cockpit.socket
```

Vérifiez son état :
```bash
sudo systemctl status cockpit
```

---

## **4. Accès à l'interface web de Cockpit**
Cockpit est maintenant accessible via un navigateur. Ouvrez un navigateur et entrez l’URL suivante :
```
https://VM_IP:9090
```

### **4.1. Connexion**
- Utilisez les identifiants de votre compte utilisateur **azureuser**.
- Acceptez l'exception de sécurité si le certificat SSL auto-signé est utilisé.

---

## **5. Vérification et gestion de Cockpit**
Une fois connecté, vous pouvez :
- **Superviser les performances du serveur** (CPU, mémoire, réseau)
- **Gérer les utilisateurs et les connexions SSH**
- **Surveiller les logs système**
- **Configurer les services et mises à jour**
- **Gérer le stockage et les disques**
- **Configurer le pare-feu et les connexions réseau**

---

## **6. Étapes supplémentaires (optionnel)**
### **6.1. Installation des extensions Cockpit**
Cockpit peut être enrichi avec des modules supplémentaires :

- Gestion des conteneurs avec Podman :
```bash
sudo apt install cockpit-podman -y
```

- Gestion des machines virtuelles avec KVM :
```bash
sudo apt install cockpit-machines -y
```

Après installation, les nouveaux onglets apparaîtront dans l’interface.

### **6.2. Sécurisation avec un certificat SSL**
Par défaut, Cockpit utilise un certificat auto-signé. Pour ajouter un certificat Let’s Encrypt :
```bash
sudo apt install certbot
sudo certbot certonly --standalone -d votre-domaine.com
```

Ajoutez ensuite le certificat dans Cockpit.

---

## **7. Exercices pratiques**
1. **Créer une infrastructure Azure avec le script `01-setup_azure.sh`.**
2. **Déployer une VM Ubuntu 22.04 avec le script `02-create_vm_cockpit.sh`.**
3. **Installer et configurer Cockpit sur la VM.**
4. **Explorer l'interface Cockpit et tester la gestion des services.**
5. **Ajouter un module complémentaire comme `cockpit-podman` et tester sa fonctionnalité.**

---

## **8. Conclusion**
Cockpit est une solution puissante pour l’administration des serveurs Linux sur Azure, offrant une gestion simplifiée via une interface web intuitive. Ce guide a montré comment :
- Déployer un environnement Azure avec **un réseau et une VM**,
- Installer et configurer **Cockpit** sur **Ubuntu Server 22.04**,
- Accéder et gérer **Cockpit à distance** via un navigateur web.

---

## **9. Étapes suivantes**
- **Configurer Cockpit avec Ansible** pour automatiser le déploiement.
- **Surveiller plusieurs serveurs depuis une seule interface Cockpit.**
- **Intégrer Cockpit avec Prometheus pour des analyses avancées.**

Ce guide fournit une approche détaillée pour comprendre et exploiter **Cockpit** efficacement sur **Azure**.
