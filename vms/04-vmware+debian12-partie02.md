## **Installation automatique de Debian 12 sur VMware**

### **1. Télécharger et installer VMware Workstation Player**
1. Rendez-vous sur le site officiel de [VMware](https://www.vmware.com/products/workstation-player.html).
2. Téléchargez **VMware Workstation Player** (version gratuite) pour Windows.
3. Installez-le en suivant les instructions (cliquez simplement sur "Next" et "Install").

### **2. Télécharger l'image ISO de Debian 12**
1. Ouvrez votre navigateur et allez sur [le site officiel de Debian](https://www.debian.org/distrib/).
2. Téléchargez **Debian 12 "Bookworm" 64-bit netinst ISO**.

### **3. Création d'une nouvelle machine virtuelle dans VMware**
1. Ouvrez **VMware Workstation Player**.
2. Cliquez sur **Create a New Virtual Machine**.
3. **Installer l'OS depuis l'image ISO :**
   - Sélectionnez **Installer from a disk image (ISO)**.
   - Cliquez sur **Browse** et sélectionnez l'image ISO de **Debian 12** que vous avez téléchargée.
   - Cliquez sur **Next**.

### **4. Configuration des informations utilisateur automatiques**
1. Entrez les informations suivantes :
   - **Full Name :** `eleve`
   - **Username :** `eleve`
   - **Password :** `eleve`
   - **Confirm Password :** `eleve`
2. **Nom de la machine :** **Debian12** sera automatiquement utilisé comme nom de machine.
3. Cliquez sur **Next**.

### **5. Configuration de la machine virtuelle**
1. **Nom de la VM :** Laissez **Debian12** ou personnalisez-le.
2. **Location :** Choisissez l'emplacement où vous souhaitez stocker la machine virtuelle.
3. **Taille du disque dur :** Laissez la taille recommandée de **20 Go** (ou plus si nécessaire).
4. **Type de disque :** Choisissez **Split virtual disk into multiple files** si nécessaire, puis cliquez sur **Next**.

### **6. Finalisation de la configuration de la VM**
1. Cliquez sur **Customize Hardware** pour ajuster les ressources :
   - **Mémoire vive (RAM) :** Allouez au moins **2 Go** (2048 Mo).
   - **Processeurs :** Laissez les options par défaut ou ajustez selon les capacités de votre machine hôte.
2. Cliquez sur **Close** puis **Finish** pour créer la machine virtuelle.

### **7. Démarrage et installation automatique de Debian 12**
1. Cliquez sur **Play Virtual Machine** pour démarrer la machine virtuelle.
2. **VMware** lancera automatiquement l'installation de **Debian 12** à partir de l'ISO.
3. L’installation se fera de manière automatique avec les paramètres :
   - **Nom de la machine :** Debian12
   - **Nom d'utilisateur :** eleve
   - **Mot de passe :** eleve

### **8. Finalisation de l'installation**
1. L’installation de Debian 12 se déroulera sans intervention manuelle.
2. Une fois l'installation terminée, Debian redémarrera automatiquement.
3. Si nécessaire, VMware éjectera l'image ISO virtuellement après l’installation.

### **9. Connexion automatique**
1. Au redémarrage, utilisez les informations suivantes pour vous connecter :
   - **Nom d'utilisateur :** `eleve`
   - **Mot de passe :** `eleve`
2. Vous serez dirigé vers l’environnement de bureau de Debian 12 automatiquement.

---

## **Résumé rapide :**

- **Nom de la machine :** Debian12
- **Nom d'utilisateur :** eleve
- **Mot de passe :** eleve
- **Démarrage et installation automatiques** via VMware Workstation Player
