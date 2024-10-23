# **Installation automatique de Debian 12 sur VirtualBox 7 (version simplifiée)**

### **1. Télécharger et installer VirtualBox 7 sur Windows**
1. Rendez-vous sur le site officiel de [VirtualBox](https://www.virtualbox.org/).
2. Téléchargez la version pour **Windows Hosts**.
3. Installez VirtualBox en suivant les instructions (il suffit de cliquer sur "Next" puis "Install").

### **2. Télécharger l'image ISO de Debian 12**
1. Ouvrez votre navigateur et allez sur [le site officiel de Debian](https://www.debian.org/) :  https://www.debian.org/distrib/ ou https://www.debian.org/
2. Téléchargez **Debian 12 "Bookworm" 64-bit netinst ISO**.

### **3. Création automatique de la machine virtuelle dans VirtualBox 7**
1. Lancez **VirtualBox 7**.
2. Cliquez sur **Nouvelle** pour créer une nouvelle machine virtuelle.
3. Entrez **Debian12** comme nom de la machine virtuelle.
4. Dans la section **Fichier ISO**, cliquez sur **Ajouter un ISO** et sélectionnez l’image ISO de **Debian 12** que vous avez téléchargée.
5. Laisse VirtualBox détecter automatiquement les paramètres (type de système **Linux**, version **Debian (64-bit)**).
6. Attribuez **2 Go de RAM** (par défaut) ou plus si nécessaire.
7. Cliquez sur **Suivant** et laissez VirtualBox créer un disque dur virtuel d’environ **20 Go** automatiquement.
8. Une fois configuré, cliquez sur **Démarrer**.

### **4. Installation automatique de Debian 12**
1. VirtualBox lancera Debian 12 directement depuis l’image ISO, sans que tu aies besoin de configurer quoi que ce soit manuellement.
2. **Debian12** sera automatiquement utilisé comme nom de machine.
3. **Utilisateur :** Le nom d'utilisateur sera **eleve**.
4. **Mot de passe :** Le mot de passe sera **eleve**.

### **5. Finalisation de l'installation**
1. L’installation se fera automatiquement, sans intervention. Une fois terminé, Debian redémarrera automatiquement.
2. Si l’image ISO est toujours montée, VirtualBox 7 te proposera de l’éjecter virtuellement.
3. Redémarre la machine virtuelle si nécessaire.

### **6. Connexion automatique**
1. Au démarrage, connecte-toi avec les informations suivantes :
   - **Nom d'utilisateur :** `eleve`
   - **Mot de passe :** `eleve`
2. Tu seras dirigé vers le bureau de Debian 12 automatiquement.

---

## **Résumé rapide :**

- **Nom de la machine :** Debian12
- **Nom d'utilisateur :** eleve
- **Mot de passe :** eleve
- **Démarrage et installation automatiques** via VirtualBox 7

---

Avec ces étapes, tout est automatisé. Tu n'as rien à configurer manuellement lors de l'installation de **Debian 12** sur **VirtualBox 7**, à part sélectionner l'image ISO et démarrer la machine.
