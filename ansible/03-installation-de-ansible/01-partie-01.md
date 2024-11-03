# 🎩 Guide d'Installation et de Configuration de Ansible sur votre MASTER 🛠️

Ce guide vous aidera à installer Ansible, à configurer un fichier d'inventaire et à exécuter votre première commande Ansible. 🚀

---
# 1. 💾 Installation de Ansible
---

1.1. 🔍 **Supprimez** toute version existante d'Ansible (si installée) :
   ```bash
   sudo apt remove ansible
   ```
1.2. 📦 **Installez** les dépendances nécessaires :
   ```bash
   sudo apt install software-properties-common
   ```
1.3. 🛠️ **Ajoutez le dépôt PPA** pour Ansible et mettez à jour :
   ```bash
   sudo apt-add-repository --yes --update ppa:ansible/ansible
   sudo apt update
   ```
1.4. ✅ **Installez Ansible** :
   ```bash
   sudo apt install ansible
   ```
1.5. 📝 **Vérifiez la version** pour confirmer l'installation :
   ```bash
   ansible --version
   ```
1.6. 📝 **Vérifiez si Ansible est installé et fonctionne correctement sur la machine locale** :
   ```bash
   ansible localhost -m 'ping'
   ```

La commande `ansible localhost -m ping` est une commande Ansible qui permet de vérifier si Ansible est installé et fonctionne correctement sur la machine locale (le serveur sur lequel la commande est exécutée).

Voici un décryptage de chaque partie de la commande :

- **`ansible`** : le programme Ansible que vous exécutez.
- **`localhost`** : spécifie que vous voulez exécuter la commande sur la machine locale (celle où la commande est lancée).
- **`-m ping`** : utilise le module `ping` d'Ansible pour vérifier la connectivité avec l'hôte spécifié (ici `localhost`). Ce `ping` n'est pas le même que la commande réseau `ping` ; c'est un module d'Ansible qui envoie une requête pour vérifier que l'hôte est accessible et que l'installation d'Ansible est correctement configurée.

### Exemple de Résultat

Si tout est bien configuré, vous devriez voir un message de type :

```plaintext
localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

Cela signifie que la connexion avec l'hôte (`localhost`) est réussie et qu'Ansible fonctionne correctement.




---

## 2. 📂 Configuration du Fichier d'Inventaire

1. 📁 Accédez au répertoire de configuration d'Ansible :
   ```bash
   cd /etc/ansible
   ```
2. ✏️ Ouvrez le fichier de configuration pour le modifier :
   ```bash
   nano ansible.cfg
   ```
3. ⚙️ **Ajouter une configuration** pour éviter les vérifications de clés hôtes :
   Ajoutez la section suivante dans `ansible.cfg` pour éviter les échecs de vérification des clés hôtes lors de la première connexion :
   ```ini
   [defaults]
   host_key_checking = false
   ```

---

## 3. 🔧 Tester votre Première Commande

1. 📂 Accédez au répertoire où se trouve votre fichier d'inventaire :
   ```bash
   cd exercices-ansibles
   ```
2. 🚀 Exécutez une **commande de test (ping)** sur tous les hôtes définis dans votre inventaire :
   ```bash
   ansible all -m 'ping' -i inventory
   ```

   ### 🚨 Résolution d'Éventuels Problèmes

   - ❌ **En cas d'échec** : 
     Vérifiez la configuration dans `ansible.cfg` et assurez-vous d'avoir ajouté `[defaults] host_key_checking = false`.
     Pour des conseils supplémentaires, consultez la 🌐 [documentation sur Stack Overflow](https://stackoverflow.com) (lien suggéré pour résoudre les erreurs possibles).

3. 🔄 **Réessayez la commande** après avoir corrigé la configuration :
   ```bash
   ansible all -m 'ping' -i inventory
   ```
   - Vous devriez voir une réponse ✅ de réussite pour tous les hôtes configurés.

---

## 📝 Résumé

Ce guide vous a montré comment :
- 💻 **Installer** Ansible sur votre système.
- ⚙️ **Configurer** les options de connexion pour éviter les vérifications de clés hôtes.
- 🎯 **Exécuter** une commande de test pour valider la configuration.

🎉 **Félicitations !** Vous êtes maintenant prêt(e) à utiliser Ansible pour automatiser vos tâches d'administration de systèmes. ✨
