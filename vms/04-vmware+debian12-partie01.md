# **Installation complète de Debian 12 sur VMware Workstation Pro**

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

