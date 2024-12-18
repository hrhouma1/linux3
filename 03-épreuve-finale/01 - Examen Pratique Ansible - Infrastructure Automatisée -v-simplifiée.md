# **Examen Pratique Ansible : Infrastructure Automatisée Simplifiée**

**Durée** : 3 heures  
**Total** : 100 points  

---

# **Introduction :**
Cet examen est conçu pour vous offrir une expérience pratique avec Ansible. Vous allez configurer une infrastructure simple et tester vos connaissances pas à pas. L’objectif principal est de vous familiariser avec les concepts de base d’Ansible tout en expérimentant un scénario réaliste.

👉 **Important :** Si une partie ne fonctionne pas ou si vous rencontrez des difficultés, ce n’est pas un problème ! Notez vos observations, corrigez si possible, et expliquez vos ajustements. Tout effort et réflexion sera valorisé.

---

# **Objectif Global :**
Créer une infrastructure Docker et la gérer avec Ansible à travers les étapes suivantes :
1. Configuration des conteneurs (infrastructure de base).
2. Création d’un inventaire Ansible.
3. Automatisation des installations de services.
4. Gestion des utilisateurs avec Ansible.
5. Génération d’un rapport système.

Chaque section est accompagnée d’un exemple ou de commandes pour vous guider. **N’oubliez pas d’ajouter des commentaires** dans vos fichiers pour expliquer vos choix et d’indiquer si cela fonctionne ou non.

---

# **Structure suggérée de vos fichiers :**
Organisez votre travail comme suit :

```
ansible_exam/
├── docker-compose.yml   # Configuration Docker
├── inventory.ini        # Inventaire Ansible
├── playbooks/           # Dossier contenant les playbooks
│   ├── setup.yml        # Playbook pour installer des services
│   ├── users.yml        # Playbook pour gérer les utilisateurs
│   ├── monitoring.yml   # Playbook pour créer un rapport
├── templates/           # Dossier pour les templates Jinja2
│   └── report.j2        # Template HTML pour le rapport
```

---

# **Étape 1 : Préparation de l’environnement**

1. **Créer une infrastructure Docker :**
   Déployez une infrastructure simple avec **6 conteneurs** :
   - 2 conteneurs Ubuntu (web1, web2)
   - 2 conteneurs Debian (db1, db2)
   - 2 conteneurs AlmaLinux (mail1, mail2)

2. **Configurer l'accès SSH :**
   Assurez-vous que chaque conteneur est accessible via SSH pour qu’Ansible puisse fonctionner.

👉 **Commandes suggérées :**
```bash
docker-compose up -d  # Lancez les conteneurs
docker ps             # Vérifiez leur statut
ssh-keygen -t rsa     # Générez une clé SSH
```

3. **Testez la connectivité SSH :**  
Vérifiez que vous pouvez vous connecter à chaque conteneur avec la commande `ssh`.

👉 **Critères d’évaluation :**
- Présence du fichier `docker-compose.yml`.
- Connectivité réussie ou explication des problèmes rencontrés.

---

# **Étape 2 : Création de l’inventaire Ansible**

1. **Créez un inventaire structuré :**  
Organisez vos hôtes dans un fichier `inventory.ini` avec des groupes :
   - `webservers` : web1, web2
   - `databases` : db1, db2
   - `mailservers` : mail1, mail2

2. **Ajoutez des variables pour chaque groupe :**  
Par exemple, définissez l’utilisateur SSH ou l’interpréteur Python.

👉 **Exemple de fichier :**
```ini
[webservers]
web1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
web2 ansible_host=172.20.0.3 ansible_user=root ansible_python_interpreter=/usr/bin/python3
```

3. **Testez l’inventaire :**  
Utilisez la commande suivante pour vérifier que tout fonctionne :
```bash
ansible all -i inventory.ini -m ping
```

👉 **Critères d’évaluation :**
- Fichier d’inventaire clair et fonctionnel.
- Explications si des ajustements sont nécessaires.

---

# **Étape 3 : Installation de services avec Ansible**

1. **Écrivez un playbook `setup.yml` :**  
Ce playbook doit installer les services suivants :
   - Apache sur les serveurs web
   - MariaDB sur les bases de données
   - Postfix sur les serveurs mail

2. **Gérez les différences entre distributions :**
   Utilisez des modules adaptés (`apt` pour Ubuntu/Debian, `yum` pour AlmaLinux).

👉 **Exemple de tâche :**
```yaml
- name: Installer Apache sur les serveurs web
  apt:
    name: apache2
    state: present
    update_cache: yes
  when: "'webservers' in group_names"
```

3. **Exécutez le playbook :**
```bash
ansible-playbook -i inventory.ini playbooks/setup.yml
```

👉 **Critères d’évaluation :**
- Playbook fonctionnel ou correctement commenté si des erreurs surviennent.
- Explications sur les ajustements apportés.

---

# **Étape 4 : Gestion des utilisateurs avec Ansible**

1. **Écrivez un playbook `users.yml` :**  
Créez 5 utilisateurs sur tous les serveurs avec :
   - Des mots de passe hashés.
   - Des droits sudo pour certains utilisateurs.

2. **Utilisez des boucles :**  
Simplifiez la gestion des utilisateurs avec des boucles.

👉 **Exemple :**
```yaml
- name: Ajouter les utilisateurs
  user:
    name: "{{ item.name }}"
    password: "{{ item.password }}"
    shell: /bin/bash
  loop:
    - { name: 'user1', password: 'hashed_password1' }
    - { name: 'user2', password: 'hashed_password2' }
```

👉 **Critères d’évaluation :**
- Création réussie des utilisateurs ou explications claires des erreurs.

---

# **Étape 5 : Génération d’un rapport système**

1. **Écrivez un playbook `monitoring.yml` :**  
Collectez des informations système avec Ansible et générez un rapport HTML.

2. **Utilisez un template Jinja2 :**  
Créez un fichier `templates/report.j2` pour formater le rapport.

👉 **Exemple de template :**
```html
<!DOCTYPE html>
<html>
<head><title>Rapport Système</title></head>
<body>
  <h1>Informations système</h1>
  <p>Processeur : {{ ansible_processor[1] }}</p>
  <p>Adresse IP : {{ ansible_default_ipv4.address }}</p>
</body>
</html>
```

3. **Exécutez le playbook :**
```bash
ansible-playbook -i inventory.ini playbooks/monitoring.yml
```

👉 **Critères d’évaluation :**
- Rapport généré avec succès ou commentaires détaillés si cela ne fonctionne pas.

---

# **Bonus : Concepts avancés (10 points)**

Pour aller plus loin, vous pouvez :
- Utiliser **Ansible Vault** pour sécuriser des données sensibles (par exemple, mots de passe).
- Implémenter des **rôles Ansible** pour mieux organiser vos playbooks.
- Écrire un **playbook de tests automatisés** pour valider les configurations.

---

# **Conclusion et conseils :**

Cet examen est avant tout une opportunité d’apprentissage. Si quelque chose ne fonctionne pas, n’hésitez pas à le signaler, à corriger ou à proposer une solution alternative. Toute tentative raisonnée et expliquée sera valorisée.

Bon courage ! 🎯
