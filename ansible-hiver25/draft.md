




---

# 🛠️ Annexe 1 - Résolution d'un problème
---

L'objectif est de démontrer **progressivement** comment résoudre les erreurs rencontrées lors de la création d'utilisateurs avec Ansible, notamment :  
- **L'absence du module `passlib`** empêche le hashage des mots de passe.  
- **L'absence de `pip3`** empêche l'installation automatique de `passlib`.  

Nous allons exécuter **six playbooks** pour identifier et corriger ces erreurs.

---

## 🎯 **Première partie : Problème de hashage des mots de passe (`passlib` manquant)**

### **🔴 1️⃣ Playbook 1 : Observer l'erreur liée au hashage (`passlib` manquant)**
Ce premier playbook déclenche volontairement une **erreur** en tentant de hasher un mot de passe avec `password_hash('sha512')`, alors que le module `passlib` n'est pas installé.

#### **Création du fichier `add-users-error.yml`**
```yaml
---
- name: Ajouter Plusieurs Utilisateurs (Avec Erreur)
  hosts: database
  become: yes
  vars:
    db_users:
      - username: "alice"
        password: "password1"
        uid: 1001
      - username: "bob"
        password: "password2"
        uid: 1002
      - username: "charlie"
        password: "password3"
        uid: 1003
  tasks:
    - name: Créer les Utilisateurs (Hashé) ❌
      user:
        name: "{{ item.username }}"
        uid: "{{ item.uid }}"
        password: "{{ item.password | password_hash('sha512') }}"
        shell: "/bin/bash"
        state: present
      loop: "{{ db_users }}"
```

#### **Exécution**
```bash
ansible-playbook -i inventory.ini add-users-error.yml
```

🔴 **Erreur attendue** :
```
No module named 'passlib'. Unable to encrypt nor hash, passlib must be installed.
```

---

### **🟡 2️⃣ Playbook 2 : Contourner l'erreur en retirant le hashage**
Dans ce playbook, **le hashage est temporairement supprimé** pour vérifier que l'erreur disparaît.

#### **Création du fichier `add-users-no-hash.yml`**
```yaml
---
- name: Ajouter Plusieurs Utilisateurs (Sans Hash)
  hosts: database
  become: yes
  vars:
    db_users:
      - username: "alice"
        password: "password1"
        uid: 1001
      - username: "bob"
        password: "password2"
        uid: 1002
      - username: "charlie"
        password: "password3"
        uid: 1003
  tasks:
    - name: Créer les Utilisateurs (Sans Hash) ✅
      user:
        name: "{{ item.username }}"
        uid: "{{ item.uid }}"
        password: "{{ item.password }}"
        shell: "/bin/bash"
        state: present
      loop: "{{ db_users }}"
```

#### **Exécution**
```bash
ansible-playbook -i inventory.ini add-users-no-hash.yml
```

🟢 **Résultat attendu** :  
Les utilisateurs sont créés **mais sans hashage**, ce qui **n'est pas sécurisé**.

---

### **🟢 3️⃣ Playbook 3 : Installation de `passlib` et hashage sécurisé**
Ce playbook installe `passlib` avec `pip3`, puis ajoute les utilisateurs avec un mot de passe sécurisé.

#### **Création du fichier `add-users-fixed.yml`**
```yaml
---
- name: Ajouter Plusieurs Utilisateurs (Avec Hash après installation de passlib)
  hosts: database
  become: yes
  vars:
    db_users:
      - username: "alice"
        password: "password1"
        uid: 1001
      - username: "bob"
        password: "password2"
        uid: 1002
      - username: "charlie"
        password: "password3"
        uid: 1003
  tasks:
    - name: Installer `passlib` avec pip 🛠️
      ansible.builtin.pip:
        name: passlib
        state: present

    - name: Créer les Utilisateurs (Hashé) ✅
      user:
        name: "{{ item.username }}"
        uid: "{{ item.uid }}"
        password: "{{ item.password | password_hash('sha512') }}"
        shell: "/bin/bash"
        state: present
      loop: "{{ db_users }}"
```

#### **Exécution**
```bash
ansible-playbook -i inventory.ini add-users-fixed.yml
```

🔴 **Nouvelle erreur possible** :  
```
Unable to find pip3
```

---

## 🎯 **Deuxième partie : Problème d’installation de `pip3`**

### **🔎 4️⃣ Playbook 4 : Vérifier si `pip3` est installé**
Avant d’installer `passlib`, il est nécessaire de s’assurer que `pip3` est bien présent.

#### **Création du fichier `check-pip.yml`**
```yaml
---
- name: Vérifier la présence de pip3
  hosts: database
  become: yes
  tasks:
    - name: Vérifier si pip3 est installé
      command: which pip3
      register: pip3_check
      ignore_errors: yes

    - name: Afficher le résultat
      debug:
        msg: "{{ pip3_check.stdout if pip3_check.rc == 0 else 'pip3 non trouvé' }}"
```

#### **Exécution**
```bash
ansible-playbook -i inventory.ini check-pip.yml
```

🔴 **Si `pip3` est absent**, il faudra l’installer.

---

### **🛠 5️⃣ Playbook 5 : Installer `pip3` si nécessaire**
Si `pip3` est absent, ce playbook l’installe.

#### **Création du fichier `install-pip.yml`**
```yaml
---
- name: Installer pip3 si nécessaire
  hosts: database
  become: yes
  tasks:
    - name: Installer les prérequis
      package:
        name: "{{ item }}"
        state: present
      loop:
        - python3
        - python3-pip

    - name: Vérifier l'installation de pip3
      command: which pip3
      register: pip3_check

    - name: Afficher le chemin de pip3
      debug:
        msg: "pip3 est installé à : {{ pip3_check.stdout }}"
```

#### **Exécution**
```bash
ansible-playbook -i inventory.ini install-pip.yml
```

---

### **✅ 6️⃣ Playbook 6 : Installer `passlib` après `pip3`**
Enfin, `passlib` peut être installé correctement.

#### **Création du fichier `install-passlib.yml`**
```yaml
---
- name: Installer passlib après installation de pip3
  hosts: database
  become: yes
  tasks:
    - name: Vérifier si pip3 est installé
      command: which pip3
      register: pip3_check
      ignore_errors: yes

    - name: Installer passlib avec pip3
      pip:
        name: passlib
        executable: "{{ pip3_check.stdout | default('/usr/bin/pip3') }}"
        state: present
      when: pip3_check.rc == 0
```

#### **Exécution**
```bash
ansible-playbook -i inventory.ini install-passlib.yml
```

---

## 📌 **Résumé de la progression**
### **🔹 Partie 1 : Hashage des mots de passe**
| 🔢 | Playbook | Résultat |
|----|----------|----------|
| **4️** | `add-users-error.yml` | ❌ Erreur (`passlib` absent) |
| **5️⃣** | `add-users-no-hash.yml` | ⚠️ Succès mais mot de passe non sécurisé |
| **6️⃣** | `add-users-fixed.yml` | ❌ Erreur (`pip3` absent) |

### **🔹 Partie 2 : Installation de `pip3` et `passlib`**
| 🔢 | Playbook | Résultat |
|----|----------|----------|
| **7️⃣** | `check-pip.yml` | Vérifie la présence de `pip3` |
| **8️⃣** | `install-pip.yml` | Installe `pip3` si absent |
| **9️⃣** | `install-passlib.yml` | Installe `passlib` avec `pip3` |

🚀 **Cette approche assure une installation progressive et sans erreur.**



# Annexe 2



```yaml
---
- name: Ajouter Plusieurs Utilisateurs
  hosts: all
  become: yes
  vars:
    db_users:
      - username: "alice"
        password: "password1"
        uid: 1001
      - username: "bob"
        password: "password2"
        uid: 1002
      - username: "charlie"
        password: "password3"
        uid: 1003

  tasks:
    - name: Installer python3-passlib sur Ubuntu/Debian
      apt:
        name: python3-passlib
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Installer python3-passlib sur AlmaLinux
      dnf:
        name: python3-passlib
        state: present
      when: ansible_os_family == "RedHat"

    - name: Créer les Utilisateurs sur Ubuntu/Debian
      user:
        name: "{{ item.username }}"
        uid: "{{ item.uid }}"
        password: "{{ item.password | password_hash('sha512') }}"
        shell: "/bin/bash"
        state: present
      loop: "{{ db_users }}"
      when: ansible_os_family == "Debian"

    - name: Créer les Utilisateurs sur AlmaLinux
      user:
        name: "{{ item.username }}"
        uid: "{{ item.uid }}"
        password: "{{ item.password | password_hash('sha512') }}"
        shell: "/bin/bash"
        state: present
      loop: "{{ db_users }}"
      when: ansible_os_family == "RedHat"
```

Cette version du playbook :

1. Installe d'abord `python3-passlib` selon le système d'exploitation
2. Utilise des conditions `when` pour exécuter les tâches appropriées selon la famille du système d'exploitation
3. Gère séparément les systèmes basés sur Debian (Ubuntu/Debian) et RedHat (AlmaLinux)

Pour l'utiliser :

1. Créez un fichier inventory.ini avec vos nœuds
2. Exécutez le playbook avec :
```bash
ansible-playbook -i inventory.ini add-users.yml
```

Pour vérifier que les utilisateurs ont été créés correctement :
```bash
ansible all -i inventory.ini -m shell -a "getent passwd alice bob charlie"
```


# Annexe 3


==> **causes et pistes de résolution** pour les erreurs de type :

```
Unable to encrypt nor hash, passlib must be installed. No module named 'passlib'
```

Dans les logs, on observe en particulier :
- Sur **Ubuntu/Debian** : « passlib » est censé être installé via `apt` mais Ansible ne le détecte toujours pas.  
- Sur **AlmaLinux** : « No package python3-passlib available. »

Ce qui provoque l’échec de la tâche `user:` quand vous essayez de faire un `password_hash('sha512')`.  

---

# 1. Pourquoi l’erreur apparaît ?

1. **Le paquet `python3-passlib` est introuvable** sur certaines distributions (comme AlmaLinux) avec le dépôt par défaut.  
2. **La librairie est installée mais pas dans l’environnement Python qu’Ansible utilise**.  
   - Par exemple, `apt install python3-passlib` l’installe pour Python 3 de la machine, mais Ansible pourrait utiliser un autre interpréteur Python (moins fréquent si `ansible_python_interpreter` est bien `/usr/bin/python3`).  
3. **Le module `passlib` n’est tout simplement pas installé** : parfois un conflit de paquets, des dépôts obsolètes, ou un cache non mis à jour.

---

# 2. Approche Générale de Résolution

Pour qu’Ansible puisse **hasher** les mots de passe, il faut que **`passlib`** soit disponible **dans le même environnement Python** qu’Ansible.  

### Les étapes-clés sont donc :

1. **Installer Python 3 + pip** (si besoin).  
2. **Installer le paquet `passlib`** via :  
   - **Le gestionnaire de paquets système** : `apt` (Debian/Ubuntu) ou `dnf` (AlmaLinux, RedHat, CentOS, etc.).  
   - **OU** un **fallback via pip** (en dernier recours, si le paquet `python3-passlib` n’existe pas ou n’est pas dispo dans vos dépôts).  
3. **Vérifier que `passlib` est bien importable** dans **le même** Python qu’Ansible utilise.  
4. **Utiliser `password_hash('sha512')`** uniquement si `passlib` est disponible. Sinon, créer l’utilisateur sans hash (ou échouer).

---

# 3. Spécificité d’AlmaLinux : le paquet `python3-passlib` absent

Sur **AlmaLinux** (ou RHEL-like), le paquet `python3-passlib` n’est parfois pas disponible dans les dépôts de base. Il faut alors :
1. **Activer EPEL** (ou CRB) si besoin.
2. Installer le paquet `python3-passlib` depuis ce dépôt supplémentaire.  

**Exemple** :
```yaml
- name: Activer EPEL sur AlmaLinux (facultatif si déjà disponible)
  yum:
    name: epel-release
    state: present
  when: ansible_os_family == "RedHat"

- name: Installer passlib via DNF sur AlmaLinux
  dnf:
    name: python3-passlib
    state: present
  when: ansible_os_family == "RedHat"
```

> **Note** : Selon la version d’AlmaLinux ou CentOS, vous devrez peut-être activer le dépôt “CRB” (Common Release Base).  

Si **même avec EPEL** vous n’avez pas accès au paquet `python3-passlib`, vous pouvez alors **installer via `pip3`**.

---

# 4. Exemple de Playbook Complet avec Fallback vers pip

Le Playbook ci-dessous :

1. **Détecte la distribution**.  
2. **Installe** `python3` et `pip3` si besoin.  
3. **Active EPEL** (ou CRB) sur AlmaLinux.  
4. **Tente d’installer `python3-passlib`** via `apt` ou `dnf`.  
5. **En cas d’échec**, installe via `pip3`.  
6. **Vérifie** la présence de passlib.  
7. **Crée** les utilisateurs avec un mot de passe hashé si `passlib` est dispo, sinon en clair (pour éviter l’échec complet).

```yaml
---
- name: Gérer l'installation de passlib et créer des utilisateurs
  hosts: all
  become: yes

  vars:
    db_users:
      - username: "alice"
        password: "password1"
        uid: 1001
      - username: "bob"
        password: "password2"
        uid: 1002
      - username: "charlie"
        password: "password3"
        uid: 1003

  tasks:
    ########################################################################
    # 1) DÉTECTER LA DISTRIBUTION                                          #
    ########################################################################
    - name: Gather facts
      setup:

    ########################################################################
    # 2) INSTALLER PYTHON3 ET PIP3                                         #
    ########################################################################
    - name: Installer python3 et pip3 sur Debian/Ubuntu
      apt:
        name:
          - python3
          - python3-pip
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian"

    - name: Installer python3 et pip3 sur RedHat/AlmaLinux
      dnf:
        name:
          - python3
          - python3-pip
        state: present
        update_cache: yes
      when: ansible_os_family == "RedHat"

    ########################################################################
    # 3) ACTIVER EPEL (OU CRB) SUR ALMALINUX SI BESOIN                     #
    ########################################################################
    - name: Activer EPEL sur AlmaLinux
      yum:
        name: epel-release
        state: present
      when: ansible_os_family == "RedHat"

    ########################################################################
    # 4) INSTALLER PASSLIB VIA LE GESTIONNAIRE DE PAQUETS                  #
    ########################################################################
    - name: Installer passlib sur Debian/Ubuntu
      apt:
        name: python3-passlib
        state: present
      when: ansible_os_family == "Debian"
      register: passlib_system_install_deb
      ignore_errors: yes

    - name: Installer passlib sur RedHat/AlmaLinux
      dnf:
        name: python3-passlib
        state: present
      when: ansible_os_family == "RedHat"
      register: passlib_system_install_rh
      ignore_errors: yes

    ########################################################################
    # 5) (FALLBACK) INSTALLER PASSLIB VIA pip3 SI LES PACKAGES SYSTÈME ÉCHOUENT
    ########################################################################
    - name: Installer passlib via pip3 (fallback Debian/Ubuntu)
      pip:
        name: passlib
        state: present
      when: ansible_os_family == "Debian" and passlib_system_install_deb is failed

    - name: Installer passlib via pip3 (fallback RedHat/AlmaLinux)
      pip:
        name: passlib
        state: present
      when: ansible_os_family == "RedHat" and passlib_system_install_rh is failed

    ########################################################################
    # 6) VÉRIFIER LA PRÉSENCE DE PASSLIB                                   #
    ########################################################################
    - name: Tester l'import de passlib
      command: python3 -c "import passlib"
      register: passlib_check
      failed_when: false
      changed_when: false

    - name: Afficher l'état de passlib
      debug:
        msg: "{{ '✅ passlib est installé' if passlib_check.rc == 0 else '❌ passlib est manquant' }}"

    ########################################################################
    # 7) CRÉER LES UTILISATEURS                                           #
    ########################################################################
    - name: Créer les utilisateurs (hashé si passlib dispo)
      user:
        name: "{{ item.username }}"
        uid: "{{ item.uid }}"
        password: "{{ item.password | password_hash('sha512') if passlib_check.rc == 0 else item.password }}"
        shell: "/bin/bash"
        state: present
      loop: "{{ db_users }}"
```

### Points importants

- **`ignore_errors: yes`** lors de l’installation de `python3-passlib` pour pouvoir **ensuite** retomber sur `pip`.  
- Les tâches `register: passlib_system_install_*` vous permettent de savoir si l’installation via paquet système a échoué ou non.  
- Le **module `pip`** d’Ansible installe par défaut dans le Python utilisé par Ansible (puisque vous avez `ansible_python_interpreter=/usr/bin/python3`).  
- Dans la tâche de création d’utilisateurs : on **hash** si `passlib_check.rc == 0` (import réussi) ; sinon, on met le mot de passe en clair.

---

# 5. Que faire si ça ne fonctionne toujours pas ?

1. **Vérifiez les logs** lors des installations (APT ou DNF). Peut-être qu’un **cache** est trop vieux, ou qu’un paquet n’existe pas dans vos dépôts.  
2. **Activez d’autres dépôts** (EPEL, CRB, etc.) si nécessaire sur AlmaLinux.  
3. **Vérifiez la version de Python** qu’utilise Ansible :  
   ```bash
   ansible all -i inventory.ini -m setup -a "filter=ansible_python*"
   ```
   Regardez le chemin retourné par `ansible_python_interpreter`. Assurez-vous que c’est bien `/usr/bin/python3`.  
4. **Faites un test manuel** sur un conteneur :  
   ```bash
   docker exec -it node4 bash
   python3 -c "import passlib; print(passlib.__version__)"
   ```
   Si ça échoue, c’est que `passlib` n’a pas été réellement installé pour ce Python.  
5. **Nettoyez vos conteneurs** ou recréez-les si vous faites des tests en boucle et que certains fichiers ou configurations sont devenues incohérentes.

---

# 6. Conclusion

- La **difficulté principale** sur AlmaLinux (et parfois Debian/Ubuntu minimal) est que `python3-passlib` n’est pas toujours dans les **dépôts par défaut**.  
- La solution la plus simple est souvent de **tomber en fallback** : `pip3 install passlib`.  
- Pour **éviter l’échec complet** de la création d’utilisateurs, il faut soit  
  - (a) S’assurer que `passlib` est vraiment installé, **ou**  
  - (b) Mettre le mot de passe en clair si on ne le trouve pas (moyen, mais ça évite de planter le Playbook).  

Avec le **Playbook incrémental** ci-dessus (et l’activation de `epel-release` si besoin), vous devriez **résoudre** l’erreur *« passlib must be installed »* et enfin pouvoir créer vos utilisateurs avec **hashage** de mot de passe !
