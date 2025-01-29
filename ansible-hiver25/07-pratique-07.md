## 📝 Introduction

Dans ce chapitre, nous allons explorer les boucles **`with_*`** dans Ansible, qui permettent d'itérer sur des listes, des dictionnaires, des plages, et même des données complexes comme des fichiers ou des sous-éléments. Ces boucles offrent une flexibilité accrue pour automatiser des tâches répétitives sur différents types de données.

### Objectifs :
- Apprendre à utiliser les différentes variantes de `with_*` pour gérer des tâches complexes.
- Intégrer ces concepts dans une infrastructure Docker avec des conteneurs multi-distributions.
- Automatiser des configurations et des déploiements en utilisant les bonnes pratiques Ansible.

---

## 📋 Table des Matières

1. [Étape Préparatoire : Configuration de l'Infrastructure Docker](#etape-preparatoire)
2. [Partie 1 : Utiliser `with_items` pour Itérer sur une Liste](#partie1)
3. [Partie 2 : Utiliser `with_dict` pour Itérer sur un Dictionnaire](#partie2)
4. [Partie 3 : Utiliser `with_sequence` pour Créer une Plage de Nombres](#partie3)
5. [Partie 4 : Utiliser `with_file` pour Gérer des Fichiers](#partie4)
6. [Partie 5 : Utiliser `with_nested` pour Combiner Plusieurs Listes](#partie5)
7. [Conclusion](#conclusion)

---

<a name="etape-preparatoire"></a>
## ⚙️ Étape Préparatoire : Configuration de l'Infrastructure Docker

Suivez les étapes décrites dans votre environnement pour configurer l'infrastructure Docker avec `docker-compose.yml`. Une fois les conteneurs en cours d'exécution, créez un inventaire Ansible pour définir les nœuds Docker.

### Exemple d'Inventaire :

**`inventory.ini`** :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node2 ansible_host=172.20.0.3 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node3 ansible_host=172.20.0.4 ansible_user=root ansible_python_interpreter=/usr/bin/python3
```

---

<a name="partie1"></a>
## 📝 Partie 1 : Utiliser `with_items` pour Itérer sur une Liste

### Exemple : Installation de Plusieurs Paquets sur Tous les Conteneurs

**Playbook : `install-packages.yml`**

```yaml
---
- name: Installer des paquets sur les conteneurs
  hosts: node_containers
  become: yes
  tasks:
    - name: Installer une liste de paquets
      package:
        name: "{{ item }}"
        state: present
      with_items:
        - vim
        - curl
        - git
```

### Commande pour Exécuter :

```bash
ansible-playbook -i inventory.ini install-packages.yml
```

### Résultat :
Les paquets `vim`, `curl`, et `git` seront installés sur chaque conteneur.

---

<a name="partie2"></a>
## 📝 Partie 2 : Utiliser `with_dict` pour Itérer sur un Dictionnaire

### Exemple : Configurer des Paramètres dans un Fichier

**Playbook : `configure-settings.yml`**

```yaml
---
- name: Configurer des paramètres système
  hosts: node_containers
  become: yes
  vars:
    system_settings:
      MaxSessions: 10
      MaxStartups: 3
      AllowTcpForwarding: yes
  tasks:
    - name: Ajouter des paramètres au fichier de configuration
      lineinfile:
        path: /etc/ssh/sshd_config
        line: "{{ item.key }} {{ item.value }}"
      with_dict: "{{ system_settings }}"
```

### Commande pour Exécuter :

```bash
ansible-playbook -i inventory.ini configure-settings.yml
```

### Résultat :
Les paramètres définis dans le dictionnaire `system_settings` seront ajoutés au fichier `/etc/ssh/sshd_config`.

---

<a name="partie3"></a>
## 📝 Partie 3 : Utiliser `with_sequence` pour Créer une Plage de Nombres

### Exemple : Créer des Utilisateurs avec un UID

**Playbook : `create-users.yml`**

```yaml
---
- name: Créer des utilisateurs avec des UIDs spécifiques
  hosts: node_containers
  become: yes
  tasks:
    - name: Ajouter des utilisateurs
      user:
        name: "user{{ item }}"
        uid: "100{{ item }}"
        shell: "/bin/bash"
        state: present
      with_sequence: start=1 end=5
```

### Commande pour Exécuter :

```bash
ansible-playbook -i inventory.ini create-users.yml
```

### Résultat :
Les utilisateurs `user1`, `user2`, ..., `user5` seront créés avec des UIDs correspondants (`1001`, `1002`, ..., `1005`).

---

<a name="partie4"></a>
## 📝 Partie 4 : Utiliser `with_file` pour Gérer des Fichiers

### Exemple : Copier Plusieurs Fichiers sur Tous les Conteneurs

**Playbook : `copy-files.yml`**

```yaml
---
- name: Copier des fichiers sur les conteneurs
  hosts: node_containers
  become: yes
  tasks:
    - name: Copier un fichier
      copy:
        src: "{{ item }}"
        dest: "/tmp/{{ item | basename }}"
      with_fileglob: "/path/to/files/*.txt"
```

### Commande pour Exécuter :

```bash
ansible-playbook -i inventory.ini copy-files.yml
```

### Résultat :
Tous les fichiers `.txt` du répertoire spécifié seront copiés dans `/tmp` sur chaque conteneur.

---

<a name="partie5"></a>
## 📝 Partie 5 : Utiliser `with_nested` pour Combiner Plusieurs Listes

### Exemple : Créer des Combinaisons de Répertoires et Fichiers

**Playbook : `create-directories.yml`**

```yaml
---
- name: Créer des répertoires et fichiers
  hosts: node_containers
  become: yes
  tasks:
    - name: Créer des combinaisons de répertoires et fichiers
      file:
        path: "/home/{{ item.0 }}/{{ item.1 }}"
        state: touch
      with_nested:
        - ["user1", "user2"]
        - ["file1.txt", "file2.txt"]
```

### Commande pour Exécuter :

```bash
ansible-playbook -i inventory.ini create-directories.yml
```

### Résultat :
Les fichiers suivants seront créés :
- `/home/user1/file1.txt`
- `/home/user1/file2.txt`
- `/home/user2/file1.txt`
- `/home/user2/file2.txt`

---

<a name="conclusion"></a>
## 🎯 Conclusion

Dans ce chapitre, vous avez appris à utiliser les variantes de boucles `with_*` pour :

- **Itérer sur des listes** avec `with_items`.
- **Travailler avec des dictionnaires** avec `with_dict`.
- **Générer des séquences numériques** avec `with_sequence`.
- **Gérer des fichiers** avec `with_fileglob`.
- **Créer des combinaisons complexes** avec `with_nested`.

Ces concepts rendent vos playbooks plus dynamiques, modulaires, et puissants pour gérer des infrastructures complexes. N'hésitez pas à les intégrer dans vos projets Ansible pour améliorer votre efficacité.

---

### 📚 Ressources Complémentaires
- [Documentation Ansible sur les Boucles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)
- [Filtres Jinja2 pour Manipuler les Données](https://jinja.palletsprojects.com/en/3.0.x/templates/)

