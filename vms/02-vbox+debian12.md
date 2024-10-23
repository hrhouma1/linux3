# Introduction: 
------------

- La version décrite dans ce guide est **Debian 12 "Bookworm"**, qui est la dernière version stable à ce jour (2024). 
- C'est l'une des versions les plus récentes et robustes, recommandée pour les débutants et les utilisateurs expérimentés.
- Si vous souhaitez une version différente (comme une version testing ou une version plus ancienne), vous pouvez adapter le processus de téléchargement au début en choisissant l'image ISO correspondant à la version de Debian que vous voulez installer.
- Je vous présente un **Guide complet et détaillé** pour installer **Debian 12 "Bookworm"** sur **Oracle VirtualBox** sur une machine hôte **Windows** avec un environnement de bureau (desktop). 

---

## **Installation complète de Debian 12 sur VirtualBox avec un environnement de bureau (de A à Z)**

### **1. Télécharger et installer VirtualBox sur Windows**
1. Rendez-vous sur le site officiel de [VirtualBox](https://www.virtualbox.org/).
2. Téléchargez la version pour **Windows Hosts**.
3. Installez VirtualBox en suivant les instructions de l'assistant d'installation (cliquez sur "Next", puis "Install").
4. Une fois l'installation terminée, lancez VirtualBox.

### **2. Téléchargement de l'image ISO de Debian 12**
1. Ouvrez votre navigateur et allez sur [le site officiel de Debian](https://www.debian.org/) ou https://www.debian.org/distrib/ 
2. Sous **Small installation image**, cliquez sur **64-bit PC netinst ISO** (recommandé).
3. Téléchargez le fichier ISO (environ 400 à 600 Mo).

### **3. Créer une nouvelle machine virtuelle dans VirtualBox**
1. Dans VirtualBox, cliquez sur **Nouvelle** pour créer une nouvelle machine virtuelle.
2. **Nom :** Entrez un nom comme **Debian 12**.
3. **Type :** Choisissez **Linux**.
4. **Version :** Sélectionnez **Debian (64-bit)**.
5. **Mémoire vive (RAM) :** Allouez au moins **2 Go** (2048 Mo) de RAM (plus si possible pour de meilleures performances).
6. **Disque dur :** Sélectionnez **Créer un disque dur virtuel maintenant**.
7. Cliquez sur **Créer**.

### **4. Configuration du disque dur**
1. Choisissez **VDI (VirtualBox Disk Image)**.
2. Sélectionnez **Dynamically allocated** (espace disque virtuel dynamique).
3. Attribuez au moins **20 Go** (plus recommandé si vous prévoyez d'installer de nombreux logiciels).
4. Cliquez sur **Créer** pour finaliser.

### **5. Configuration de l'image ISO dans VirtualBox**
1. Sélectionnez la machine virtuelle que vous venez de créer dans VirtualBox et cliquez sur **Configuration**.
2. Allez dans **Stockage**.
3. Sous **Contrôleur IDE**, cliquez sur **Vide**.
4. À droite, cliquez sur l'icône du disque (à côté de **Attributs**) et choisissez **Fichier de disque**.
5. Sélectionnez l'image ISO de Debian 12 que vous avez téléchargée.
6. Cliquez sur **OK** pour revenir à l'interface principale de VirtualBox.

### **6. Démarrage de la machine virtuelle et installation de Debian 12**
1. Sélectionnez votre machine virtuelle et cliquez sur **Démarrer**.
2. L'installation de Debian 12 commencera. Vous verrez l'écran de démarrage.
3. Sélectionnez **Graphical Install** en utilisant les flèches de votre clavier et appuyez sur **Entrée**.

### **7. Choix de la langue, du pays et de la disposition du clavier**
1. **Langue :** Choisissez **Français** (ou une autre langue selon votre préférence).
2. **Pays :** Sélectionnez votre pays (exemple : **Canada**).
3. **Disposition du clavier :** Choisissez la disposition de votre clavier (exemple : **Français (Canada)** pour un clavier canadien-français).

### **8. Configuration du réseau**
1. Debian configurera automatiquement votre réseau si vous êtes connecté via **Ethernet**.
2. Si vous utilisez le **Wi-Fi**, une liste des réseaux disponibles sera affichée :
   - Sélectionnez votre réseau Wi-Fi et entrez votre mot de passe.

### **9. Configurer les utilisateurs et mots de passe**
1. **Nom de l'ordinateur (hostname) :** Entrez un nom pour votre machine (ex. **debian12-vm**).
2. **Mot de passe root :** Entrez un mot de passe pour le compte **root** (super-utilisateur).
3. **Création d'un utilisateur :** Entrez votre nom complet, un nom d'utilisateur et un mot de passe pour l'utilisateur standard.

### **10. Partitionnement du disque**
1. Choisissez **Guided - Use entire disk** (partitionnement automatique recommandé).
2. Sélectionnez le disque virtuel à utiliser (exemple : **VBOX HARDDISK**).
3. Sélectionnez **All files in one partition** (recommandé pour les débutants).
4. Confirmez les modifications et laissez Debian formater et partitionner le disque.

### **11. Installation du système de base**
1. L'installation du système de base commencera automatiquement. Cela peut prendre quelques minutes (10-30 minutes selon la configuration).
2. Pendant l'installation, vous verrez une barre de progression indiquant l'état de la copie et de l'installation des fichiers.

### **12. Choix des logiciels à installer**
1. Une fois le système de base installé, Debian vous demandera quels logiciels vous souhaitez installer :
   - Cochez **Environnement de bureau Debian**.
   - Sélectionnez l'environnement de bureau de votre choix :
     - **GNOME** (interface moderne, recommandé).
     - **KDE** (personnalisable).
     - **XFCE** (léger et rapide).
   - Cochez également **Utilitaires usuels du système** pour installer des outils de base.
2. Le programme d’installation téléchargera et installera les paquets nécessaires.

### **13. Installation du gestionnaire de démarrage GRUB**
1. Debian vous demandera si vous souhaitez installer **GRUB**, le gestionnaire de démarrage.
2. Sélectionnez **Oui** et installez-le sur le disque principal (souvent **/dev/sda**).

### **14. Finalisation de l'installation**
1. Une fois l'installation terminée, Debian vous invitera à retirer l'image ISO virtuelle.
   - Retournez dans **Configuration > Stockage** dans VirtualBox et retirez l'image ISO.
2. Cliquez sur **Redémarrer** pour démarrer Debian 12.

### **15. Premier démarrage sur Debian 12**
1. Lorsque Debian démarre, vous verrez l'écran de connexion GNOME ou KDE (selon ce que vous avez sélectionné).
2. Connectez-vous avec votre nom d'utilisateur et le mot de passe que vous avez créés.

---

## **Post-installation : Mise à jour et installation de logiciels**

### **16. Mise à jour du système**
1. Une fois connecté, ouvrez un terminal en cliquant sur l'icône du terminal dans le menu.
2. Exécutez les commandes suivantes pour mettre à jour votre système :
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

### **17. Installation de logiciels supplémentaires**
1. Pour installer des logiciels supplémentaires comme **Firefox**, **LibreOffice**, ou **VLC**, utilisez la commande suivante :
   ```bash
   sudo apt install firefox libreoffice vlc
   ```

### **18. Personnalisation de l'environnement de bureau**
1. Explorez les paramètres de votre environnement de bureau pour personnaliser le fond d’écran, la disposition des fenêtres, les icônes, etc.
2. Si vous avez installé **GNOME**, allez dans **Paramètres** pour ajuster la luminosité, le son, et configurer des raccourcis clavier.

---

## **Résumé des commandes importantes**

- **Mise à jour du système :**
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

- **Installer un logiciel :**
  ```bash
  sudo apt install nom_du_logiciel
  ```

- **Éteindre/redémarrer le système :**
  ```bash
  sudo shutdown now  # Éteindre
  sudo reboot        # Redémarrer
  ```


