### **Chapitre 7 : Les Templates Jinja2 dans Ansible**

Les templates Jinja2 permettent de générer des fichiers dynamiques en intégrant des variables, des facts, des boucles et des conditions. Dans ce chapitre, vous apprendrez à :
1. Accéder aux **variables** et **facts** dans un template Jinja2.
2. Utiliser des **conditions** et des **boucles** dans les templates.
3. Dynamiser vos fichiers de configuration grâce à ces fonctionnalités.

---

### **1. Accéder aux Variables dans Jinja2**

#### **a) Structure des fichiers Jinja2**
- Les fichiers Jinja2 doivent avoir l'extension `.j2`.
- Par convention, ils sont placés dans un répertoire nommé `templates` dans votre projet.

#### **b) Exemple : Création d’un fichier HTML dynamique**

**1. Créez le répertoire et le fichier template :**
```bash
mkdir templates
cd templates
cat > index.j2 <<EOF
A message from {{ inventory_hostname }}
{{ webserver_message }}
EOF
```

- `{{ inventory_hostname }}` : une variable spéciale d’Ansible qui contient le nom de l’hôte en cours.
- `{{ webserver_message }}` : une variable définie dans le playbook.

**2. Écrivez un playbook pour utiliser le template :**
```yaml
---
- name: Vérifier si Apache fonctionne
  hosts: webservers
  vars:
    webserver_message: "Je suis prêt pour la ligne d'arrivée."
  tasks:
    - name: Démarrer Apache
      service:
        name: httpd
        state: started
    - name: Créer index.html avec Jinja2
      template:
        src: index.j2
        dest: /var/www/html/index.html
```

**3. Exécutez le playbook :**
```bash
ansible-playbook check-apache.yml
```

**4. Vérifiez le résultat :**
```bash
ansible webservers -m command -a "cat /var/www/html/index.html"
```

**Sortie attendue :**
```text
A message from node2
Je suis prêt pour la ligne d'arrivée.
A message from node3
Je suis prêt pour la ligne d'arrivée.
```

---

### **2. Accéder aux Facts dans Jinja2**

Les **facts** sont des informations collectées par Ansible sur les hôtes. Vous pouvez les inclure dans vos templates Jinja2 pour personnaliser les fichiers.

#### **Exemple : Résumé d'informations sur un serveur**

**1. Créez le fichier `info.j2` :**
```bash
cat > templates/info.j2 <<EOF
Résumé des informations du serveur
----------------------------------
Nom d'hôte : {{ ansible_facts['hostname'] }}
FQDN : {{ ansible_facts['fqdn'] }}
Adresse IP : {{ ansible_facts['default_ipv4']['address'] }}
Distribution : {{ ansible_facts['distribution'] }}
Version : {{ ansible_facts['distribution_version'] }}
Serveurs DNS : {{ ansible_facts['dns']['nameservers'] }}
Mémoire totale : {{ ansible_facts['memtotal_mb'] }} MB
Mémoire libre : {{ ansible_facts['memfree_mb'] }} MB
EOF
```

**2. Écrivez le playbook :**
```yaml
---
- name: Résumé des informations du serveur
  hosts: all
  tasks:
    - name: Créer server-info.txt avec Jinja2
      template:
        src: info.j2
        dest: /tmp/server-info.txt
```

**3. Exécutez le playbook et vérifiez le fichier généré :**
```bash
ansible-playbook server-info.yml
ansible all -m command -a "cat /tmp/server-info.txt"
```

---

### **3. Utiliser des Conditions dans Jinja2**

Vous pouvez utiliser des **conditions** dans vos templates pour ajuster le contenu selon des tests.

#### **Exemple : Vérifier le statut de SELinux**

**1. Créez le fichier `selinux.j2` :**
```bash
cat > templates/selinux.j2 <<EOF
{% set selinux_status = ansible_facts['selinux']['status'] %}
{% if selinux_status == "enabled" %}
SELINUX EST ACTIVÉ
{% elif selinux_status == "disabled" %}
SELINUX EST DÉSACTIVÉ
{% else %}
SELINUX N'EST PAS DISPONIBLE
{% endif %}
EOF
```

**2. Écrivez le playbook :**
```yaml
---
- name: Vérifier le statut de SELinux
  hosts: all
  tasks:
    - name: Afficher le statut de SELinux
      debug:
        msg: "{{ ansible_facts['selinux']['status'] }}"
    - name: Créer selinux.out avec Jinja2
      template:
        src: selinux.j2
        dest: /tmp/selinux.out
```

**3. Exécutez le playbook et vérifiez le fichier généré :**
```bash
ansible-playbook selinux-status.yml
ansible all -m command -a "cat /tmp/selinux.out"
```

---

### **4. Utiliser des Boucles dans Jinja2**

Les boucles Jinja2 permettent de parcourir des listes ou des gammes de valeurs.

#### **Exemple : Générer dynamiquement un fichier `/etc/hosts`**

**1. Créez le fichier `hosts.j2` :**
```bash
cat > templates/hosts.j2 <<EOF
{% for host in groups['all'] %}
{{ hostvars[host].ansible_facts.default_ipv4.address }} {{ hostvars[host].ansible_facts.fqdn }} {{ hostvars[host].ansible_facts.hostname }}
{% endfor %}
EOF
```

**2. Écrivez le playbook :**
```yaml
---
- name: Mise à jour dynamique du fichier /etc/hosts
  hosts: all
  tasks:
    - name: Mettre à jour /etc/hosts avec Jinja2
      template:
        src: hosts.j2
        dest: /etc/hosts
```

**3. Exécutez le playbook et vérifiez le fichier généré :**
```bash
ansible-playbook local-dns.yml
ansible node1 -m command -a "cat /etc/hosts"
```

**Sortie attendue :**
```text
10.0.0.5 node1.linuxhandbook.local node1
10.0.0.6 node2.linuxhandbook.local node2
10.0.0.7 node3.linuxhandbook.local node3
10.0.0.8 node4.linuxhandbook.local node4
```

---

### **5. Exercice Pratique**

**Objectif :**
1. Créez un template nommé `motd.j2` avec le contenu suivant :
   ```text
   Bienvenue sur {{ inventory_hostname }}.
   Mon adresse IP est {{ ansible_facts['default_ipv4']['address'] }}.
   ```
2. Créez un playbook `lab7.yml` qui utilise ce template pour modifier `/etc/motd` sur tous les hôtes gérés.

**Solution :**

**Template `motd.j2` :**
```bash
cat > templates/motd.j2 <<EOF
Bienvenue sur {{ inventory_hostname }}.
Mon adresse IP est {{ ansible_facts['default_ipv4']['address'] }}.
EOF
```

**Playbook `lab7.yml` :**
```yaml
---
- name: Modifier le fichier /etc/motd
  hosts: all
  tasks:
    - name: Mettre à jour /etc/motd avec Jinja2
      template:
        src: motd.j2
        dest: /etc/motd
```

**Exécution :**
```bash
ansible-playbook lab7.yml
ansible all -m command -a "cat /etc/motd"
```

---

### **Résumé du Chapitre**

Vous avez appris à utiliser les **templates Jinja2** pour :
1. Dynamiser vos fichiers en accédant aux **variables** et **facts**.
2. Adapter vos fichiers à l’aide de **conditions**.
3. Gérer des contenus complexes avec des **boucles**.

Les templates Jinja2 ajoutent une flexibilité immense à vos déploiements Ansible, permettant de créer des configurations précises et personnalisées pour chaque hôte. Dans le prochain chapitre, vous découvrirez comment sécuriser vos fichiers sensibles avec **Ansible Vault**.
