# **Rôles Ansible**

- Bienvenue dans ce cours détaillé conçu spécialement pour des débutants complets. 
- Nous allons apprendre à utiliser les **rôles Ansible** comme des professionnels, étape par étape, avec des explications claires et des exemples concrets.


---

# **1. Qu'est-ce qu'un rôle Ansible ?**

Un **rôle Ansible** est une méthode pour organiser vos **tâches**, **variables**, **templates**, et autres fichiers nécessaires à l'automatisation. C'est comme une boîte à outils qui contient tout ce dont vous avez besoin pour configurer une partie de votre infrastructure.

Imaginez que vous devez configurer plusieurs serveurs avec Apache, MariaDB et Postfix. Sans rôle, vous mettez tout dans un seul gros fichier, ce qui devient vite compliqué. Avec des rôles, vous séparez chaque configuration dans des modules indépendants, faciles à réutiliser.

---

# **2. Pourquoi utiliser les rôles ?**

- **Organisation** : Les rôles mettent tout au bon endroit. Plus de chaos !
- **Réutilisation** : Une fois un rôle créé, vous pouvez l'utiliser dans d'autres projets.
- **Collaboration** : Avec des rôles, toute l'équipe comprend la structure.
- **Facilité** : Ils rendent vos playbooks plus courts et plus propres.

---

# **3. Structure d’un rôle Ansible**

Un rôle suit une **structure fixe**, comme un classeur avec des sections spécifiques pour chaque type de fichier. Voici à quoi cela ressemble :

```
roles/
├── webserver/              # Nom du rôle
│   ├── tasks/              # Tâches principales
│   │   └── main.yml        # Liste des actions à exécuter
│   ├── handlers/           # Gestion des événements (ex: redémarrer un service)
│   │   └── main.yml
│   ├── vars/               # Variables spécifiques au rôle
│   │   └── main.yml
│   ├── defaults/           # Variables par défaut
│   │   └── main.yml
│   ├── files/              # Fichiers statiques à copier (ex: configurations)
│   ├── templates/          # Templates dynamiques (fichiers Jinja2)
│   ├── meta/               # Dépendances entre rôles
│   │   └── main.yml
│   └── README.md           # Documentation du rôle
```

Chaque dossier a un rôle précis :
- **tasks/** : Ce que le rôle doit faire.
- **handlers/** : Ce qui se passe après certaines tâches (ex: redémarrer un service).
- **vars/** et **defaults/** : Les valeurs utilisées par le rôle.
- **files/** et **templates/** : Les fichiers à copier sur les machines.
- **meta/** : Si un rôle dépend d’autres rôles.

---

# **4. Création d’un rôle pas à pas**

### **4.1 Préparer l’environnement**
1. **Créer un répertoire de projet** :
   ```bash
   mkdir ansible_roles_project
   cd ansible_roles_project
   mkdir roles
   ```

2. **Créer un rôle avec Ansible Galaxy** :
   ```bash
   ansible-galaxy init roles/webserver
   ```
   Cela génère automatiquement la structure complète du rôle.

### **4.2 Explorer la structure du rôle**
Tapez :
```bash
tree roles/webserver
```

Vous verrez ceci :
```
roles/webserver/
├── defaults/
├── files/
├── handlers/
├── meta/
├── tasks/
├── templates/
├── vars/
└── README.md
```

Chaque dossier est vide au début, sauf `tasks/main.yml`.

---

# **5. Développer un rôle complet**

### **Exemple : Rôle `webserver` pour Apache**

Nous allons créer un rôle pour :
1. Installer Apache.
2. Configurer un fichier de site web.
3. Redémarrer Apache après les modifications.

#### **5.1 Ajouter des tâches (tasks)**
Ouvrez `roles/webserver/tasks/main.yml` et écrivez :
```yaml
---
- name: Installer Apache
  apt:
    name: apache2
    state: present
    update_cache: yes
  notify: Redémarrer Apache

- name: Copier la configuration du site
  template:
    src: apache.conf.j2
    dest: /etc/apache2/sites-available/000-default.conf
  notify: Redémarrer Apache

- name: Activer le site par défaut
  command: a2ensite 000-default
  notify: Redémarrer Apache
```

#### **5.2 Ajouter un événement (handlers)**
Ouvrez `roles/webserver/handlers/main.yml` :
```yaml
---
- name: Redémarrer Apache
  service:
    name: apache2
    state: restarted
```

#### **5.3 Ajouter des variables**
Ouvrez `roles/webserver/vars/main.yml` :
```yaml
---
apache_port: 80
apache_root: "/var/www/html"
```

#### **5.4 Ajouter un template**
Créez `roles/webserver/templates/apache.conf.j2` :
```apache
<VirtualHost *:{{ apache_port }}>
    DocumentRoot {{ apache_root }}
    <Directory {{ apache_root }}>
        AllowOverride All
    </Directory>
</VirtualHost>
```

---

# **6. Utilisation du rôle dans un playbook**

Créez un playbook principal `site.yml` :
```yaml
---
- name: Configurer les serveurs web
  hosts: webservers
  become: yes
  roles:
    - webserver
```

---

# **7. Exécution du rôle**

1. **Créer un fichier d’inventaire** :
   Créez `inventory.ini` :
   ```ini
   [webservers]
   web1 ansible_host=192.168.1.101 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   ```

2. **Exécuter le playbook** :
   ```bash
   ansible-playbook -i inventory.ini site.yml
   ```

---

# **8. Améliorations avancées**

### **8.1 Ajouter des dépendances**
Si votre rôle dépend d’un autre, éditez `roles/webserver/meta/main.yml` :
```yaml
---
dependencies:
  - role: common
```

### **8.2 Protéger les secrets avec Ansible Vault**
1. **Créer un fichier Vault** :
   ```bash
   ansible-vault create roles/webserver/vars/secrets.yml
   ```
   Ajoutez :
   ```yaml
   db_password: "secure_password_123"
   ```

2. **Appeler les secrets dans les tâches** :
   ```yaml
   - name: Configurer une base de données
     mysql_user:
       name: admin
       password: "{{ db_password }}"
   ```

3. **Exécuter avec le mot de passe Vault** :
   ```bash
   ansible-playbook -i inventory.ini site.yml --ask-vault-pass
   ```

---

# **9. Conclusion**

Les rôles Ansible permettent de structurer vos configurations de manière professionnelle, même pour des projets complexes. Avec ce cours, vous pouvez :
- Créer et organiser vos rôles.
- Réutiliser votre travail pour d'autres projets.
- Protéger vos secrets avec Ansible Vault.

---

# **10. Exercices pratiques**

1. **Créer un rôle pour installer MariaDB** :
   - Installez MariaDB.
   - Configurez un mot de passe root (utilisez Ansible Vault).
   - Redémarrez le service après modification.

2. **Créer un rôle pour un serveur mail (Postfix)** :
   - Installez Postfix.
   - Configurez un fichier de configuration par défaut.

3. **Intégrer tous vos rôles dans un seul playbook et testez-les sur une infrastructure de test.**
