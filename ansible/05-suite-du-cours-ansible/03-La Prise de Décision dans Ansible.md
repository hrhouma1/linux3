### **Chapitre 6 : La Prise de Décision dans Ansible**

Dans ce chapitre, nous explorons comment ajouter des **conditions**, gérer les **exceptions** et utiliser des **handlers** dans vos playbooks Ansible. Ces fonctionnalités permettent de rendre vos automatisations plus intelligentes, flexibles et adaptées aux besoins spécifiques des hôtes gérés.

---

### **1. Exécuter des tâches sous conditions avec `when`**

La directive `when` permet d'exécuter une tâche uniquement si une condition est remplie.

#### **a) Utiliser `when` avec les Facts**
Les **facts** sont des informations sur vos hôtes collectées automatiquement par Ansible. Vous pouvez utiliser `when` pour exécuter une tâche sur certains hôtes en fonction de ces facts.

**Exemple : Détecter les serveurs Ubuntu**
```yaml
---
- name: Utiliser when avec des facts
  hosts: all
  tasks:
    - name: Détecter les serveurs Ubuntu
      debug:
        msg: "Ceci est un serveur Ubuntu."
      when: ansible_facts['distribution'] == "Ubuntu"
```

**Explication :**
- La condition `ansible_facts['distribution'] == "Ubuntu"` vérifie si le système d’exploitation est Ubuntu.
- La tâche est ignorée pour les hôtes non Ubuntu.

**Résultat attendu :**
La tâche est exécutée uniquement pour les hôtes Ubuntu.

---

#### **b) Utiliser `when` avec des registres**
Les **registres** stockent les sorties des tâches précédentes. Vous pouvez utiliser ces données pour décider d'exécuter ou non une tâche.

**Exemple : Détecter les serveurs CentOS**
```yaml
---
- name: Utiliser when avec des registres
  hosts: all
  tasks:
    - name: Sauvegarder le contenu de /etc/os-release
      command: cat /etc/os-release
      register: os_release
    - name: Détecter les serveurs CentOS
      debug:
        msg: "Ceci est un serveur CentOS."
      when: os_release.stdout.find('CentOS') != -1
```

**Explication :**
- La commande `cat /etc/os-release` est exécutée et sa sortie est enregistrée dans `os_release`.
- La condition `os_release.stdout.find('CentOS') != -1` vérifie si la sortie contient le mot "CentOS".

---

#### **c) Tester plusieurs conditions**
Vous pouvez combiner plusieurs conditions avec les opérateurs logiques `and`, `or` et `not`.

**Exemple : Redémarrer uniquement les serveurs CentOS 8**
```yaml
---
- name: Redémarrer les serveurs CentOS 8
  hosts: all
  tasks:
    - name: Redémarrer
      reboot:
      when: >
        ansible_facts['distribution'] == "CentOS" and
        ansible_facts['distribution_major_version'] == "8"
```

**Exemple : Redémarrer les serveurs CentOS ou RedHat**
```yaml
when: >
  ansible_facts['distribution'] == "CentOS" or
  ansible_facts['distribution'] == "RedHat"
```

---

#### **d) Utiliser `when` avec des boucles**
Quand `when` est combiné avec une boucle, la condition est testée pour chaque élément.

**Exemple : Afficher les nombres pairs entre 1 et 10**
```yaml
---
- name: Afficher les nombres pairs
  hosts: localhost
  tasks:
    - name: Afficher les nombres pairs
      debug:
        msg: "Le nombre {{ item }} est pair."
      loop: "{{ range(1, 11) | list }}"
      when: item % 2 == 0
```

---

#### **e) Utiliser `when` avec des variables**
Vous pouvez utiliser vos propres variables dans les conditions.

**Exemple : Vérifier les disponibilités**
```yaml
---
- name: Vérifier si on est disponible
  hosts: localhost
  vars:
    weekend: true
    on_call: "no"
  tasks:
    - name: Exécuter si on est disponible
      debug:
        msg: "Vous êtes disponible !"
      when: weekend and not on_call | bool
```

**Astuce :** Testez si une variable est définie avec `is defined`.

---

### **2. Gérer les exceptions avec des Blocks**

Les blocks permettent de regrouper des tâches et de gérer les erreurs avec les sections `rescue` (si une erreur survient) et `always` (toujours exécutée).

#### **a) Exemple simple avec un Block**
```yaml
---
- name: Installer et démarrer Apache
  hosts: webservers
  tasks:
    - name: Installer et démarrer Apache
      block:
        - name: Installer httpd
          yum:
            name: httpd
            state: latest
        - name: Démarrer et activer httpd
          service:
            name: httpd
            state: started
            enabled: yes
      rescue:
        - name: Gérer l'erreur
          debug:
            msg: "Une erreur est survenue, tentative de réparation..."
      always:
        - name: Toujours exécuter
          debug:
            msg: "Ce message est toujours affiché, peu importe ce qui s'est passé."
```

---

### **3. Exécuter des tâches conditionnelles avec les Handlers**

Les **handlers** sont des tâches spéciales qui s'exécutent uniquement lorsqu'elles sont notifiées par d'autres tâches.

#### **a) Exemple simple de Handler**
```yaml
---
- name: Exemple de handler
  hosts: localhost
  tasks:
    - name: Créer un groupe
      group:
        name: engineers
      notify: ajouter utilisateur
  handlers:
    - name: ajouter utilisateur
      user:
        name: elliot
        groups: engineers
        append: yes
```

**Explication :**
- Si le groupe `engineers` est créé, le handler `ajouter utilisateur` est déclenché.
- Si le groupe existe déjà, le handler n'est pas exécuté (idempotence).

---

### **4. Configurer des services avec des Handlers**

Les handlers sont particulièrement utiles pour redémarrer des services après une modification de configuration.

#### Exemple : Configurer SSH
```yaml
---
- name: Configurer SSH
  hosts: all
  tasks:
    - name: Modifier la configuration SSH
      blockinfile:
        path: /etc/ssh/sshd_config
        block: |
          MaxAuthTries 4
          Banner /etc/motd
          X11Forwarding no
      notify: redémarrer ssh
  handlers:
    - name: redémarrer ssh
      service:
        name: sshd
        state: restarted
```

---

### **Résumé**

Dans ce chapitre, vous avez appris à :
1. **Exécuter des tâches conditionnellement** avec `when` en utilisant des facts, des registres ou vos propres variables.
2. **Gérer les erreurs** avec les blocks et leurs sections `rescue` et `always`.
3. **Déclencher des tâches** spécifiques avec des handlers, notamment pour configurer des services de manière idempotente.

### **Exercice**
- Créez un playbook `lab6.yml` qui :
  1. Installe le paquet `nfs-utils` uniquement sur les serveurs CentOS.
  2. Utilise `when` pour conditionner l'installation.
 




### **Solution de l'Exercice : Installer `nfs-utils` uniquement sur CentOS**

Voici un playbook qui répond à l'exercice demandé.

```yaml
---
- name: Installer nfs-utils uniquement sur CentOS
  hosts: all
  tasks:
    - name: Installer le paquet nfs-utils
      yum:
        name: nfs-utils
        state: present
      when: ansible_facts['distribution'] == "CentOS"
```

### **Explications :**
1. **Tâche unique :** Cette tâche utilise le module `yum` pour installer le paquet `nfs-utils`.
2. **Condition `when` :** La condition `ansible_facts['distribution'] == "CentOS"` vérifie si le système d’exploitation de l’hôte est CentOS.
3. **Hôtes ciblés :** Le playbook s'exécute sur tous les hôtes (`hosts: all`), mais la tâche ne s'exécute que sur ceux qui remplissent la condition.

#### **Exécution :**
Pour exécuter ce playbook :
```bash
ansible-playbook lab6.yml
```

---

### **Résumé du Chapitre**

Ce chapitre vous a enseigné les bases essentielles pour rendre vos playbooks Ansible plus intelligents et adaptables. Voici ce que vous avez appris :

1. **Exécuter des tâches conditionnellement (`when`) :**
   - Basé sur les facts collectés par Ansible.
   - En utilisant les registres pour prendre des décisions à partir des sorties de tâches.
   - En combinant plusieurs conditions avec `and`, `or` et `not`.

2. **Gérer les erreurs avec des Blocks :**
   - Les sections `block` pour regrouper des tâches.
   - Les sections `rescue` pour gérer les erreurs et les corriger.
   - La section `always` pour exécuter des tâches systématiquement.

3. **Handlers pour les modifications conditionnelles :**
   - Déclencher des tâches spécifiques (comme redémarrer un service) uniquement lorsqu'une autre tâche provoque un changement.
   - Garantir l'idempotence des tâches avec les handlers.

### **Pratique Recommandée :**
1. **Utilisation judicieuse des conditions :** Simplifiez vos playbooks en plaçant des conditions au bon endroit, plutôt que de dupliquer les tâches.
2. **Testez les erreurs avec Blocks :** Pour des environnements complexes, intégrez des Blocks avec `rescue` et `always` afin de gérer proprement les défaillances.
3. **Optimisez avec des handlers :** Utilisez des handlers pour gérer les services (redémarrer, recharger) uniquement lorsque cela est nécessaire.

---

Dans le **prochain chapitre**, vous découvrirez comment utiliser les **templates Jinja2** pour créer et gérer dynamiquement des fichiers de configuration. Cette étape est essentielle pour rendre vos déploiements encore plus flexibles et puissants.
