# 🎩 Guide d'Installation et de Configuration de Ansible 🛠️

Ce guide vous aidera à installer Ansible, à configurer un fichier d'inventaire et à exécuter votre première commande Ansible. 🚀

---

## 1. 💾 Installation de Ansible

1. 🔍 **Supprimez** toute version existante d'Ansible (si installée) :
   ```bash
   sudo apt remove ansible
   ```
2. 📦 **Installez** les dépendances nécessaires :
   ```bash
   sudo apt install software-properties-common
   ```
3. 🛠️ **Ajoutez le dépôt PPA** pour Ansible et mettez à jour :
   ```bash
   sudo apt-add-repository --yes --update ppa:ansible/ansible
   sudo apt update
   ```
4. ✅ **Installez Ansible** :
   ```bash
   sudo apt install ansible
   ```
5. 📝 **Vérifiez la version** pour confirmer l'installation :
   ```bash
   ansible --version
   ```

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
```
