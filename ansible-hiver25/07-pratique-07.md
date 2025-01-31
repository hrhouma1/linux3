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



--------------------
# Annexe 1 
---------------------

### ✅ **Commandes AdHoc pour Vérifier Chaque Étape du Playbook**

Voici les commandes AdHoc à exécuter après chaque tâche pour s'assurer que les configurations ont bien été appliquées.



## **1️⃣ Vérification de l'installation des paquets (`with_items`)**
### **Commande**
```bash
ansible all -i inventory.ini -m shell -a "which vim git curl"
```
### **Résultat attendu**
Les chemins vers `vim`, `git`, et `curl` doivent s'afficher, prouvant leur installation.

---

## **2️⃣ Vérification des paramètres SSH (`with_dict`)**
### **Commande**
```bash
ansible all -i inventory.ini -m shell -a "grep -E 'MaxSessions|MaxStartups|AllowTcpForwarding' /etc/ssh/sshd_config"
```
### **Résultat attendu**
Le terminal doit afficher :
```
MaxSessions 10
MaxStartups 3
AllowTcpForwarding yes
```
### **Redémarrer SSH si nécessaire**
```bash
ansible all -i inventory.ini -m service -a "name=sshd state=restarted"
```

---

## **3️⃣ Vérification de la création des utilisateurs (`with_sequence`)**
### **Commande**
```bash
ansible all -i inventory.ini -m shell -a "cat /etc/passwd | grep user"
```
### **Résultat attendu**
Chaque utilisateur `user1`, `user2`, ..., `user5` doit apparaître avec son UID.

---

## **4️⃣ Vérification de la copie des fichiers (`with_fileglob`)**
### **Commande**
```bash
ansible all -i inventory.ini -m shell -a "ls -l /tmp/"
```
### **Résultat attendu**
Tous les fichiers `.txt` spécifiés dans `/path/to/files/` doivent être présents dans `/tmp/` des conteneurs.

---

## **5️⃣ Vérification des fichiers imbriqués (`with_nested`)**
### **Commande**
```bash
ansible all -i inventory.ini -m shell -a "ls -l /home/user1 /home/user2"
```
### **Résultat attendu**
Les fichiers suivants doivent être présents :
```
/home/user1/file1.txt
/home/user1/file2.txt
/home/user2/file1.txt
/home/user2/file2.txt
```

---

## **🔍 Vérification Manuelle en Entrant dans un Conteneur**
Si tu veux entrer dans un conteneur et faire les vérifications directement :

1. **Lister les conteneurs**  
   ```bash
   docker ps
   ```
   ✅ **Attendu :** Voir les IDs et noms des conteneurs en cours d'exécution.

2. **Se connecter à un conteneur**  
   ```bash
   docker exec -it <container_id> /bin/bash
   ```
   Remplace `<container_id>` par l'ID réel.

3. **Vérifier les paquets installés**  
   ```bash
   which vim git curl
   ```

4. **Vérifier les utilisateurs créés**  
   ```bash
   cat /etc/passwd | grep user
   ```

5. **Vérifier les fichiers copiés**  
   ```bash
   ls -l /tmp/
   ```

6. **Vérifier les répertoires et fichiers imbriqués**  
   ```bash
   ls -l /home/user1 /home/user2
   ```

7. **Quitter le conteneur**  
   ```bash
   exit
   ```

---

## **📌 Vérification Automatisée sur Tous les Conteneurs**
Si tu veux automatiser la vérification sur **tous les conteneurs**, exécute :

```bash
for id in $(docker ps -q); do 
    echo "=== Vérification dans $id ==="; 
    docker exec -it $id ls -l /tmp/;
    docker exec -it $id grep -E 'MaxSessions|MaxStartups|AllowTcpForwarding' /etc/ssh/sshd_config;
    docker exec -it $id cat /etc/passwd | grep user;
    echo "===========================";
done
```
✅ **Attendu :** Un résumé des vérifications pour chaque conteneur.

---

### 🚀 **Avec ces commandes, nous pouvons valider chaque étape de ton playbook et t'assurer qu'Ansible a bien appliqué les configurations !**
