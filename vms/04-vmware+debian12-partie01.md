# Partie 1 - **Installation complète de Debian 12 sur VMware Workstation Pro**

*Ceci est un guide simplifié pour installer **Debian 12** sur **VMware Workstation Pro** en utilisant l'option **Typical (recommended)***


### **1. Créer une nouvelle machine virtuelle dans VMware Workstation Pro**
1. Ouvrez **VMware Workstation Pro**.
2. Cliquez sur **Create a New Virtual Machine**.
3. Sélectionnez l'option **Typical (recommended)** et cliquez sur **Next**.
4. **Choix du fichier ISO :** Ne sélectionnez pas l’ISO à ce stade. Choisissez **I will install the operating system later** et cliquez sur **Next**.
5. **Guest Operating System :**
   - Sélectionnez **Linux**.
   - Dans la liste déroulante **Version**, choisissez **Debian 10.x 64-bit** (même si nous installons Debian 12, c’est le plus proche).
   - Cliquez sur **Next**.

### **2. Configuration de la machine virtuelle**
1. **Nom de la machine virtuelle :** Entrez **Debian12**.
2. **Location :** Choisissez l'endroit où vous souhaitez enregistrer la VM.
3. Cliquez sur **Next**.

### **3. Taille du disque dur**
1. **Specify Disk Capacity :** Allouez **25 Go** à la machine virtuelle.
2. Sélectionnez **Store virtual disk as a single file**.
3. Cliquez sur **Next**.

### **4. Configuration du réseau**
1. Sélectionnez **Use network address translation (NAT)** pour la carte réseau.
2. Cliquez sur **Next**.

### **5. Choix de l'ISO**
1. Avant de terminer, allez dans les **Customize Hardware**.
2. Sélectionnez **CD/DVD (SATA)** dans la liste à gauche.
3. Cliquez sur **Use ISO Image File** et sélectionnez l'image ISO de **Debian 12** que vous avez téléchargée.
4. Cliquez sur **Close** pour revenir à l'écran principal, puis sur **Finish** pour finaliser la création de la machine virtuelle.

### **6. Démarrage de la machine virtuelle**
1. Sélectionnez la machine virtuelle **Debian12** dans VMware Workstation Pro et cliquez sur **Power on this virtual machine**.
2. À l'écran de démarrage de Debian, sélectionnez **Graphical Install** avec les flèches et appuyez sur **Entrée**.

### **7. Installation de Debian 12**
1. **Langue :** Sélectionnez **Français** et cliquez sur **Continuer**.
2. **Pays :** Choisissez **Canada**.
3. **Disposition du clavier :** Sélectionnez **Français (Canada)**.
4. **Nom de la machine :** Entrez **Debian12** et continuez.
5. **Domaine :** Laissez vide et continuez.

### **8. Configuration de l'utilisateur et du mot de passe**
1. **Mot de passe root :** Entrez **eleve** deux fois.
2. **Nom complet de l'utilisateur :** Entrez **eleve**.
3. **Nom d'utilisateur :** Entrez **eleve**.
4. **Mot de passe utilisateur :** Entrez **eleve** deux fois.

### **9. Choix du fuseau horaire**
1. **Région :** Sélectionnez **Est**.

### **10. Partitionnement du disque**
1. Sélectionnez **Assisté - utiliser un disque entier**.
2. Sélectionnez le disque virtuel de 25 Go créé par VMware.
3. Choisissez **Tous les fichiers dans une seule partition**.
4. Sélectionnez **Terminer le partitionnement et appliquer les changements**.
5. Lorsque vous êtes invité à confirmer le formatage des partitions, sélectionnez **Oui**.

### **11. Configuration des dépôts et des paquets**
1. **Analyse des supports d'installation :** Lorsque l'analyse trouve une étiquette, sélectionnez **Non**.
2. **Outil de configuration des paquets :** 
   - Sélectionnez **Canada** comme miroir de dépôt.
   - Miroir Debian : Sélectionnez **deb.debian.org**.
   - **Proxy :** Laissez vide et continuez.

### **12. Installation d'APT et participation aux statistiques**
1. Debian installera automatiquement APT et vous demandera si vous souhaitez participer à l'étude des statistiques d'usage.
2. **Participation aux statistiques :** Sélectionnez **Non**.

### **13. Sélection des logiciels à installer**
1. **Sélection des logiciels :**
   - Laissez les cases cochées par défaut : **Environnement de bureau Debian (GNOME)**, **Serveur SSH**, et **Utilitaires usuels du système**.
   - Cochez **Serveur SSH** si ce n’est pas déjà fait.
2. Continuez pour que Debian installe les logiciels.

### **14. Installation du programme de démarrage GRUB**
1. Lorsque vous êtes invité à installer GRUB, sélectionnez **Oui**.
2. Choisissez **/dev/sda** pour installer GRUB sur le disque principal.

### **15. Finalisation de l'installation**
1. Une fois l'installation terminée, Debian redémarrera automatiquement.
2. VMware vous demandera si vous souhaitez éjecter virtuellement l'image ISO. Faites-le pour éviter de relancer l’installation.

### **16. Connexion**
1. À l'écran de connexion, utilisez les informations suivantes :
   - **Nom d'utilisateur :** `eleve`
   - **Mot de passe :** `eleve`
2. Vous serez dirigé vers l'environnement de bureau **GNOME** de Debian 12.

---

## **Résumé rapide :**
- **Nom de la machine :** Debian12
- **Nom d'utilisateur :** eleve
- **Mot de passe :** eleve
- **Taille du disque :** 25 Go
- **Type de réseau :** NAT
- **Graphical Install** avec **GNOME** et **Serveur SSH**.


-------------------------------------------
# Partie 2 - installation de docker et docker-compose
-------------------------------------------

*Ce script automatisera entièrement l'installation de Docker et Docker Compose sur Debian 12.*

- Je vous propose un script bash automatisé pour installer **Docker** et **Docker Compose** sur **Debian 12**. Ce script comprend l'installation de Docker, puis Docker Compose, et permet de vérifier les installations.

### **Script : Installer Docker et Docker Compose sur Debian 12**

```bash
#!/bin/bash

# Met à jour la liste des paquets et installe les dépendances nécessaires
echo "Mise à jour des paquets et installation des dépendances..."
sudo apt-get update
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Ajoute la clé GPG officielle de Docker
echo "Ajout de la clé GPG officielle de Docker..."
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Ajoute le dépôt Docker à la source APT
echo "Ajout du dépôt Docker au fichier sources..."
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Met à jour la liste des paquets à nouveau
echo "Mise à jour de la liste des paquets avec le dépôt Docker..."
sudo apt-get update

# Installe Docker
echo "Installation de Docker..."
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Démarre Docker et active-le au démarrage
echo "Démarrage de Docker et activation au démarrage..."
sudo systemctl start docker
sudo systemctl enable docker

# Vérifie que Docker est bien installé
echo "Vérification de l'installation de Docker..."
sudo docker --version

# Installe Docker Compose
echo "Installation de Docker Compose..."
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Applique les permissions d'exécution à Docker Compose
echo "Applique les permissions d'exécution à Docker Compose..."
sudo chmod +x /usr/local/bin/docker-compose

# Vérifie que Docker Compose est bien installé
echo "Vérification de l'installation de Docker Compose..."
docker-compose --version

echo "Installation terminée avec succès !"
```

### **Comment utiliser ce script :**
1. **Créer le script :**
   - Ouvrez un éditeur de texte (par exemple Nano) :
     ```bash
     nano install-docker.sh
     ```
   - Collez le script ci-dessus dans l'éditeur.
   - Enregistrez le fichier en appuyant sur `CTRL + O`, puis `Entrée`, et quittez avec `CTRL + X`.

2. **Donner les permissions d'exécution :**
   ```bash
   chmod +x install-docker.sh
   ```

3. **Exécuter le script :**
   ```bash
   sudo ./install-docker.sh
   ```

---

### **Ce que fait le script :**
1. Met à jour la liste des paquets et installe les dépendances nécessaires.
2. Ajoute la clé GPG officielle et le dépôt Docker.
3. Installe Docker (Docker CE, CLI, et plugins).
4. Démarre Docker et le configure pour démarrer au lancement de la machine.
5. Installe Docker Compose directement à partir du dépôt officiel.
6. Vérifie les versions installées de Docker et Docker Compose pour s'assurer que tout est en place.

