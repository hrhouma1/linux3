# Chapitre 4 : Variables, Facts et Registres dans Ansible

Ce chapitre explore en détail l'utilisation des **variables**, des **facts** et des **registres** dans Ansible. Voici une traduction complète et détaillée.

---

### 1. Travailler avec des variables

Les variables permettent de gérer les différences entre les systèmes que vous gérez avec Ansible. Les concepts abordés incluent la définition, l'accès, et l'organisation des variables dans les fichiers.

#### Définir et référencer des variables

- Vous pouvez définir des variables directement dans un playbook à l'aide du mot-clé `vars`.  
  Exemple :
  ```yaml
  ---
  - name: Travailler avec des variables
    hosts: all
    vars:
      fav_color: jaune
  ```

- Pour accéder à une variable, Ansible utilise le système de templates Jinja2. Entourez la variable avec des doubles accolades :  
  ```yaml
  Mon couleur préférée est {{ fav_color }}
  ```

#### Exemple pratique : Playbook avec une variable
Créez un fichier nommé `variables-playbook.yml` :
```yaml
---
- name: Travailler avec des variables
  hosts: node1
  vars:
    fav_color: jaune
  tasks:
    - name: Afficher la couleur préférée
      debug:
        msg: "Ma couleur préférée est {{ fav_color }}."
```

#### Exécuter le playbook
Commande :
```bash
ansible-playbook variables-playbook.yml
```

**Résultat attendu** :
```yaml
TASK [Afficher la couleur préférée] ***
ok: [node1] => {
    "msg": "Ma couleur préférée est jaune."
}
```

---

### 2. Créer des listes et des dictionnaires

Les variables peuvent contenir des **listes** ou des **dictionnaires** :

- **Liste** :  
  ```yaml
  vars:
    port_nums: [21, 22, 23, 80, 443]
  ```

- **Dictionnaire** :  
  ```yaml
  vars:
    users:
      bob:
        uid: 1001
        shell: /bin/bash
      alice:
        uid: 1002
        shell: /bin/sh
  ```

#### Accès aux éléments

- Liste :  
  ```yaml
  Première valeur : {{ port_nums[0] }}
  ```

- Dictionnaire :  
  ```yaml
  Shell de Bob : {{ users.bob.shell }}
  ```

---

### 3. Inclure des variables externes

Vous pouvez inclure des fichiers de variables externes :

- Exemple d'un fichier `myvars.yml` :
  ```yaml
  ---
  port_nums: [21, 22, 23, 80, 443]
  users:
    bob:
      uid: 1001
      shell: /bin/bash
  ```

- Inclusion dans un playbook :
  ```yaml
  vars_files:
    - myvars.yml
  ```

---

### 4. Demander une saisie utilisateur

Avec `vars_prompt`, vous pouvez demander à l'utilisateur de définir une variable pendant l'exécution.

Exemple :
```yaml
---
- name: Saluer l'utilisateur
  hosts: localhost
  vars_prompt:
    - name: username
      prompt: Quel est votre nom ?
      private: no
  tasks:
    - name: Dire bonjour
      debug:
        msg: "Bonjour {{ username }} !"
```

---

### 5. Variables par hôte et groupe

#### Variables spécifiques aux hôtes
1. Créez un répertoire `host_vars`.
2. Ajoutez un fichier pour chaque hôte :
   ```bash
   echo "message: Je suis un serveur proxy" > host_vars/node1.yml
   ```

#### Variables par groupe
1. Créez un répertoire `group_vars`.
2. Ajoutez un fichier par groupe :
   ```bash
   echo "pkg: nginx" > group_vars/webservers.yml
   ```

---

### 6. Les Facts dans Ansible

Les **facts** sont des variables système collectées automatiquement par Ansible via le module `setup`. Exemple :

Commande :
```bash
ansible node1 -m setup
```

Exemple pour afficher des facts spécifiques :
```yaml
- name: Afficher les facts
  hosts: node1
  tasks:
    - name: Adresse IP
      debug:
        msg: "Adresse IP : {{ ansible_facts.default_ipv4.address }}"
    - name: OS
      debug:
        msg: "Système d'exploitation : {{ ansible_facts.distribution }}"
```

---

### 7. Créer des Facts personnalisés

#### Étapes :
1. Créez un fichier `.fact` :
   ```bash
   echo -e "[custom]\ncolor=blue\nmovie=Inception" > custom.fact
   ```
2. Copiez ce fichier sur les hôtes gérés dans `/etc/ansible/facts.d`.

Playbook pour automatiser cela :
```yaml
---
- name: Ajouter des faits personnalisés
  hosts: node1
  tasks:
    - name: Créer le répertoire facts.d
      file:
        path: /etc/ansible/facts.d
        state: directory
    - name: Copier le fichier de faits
      copy:
        src: custom.fact
        dest: /etc/ansible/facts.d/custom.fact
```

---

### 8. Capturer la sortie avec les Registres

Les **registres** stockent la sortie des commandes ou tâches. Exemple :

```yaml
---
- name: Capturer la sortie
  hosts: node1
  tasks:
    - name: Exécuter uptime
      command: uptime
      register: uptime_result
    - name: Afficher la sortie
      debug:
        msg: "Temps d'activité : {{ uptime_result.stdout }}"
```

---

### 9. Exercice

**Objectif** :
1. Exécuter la commande `free -h` et capturer la sortie.
2. Afficher l'adresse IPv4 de chaque hôte.

**Solution** :
```yaml
---
- name: Lab Chapitre 4
  hosts: all
  tasks:
    - name: Afficher la mémoire disponible
      command: free -h
      register: memory_output
    - name: Afficher la sortie mémoire
      debug:
        msg: "Mémoire : {{ memory_output.stdout }}"
    - name: Afficher l'adresse IPv4
      debug:
        msg: "Adresse IPv4 : {{ ansible_facts.default_ipv4.address }}"
```
