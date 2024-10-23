# 01-vbox+debian7.md

### **1. Téléchargement de VirtualBox**
1. Rendez-vous sur le site officiel de [VirtualBox](https://www.virtualbox.org/) et téléchargez la version compatible avec votre système d'exploitation (Windows, macOS, ou Linux).
2. Installez VirtualBox en suivant les instructions de l'assistant d'installation.

### **2. Téléchargement de l'image ISO de Debian 7**
1. Allez sur un site qui héberge des versions anciennes de Debian, comme le [Debian Archive](http://archive.debian.org/debian/).
2. Téléchargez l'image ISO correspondante à **Debian 7 ("Wheezy")** pour votre architecture (64-bit ou 32-bit).
   - [Debian 7.11 netinst ISO (64-bit)](http://archive.debian.org/debian/dists/wheezy/main/installer-amd64/current/images/netboot/mini.iso)
   - [Debian 7.11 netinst ISO (32-bit)](http://archive.debian.org/debian/dists/wheezy/main/installer-i386/current/images/netboot/mini.iso)

### **3. Création d'une nouvelle machine virtuelle sur VirtualBox**
1. Ouvrez **Oracle VirtualBox**.
2. Cliquez sur **Nouvelle** pour créer une nouvelle machine virtuelle.
3. Nommez votre machine virtuelle (par exemple, **Debian 7 Wheezy**).
4. **Type :** Choisissez **Linux**.
5. **Version :** Sélectionnez **Debian (64-bit)** ou **Debian (32-bit)** en fonction de l'architecture de votre ISO.
6. Cliquez sur **Suivant**.

### **4. Configuration de la machine virtuelle**
1. **Mémoire vive (RAM) :** Attribuez au moins **1 Go** (1024 Mo) de RAM. Si vous avez plus de RAM disponible, vous pouvez augmenter la quantité pour de meilleures performances (2 Go ou plus).
2. **Disque dur :**
   - Sélectionnez **Créer un disque dur virtuel maintenant**.
   - Cliquez sur **Créer**.
   - Choisissez **VDI (VirtualBox Disk Image)** et cliquez sur **Suivant**.
   - Choisissez **Dynamiquement alloué**.
   - Indiquez la taille du disque (au moins 8 Go, recommandé 20 Go ou plus pour des installations supplémentaires).
   - Cliquez sur **Créer**.

### **5. Configuration de l'image ISO dans VirtualBox**
1. Sélectionnez la machine virtuelle que vous venez de créer dans la liste à gauche de VirtualBox.
2. Cliquez sur **Configuration**.
3. Dans la fenêtre de configuration, allez à l'onglet **Stockage**.
4. Sous **Contrôleur IDE**, cliquez sur **Vide**.
5. À droite, sous **Attributs**, cliquez sur l'icône du disque et sélectionnez **Choisir un fichier de disque**.
6. Sélectionnez l'image ISO de Debian 7 que vous avez téléchargée.
7. Cliquez sur **OK** pour fermer la fenêtre de configuration.

### **6. Démarrage de la machine virtuelle et installation de Debian 7**
1. Sélectionnez votre machine virtuelle et cliquez sur **Démarrer**.
2. L'installation de Debian 7 commencera. Choisissez **Graphical Install** ou **Install** (en fonction de vos préférences).
3. Suivez les étapes normales d'installation :
   - Sélectionnez la langue, le pays, et la disposition de clavier.
   - Configurez votre réseau (via Ethernet ou Wi-Fi).
   - Créez le compte utilisateur et définissez les mots de passe root et utilisateur.
   - Choisissez l'option de partitionnement automatique si vous n'êtes pas familier avec le partitionnement manuel.
   - Installez le système de base et les logiciels requis (comme expliqué dans le guide précédent).

### **7. Installation du gestionnaire de démarrage GRUB**
1. Une fois le système installé, Debian vous demandera si vous souhaitez installer le gestionnaire de démarrage **GRUB**.
2. Sélectionnez **Oui** et installez GRUB sur le disque principal.

### **8. Finalisation de l'installation**
1. Une fois l'installation terminée, éjectez l'ISO virtuellement en revenant dans la configuration de la machine virtuelle sous l'onglet **Stockage** et en supprimant l'image ISO.
2. Redémarrez la machine virtuelle en cliquant sur **Démarrer**.

### **9. Premier démarrage sur Debian 7**
1. Debian devrait maintenant démarrer normalement sur votre machine virtuelle.
2. Connectez-vous avec votre nom d'utilisateur et votre mot de passe créés lors de l'installation.

### **10. Post-installation**
1. Mettez à jour votre système avec les commandes suivantes dans un terminal :
   ```bash
   sudo apt-get update
   sudo apt-get upgrade
   ```

2. Installez des logiciels supplémentaires si nécessaire :
   ```bash
   sudo apt-get install firefox libreoffice vlc
   ```

---

### **Résumé des étapes clés**
1. Télécharger et installer VirtualBox.
2. Télécharger l'image ISO de Debian 7.
3. Créer une nouvelle machine virtuelle avec 1 à 2 Go de RAM et au moins 8 Go d'espace disque.
4. Attacher l'image ISO de Debian 7 à la machine virtuelle.
5. Installer Debian 7 en suivant les étapes de partitionnement, création d'utilisateur, et installation de GRUB.
6. Démarrer Debian 7, mettre à jour le système et installer des logiciels supplémentaires.

