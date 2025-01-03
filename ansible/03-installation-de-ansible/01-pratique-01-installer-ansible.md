# 🎩 Guide d'Installation et de Configuration de Ansible sur votre MASTER 🛠️

Ce guide vous aidera à installer Ansible, à configurer un fichier d'inventaire et à exécuter votre première commande Ansible. 🚀
## Objectif:

- Faire fonctionner les 3 commandes suivantes (Nous avons besoin de deux machines , le master et un worker) 

  ```bash
  ansible --version
  ansible localhost -m 'ping'
  ansible all -m 'ping' -i inventory
   ```
---
---
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



1.7. 🗂️ Configuration du Fichier d'Inventaire

1.8. 📂 **Créez un répertoire pour les exercices Ansible** :
   ```bash
   cd
   mkdir exercices-ansible
   cd exercices-ansible
   ```

1.9. 📝 **Créez le fichier d'inventaire** :
   ```bash
   nano inventory
   ```
   - Dans le fichier `inventory`, ajoutez les informations suivantes :
     ```ini
     master ansible_connection=local
     node1 ansible_host=<adresse_ip_de_node1> ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
     ```

   Remplacez `<adresse_ip_de_node1>` par l'adresse IP de `node1`.

---

## 3. 🔧 Tester la Commande Ping sur Tous les Hôtes

1. 📡 **Exécutez la commande Ansible pour tester la connectivité avec tous les hôtes** :
   ```bash
   ansible all -m ping -i inventory
   ```

Si tout est bien configuré, vous verrez une réponse de type `SUCCESS` pour tous les hôtes, confirmant que la connexion est réussie ! 🎉

# 💀💀💀 ⚠️ ERREUR 💀💀💀
- Allez à l'étape suivante pour résoudre l'erreur 😉



---
---
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
   cd  ( + entrée)
   cd exercices-ansibles (vérifiez que vous avez le fichier d'inventaire (inventory) )
   ```
2. 🚀 Exécutez une **commande de test (ping)** sur tous les hôtes définis dans votre inventaire :
   ```bash
   ansible all -m 'ping' -i inventory
   ```

   ### 🚨 Résolution d'Éventuels Problèmes

   - ❌ **En cas d'échec** : 
     Vérifiez la configuration dans `ansible.cfg` et assurez-vous d'avoir ajouté `[defaults] host_key_checking = false`.
     Pour des conseils supplémentaires, consultez la 🌐 [documentation sur Stack Overflow](https://stackoverflow.com/questions/42462435/ansible-provisioning-error-using-a-ssh-password-instead-of-a-key-is-not-possibl) (lien suggéré pour résoudre les erreurs possibles).
     - https://stackoverflow.com/questions/42462435/ansible-provisioning-error-using-a-ssh-password-instead-of-a-key-is-not-possibl
     - https://stackoverflow.com/questions/23074412/how-to-set-host-key-checking-false-in-ansible-inventory-file

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

---------------------
---------------------
---------------------
# 📄 Annexe 1 : Résumé de l'Installation et de la Configuration d'Ansible
---------------------


Ce résumé couvre les étapes essentielles pour installer et configurer Ansible, créer un fichier d'inventaire, et exécuter les premières commandes pour tester la connectivité entre les hôtes.

---

# 📋 Il faut :

1. **Un fichier de configuration Ansible**  
   - 📁 **Chemin** : `/etc/ansible/ansible.cfg`
   - ⚙️ **Modification** : Ajouter une configuration pour désactiver la vérification des clés hôtes.

2. **Un fichier d'inventaire**  
   - 📁 **Chemin** : Créer un répertoire pour les exercices (`mkdir exercices-ansible`), puis créer le fichier `inventory`.
   - 📝 **Contenu du fichier `inventory`** :
     ```ini
     master ansible_connection=local
     node1 ansible_host=<adresse_ip_de_node1> ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
     ```
   - 📌 Remplacer `<adresse_ip_de_node1>` par l'adresse IP réelle de `node1`.

---

# 🛠️ Commandes Principales

Voici les principales commandes exécutées :

1. **Installation et mise à jour d’Ansible** :
   ```bash
   sudo apt update
   sudo apt install ansible
   ```

2. **Tester la connectivité locale** (pour vérifier l’installation) :
   ```bash
   ansible localhost -m ping
   ```

3. **Création du répertoire et du fichier d'inventaire** :
   ```bash
   cd
   mkdir exercices-ansible
   cd exercices-ansible
   nano inventory
   ```

4. **Exécuter un test de connectivité sur tous les nœuds de l'inventaire** :
   ```bash
   ansible all -m ping -i inventory
   ```
   - ⚠️ **Erreur possible** : Si cette commande échoue, il est nécessaire de désactiver la vérification des clés hôtes dans le fichier de configuration.

5. **Modification de la configuration d'Ansible pour désactiver la vérification des clés hôtes** :
   ```bash
   nano /etc/ansible/ansible.cfg
   ```
   - Ajouter la configuration suivante dans `ansible.cfg` :
     ```ini
     [defaults]
     host_key_checking = false
     ```

6. **Retester la connectivité après configuration** :
   ```bash
   cd exercices-ansible
   ansible all -m ping -i inventory
   ```

---

# ✅ Résumé des Étapes Réalisées

- 📌 Création et modification du fichier de configuration `ansible.cfg` pour éviter la vérification des clés hôtes.
- 📌 Création d'un fichier d'inventaire `inventory` pour définir les nœuds.
- 🛠️ Installation et vérification d'Ansible avec des commandes de test de connectivité (`ansible localhost -m ping` et `ansible all -m ping -i inventory`).
- 🚨 Résolution de l'erreur de connexion avec l'ajout de la configuration `[defaults] host_key_checking = false`.

Vous êtes maintenant prêt à utiliser Ansible pour gérer les nœuds définis dans l’inventaire ! 🎉









---------------------
---------------------
---------------------
# Annexe 02 : Modification du Nom d'Hôte (Hostname)



Cette annexe explique comment renommer le nom d'hôte de votre machine pour refléter un nouveau nom, par exemple `worker01`.

---

## 🖥️ Étapes pour Renommer le Nom d'Hôte

1. **Modification directe du fichier `hostname`** :
   - Ouvrez le fichier `/etc/hostname` dans un éditeur de texte comme `nano` :
     ```bash
     sudo nano /etc/hostname
     ```
   - Remplacez le contenu par le nouveau nom, par exemple :
     ```plaintext
     worker01
     ```
   - Enregistrez et fermez le fichier.

     ```bash
     bash
     ```

2. **Utilisation de la commande `hostnamectl`** :
   - Alternativement, vous pouvez utiliser `hostnamectl` pour définir le nom d'hôte sans modifier de fichier directement :
     ```bash
     sudo hostnamectl set-hostname worker01
     bash
     ```



3. **Vérification du Changement** :
   - Après avoir défini le nouveau nom d’hôte, vous pouvez vérifier le changement en utilisant la commande suivante :
     ```bash
     hostnamectl
     ```

---

## Résumé des Commandes

- **Ouvrir et modifier le fichier `hostname`** :
  ```bash
  sudo nano /etc/hostname
  bash
  ```
  - Changez le contenu pour le nouveau nom d’hôte (par exemple, `worker01`).

- **Définir le nom d’hôte avec `hostnamectl`** :
  ```bash
  sudo hostnamectl set-hostname worker01
  bash
  ```

- **Vérifier le nom d’hôte actuel** :
  ```bash
  hostnamectl
  ```

##### `bash` ????

Exécuter la commande `bash` après avoir changé le nom d'hôte n'est pas strictement nécessaire, mais cela peut parfois être utile pour rafraîchir l'environnement de terminal sans redémarrer le système.

### Pourquoi exécuter `bash` après avoir modifié le nom d’hôte ?

1. **Rafraîchir l’Environnement** : Lorsque vous exécutez `bash`, vous démarrez une nouvelle session de shell. Cela peut aider à rafraîchir certaines variables d'environnement ou informations affichées dans le terminal, y compris le nom d'hôte.

2. **Vérification Visuelle** : Si votre prompt de terminal inclut le nom d'hôte (ce qui est souvent le cas), lancer `bash` permet de mettre à jour le prompt pour afficher le nouveau nom sans avoir à ouvrir un nouveau terminal.

3. **Alternative au Redémarrage** : Bien que le changement de nom d'hôte soit pris en compte immédiatement par le système, certaines applications ou sessions peuvent encore afficher l'ancien nom d'hôte jusqu'à ce qu'elles soient relancées. Exécuter `bash` est une méthode légère pour appliquer ce changement dans la session en cours.

### En résumé
Lancer `bash` après un changement de nom d’hôte permet simplement de « rafraîchir » l'environnement du terminal actuel. Ce n’est pas obligatoire, mais cela peut vous permettre de voir instantanément les modifications, notamment si le prompt utilise le nom d'hôte.


---

En suivant ces étapes, le nom d'hôte de votre machine sera maintenant mis à jour vers `worker01` ! 🎉
