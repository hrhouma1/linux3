# Objectif :

- illustrer l’utilisation des boucles **`with_*`** dans Ansible, en se basant sur l’infrastructure Docker multi-distributions que vous avez créée auparavant.  

Vous y trouverez plusieurs tâches qui utilisent différentes variantes de boucles pour :  
1. Installer plusieurs paquets.  
2. Configurer des paramètres dans un fichier à partir d’un dictionnaire.  
3. Créer une série d’utilisateurs avec une séquence.  
4. Copier un ensemble de fichiers sur les conteneurs.  
5. Créer des répertoires et fichiers imbriqués avec `with_nested`.  

---

## 1. Préparer l’Inventaire Ansible

Assurez-vous d’avoir un inventaire `inventory.ini` similaire à celui-ci (ou adapté à votre environnement) :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node2 ansible_host=172.20.0.3 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node3 ansible_host=172.20.0.4 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node4 ansible_host=172.20.0.5 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node5 ansible_host=172.20.0.6 ansible_user=root ansible_python_interpreter=/usr/bin/python3
node6 ansible_host=172.20.0.7 ansible_user=root ansible_python_interpreter=/usr/bin/python3
```

*(Vous pouvez éventuellement ajouter ou enlever des hôtes, ou les regrouper selon vos besoins.)*

---

## 2. Créer un Playbook : `pratique-with-loops.yml`

Créez un nouveau fichier `pratique-with-loops.yml` dans le répertoire où se trouve votre inventaire :

```bash
nano pratique-with-loops.yml
```

Copiez le contenu suivant :

```yaml
---
- name: Pratique Ansible avec Boucles with_*
  hosts: node_containers
  become: yes
  tasks:

    ###################################################################
    # 1) with_items : Installer plusieurs paquets sur chaque conteneur
    ###################################################################
    - name: Installer plusieurs paquets
      package:
        name: "{{ item }}"
        state: present
      with_items:
        - vim
        - curl
        - git

    #############################################################################
    # 2) with_dict : Configurer des paramètres dans un fichier (ex : /etc/ssh/sshd_config)
    #############################################################################
    - name: Configurer des paramètres SSH
      lineinfile:
        path: /etc/ssh/sshd_config
        line: "{{ item.key }} {{ item.value }}"
        create: yes
      with_dict:
        MaxSessions: 10
        UseDNS: no
        ClientAliveInterval: 300

    ###############################################################
    # 3) with_sequence : Créer plusieurs utilisateurs "userX"
    ###############################################################
    - name: Créer plusieurs utilisateurs
      user:
        name: "loopuser{{ item }}"
        uid: "100{{ item }}"
        shell: /bin/bash
      with_sequence: start=1 end=3
      # Cela va créer: loopuser1 (uid=1001), loopuser2 (uid=1002), loopuser3 (uid=1003)

    ##################################################################
    # 4) with_fileglob : Copier tous les fichiers .txt d'un dossier local
    ##################################################################
    - name: Copier tous les fichiers .txt depuis un répertoire local
      copy:
        src: "{{ item }}"
        dest: "/tmp/{{ item | basename }}"
      with_fileglob:
        - "./files/*.txt"
      # Assurez-vous de créer un dossier 'files/' à côté de ce playbook,
      # contenant par exemple file1.txt, file2.txt, etc.

    #############################################################################
    # 5) with_nested : Créer des répertoires et fichiers imbriqués pour chaque user
    #############################################################################
    - name: Créer des répertoires et fichiers imbriqués
      file:
        path: "/home/{{ item.0 }}/{{ item.1 }}"
        state: touch
      with_nested:
        - ["loopuser1", "loopuser2", "loopuser3"]
        - ["notes.txt", "README.md"]
      # Exemple de chemins créés :
      # /home/loopuser1/notes.txt
      # /home/loopuser1/README.md
      # /home/loopuser2/notes.txt
      # /home/loopuser2/README.md
      # /home/loopuser3/notes.txt
      # /home/loopuser3/README.md
```

### Quelques Points à Vérifier ou Adapter
1. **Module `package`:**  
   - Il est générique et s’adapte à la plupart des distributions (Ubuntu/Debian => `apt`, CentOS/AlmaLinux => `yum`).  
   - Si vous utilisez une version d’Ansible où `package` n’est pas bien géré, utilisez `apt` ou `yum` séparément, avec un `when: ansible_facts['os_family'] == 'Debian'` ou `RedHat`.

2. **Chemins et Droits d’Accès :**  
   - Les fichiers de configuration (ex. `/etc/ssh/sshd_config`) nécessitent parfois un redémarrage du service. Vous pouvez ajouter une tâche pour redémarrer le service SSH si nécessaire.  

3. **Répertoire Local `files/*.txt`:**  
   - Créez un répertoire nommé `files` à côté de votre playbook, et placez-y quelques fichiers `.txt` pour tester la copie.  

---

## 3. Lancer le Playbook

Exécutez ensuite la commande suivante :

```bash
ansible-playbook -i inventory.ini pratique-with-loops.yml
```

1. **Installation de paquets**  
   - Les paquets `vim`, `curl` et `git` devraient être installés sur chaque conteneur.

2. **Configuration des paramètres SSH**  
   - Le fichier `/etc/ssh/sshd_config` sera modifié pour ajouter ou mettre à jour `MaxSessions 10`, `UseDNS no` et `ClientAliveInterval 300`.  
   - En production, vous lanceriez peut-être un `service ssh restart` ou `systemctl restart sshd` (selon la distribution) juste après, si nécessaire.

3. **Création d’utilisateurs**  
   - Trois utilisateurs `loopuser1`, `loopuser2`, et `loopuser3` seront créés, avec les UID correspondants (1001, 1002, 1003).

4. **Copie de fichiers `.txt`**  
   - Tous les fichiers `*.txt` du répertoire `files/` local seront copiés vers `/tmp/` sur chaque conteneur.

5. **Création de répertoires et fichiers imbriqués**  
   - Pour chaque utilisateur (`loopuser1`, `loopuser2`, `loopuser3`), les fichiers `notes.txt` et `README.md` seront créés dans `/home/<user>/`.

---

## 4. Vérifications

Après l’exécution du playbook, vous pouvez vérifier :

1. **Paquets installés** :  
   ```bash
   ansible all -i inventory.ini -m command -a "which vim && which git && which curl"
   ```
2. **Fichier `/etc/ssh/sshd_config`** :  
   ```bash
   ansible all -i inventory.ini -m command -a "grep 'MaxSessions' /etc/ssh/sshd_config"
   ```
3. **Utilisateurs créés** :  
   ```bash
   ansible all -i inventory.ini -m command -a "id loopuser1"
   ansible all -i inventory.ini -m command -a "id loopuser2"
   ansible all -i inventory.ini -m command -a "id loopuser3"
   ```
4. **Fichiers copiés** :  
   ```bash
   ansible all -i inventory.ini -m command -a "ls -l /tmp"
   ```
5. **Répertoires et fichiers imbriqués** :  
   ```bash
   ansible all -i inventory.ini -m command -a "ls -l /home/loopuser1 /home/loopuser2 /home/loopuser3"
   ```

Vous devriez voir la présence de `notes.txt` et `README.md` dans chacun des répertoires de ces utilisateurs.

---

# Conclusion

Cette **pratique** vous montre comment exploiter les boucles **`with_*`** dans Ansible pour automatiser une variété de tâches de manière concise et claire :  

- **`with_items`** : idéal pour installer rapidement plusieurs paquets ou exécuter la même action sur une liste d’éléments.  
- **`with_dict`** : pratique pour parcourir un dictionnaire et configurer des paramètres clé/valeur, comme dans un fichier de configuration.  
- **`with_sequence`** : parfait pour générer des listes de nombres ou de chaînes régulières (ex. création de plusieurs utilisateurs).  
- **`with_fileglob`** ou **`with_file`** : pour itérer sur des fichiers locaux et les copier à distance.  
- **`with_nested`** : utile lorsque vous souhaitez créer des combinaisons multiples de répertoires, fichiers, ou paramètres (ex. pour générer un grand nombre de configurations).

En adaptant cette approche à votre propre infrastructure, vous gagnerez en **clarté**, en **efficacité** et en **maintenabilité** pour vos playbooks Ansible. Bon apprentissage !

------------------------
------------------------
------------------------
# Annexe 1 - Vérifications
------------------------

> Liste de **commandes AdHoc** à exécuter après chaque tâche pour vérifier que tout fonctionne correctement

### **1. Vérifier l'installation des paquets (`with_items`)**
```bash
ansible all -i inventory.ini -m shell -a "which vim && which git && which curl"
```
✅ **Attendu :** Les chemins vers `vim`, `git`, et `curl` s'affichent s'ils sont bien installés.

---

### **2. Vérifier la configuration des paramètres SSH (`with_dict`)**
```bash
ansible all -i inventory.ini -m command -a "grep -E 'MaxSessions|UseDNS|ClientAliveInterval' /etc/ssh/sshd_config"
```
✅ **Attendu :** Les lignes suivantes doivent apparaître :
```
MaxSessions 10
UseDNS no
ClientAliveInterval 300
```
⚠ **Optionnel** : Redémarrer SSH si nécessaire
```bash
ansible all -i inventory.ini -m service -a "name=sshd state=restarted"
```

---

### **3. Vérifier les utilisateurs créés (`with_sequence`)**
```bash
ansible all -i inventory.ini -m shell -a "id loopuser1; id loopuser2; id loopuser3"
```
✅ **Attendu :** Chaque utilisateur a un `uid` commençant par `100X`.

---

### **4. Vérifier que les fichiers `.txt` ont été copiés (`with_fileglob`)**
```bash
ansible all -i inventory.ini -m shell -a "ls -l /tmp/"
```
✅ **Attendu :** Tous les fichiers `.txt` doivent être listés dans `/tmp/`.

---

### **5. Vérifier les répertoires et fichiers imbriqués (`with_nested`)**
```bash
ansible all -i inventory.ini -m shell -a "ls -l /home/loopuser1 /home/loopuser2 /home/loopuser3"
```
✅ **Attendu :**  
- `/home/loopuser1/notes.txt`
- `/home/loopuser1/README.md`
- `/home/loopuser2/notes.txt`
- `/home/loopuser2/README.md`
- `/home/loopuser3/notes.txt`
- `/home/loopuser3/README.md`

---

### **Commandes complémentaires de débogage**
1. **Vérifier la connectivité avec tous les hôtes** :
   ```bash
   ansible all -i inventory.ini -m ping
   ```
2. **Afficher l'utilisation du disque** :
   ```bash
   ansible all -i inventory.ini -m shell -a "df -h"
   ```
3. **Vérifier le temps de fonctionnement des machines** :
   ```bash
   ansible all -i inventory.ini -m shell -a "uptime"
   ```


------------------------
------------------------
------------------------
# Annexe2 - 
------------------------

- Pour entrer directement dans les **conteneurs Docker** et vérifier manuellement, voici les commandes essentielles.



### **1. Lister les conteneurs en cours d'exécution**
```bash
docker ps
```
✅ **Attendu** : Affiche la liste des conteneurs avec leur ID, nom et statut.

---

### **2. Ouvrir un shell dans un conteneur spécifique**
- Remplace `<container_id>` par l'ID ou le nom du conteneur concerné.
```bash
docker exec -it <container_id> /bin/bash
```
ou si le conteneur utilise `sh` au lieu de `bash` :
```bash
docker exec -it <container_id> /bin/sh
```
✅ **Attendu** : Ouvre un terminal interactif à l’intérieur du conteneur.

---

### **3. Vérifier manuellement les installations et configurations**
Une fois à l'intérieur du conteneur, exécute les commandes suivantes pour vérifier :

#### **✔️ Paquets installés**
```bash
which vim git curl
```
✅ **Attendu** : Affiche les chemins des binaires (`/usr/bin/vim`, etc.).

#### **✔️ Paramètres SSH**
```bash
cat /etc/ssh/sshd_config | grep -E 'MaxSessions|UseDNS|ClientAliveInterval'
```
✅ **Attendu** : Affiche les lignes :
```
MaxSessions 10
UseDNS no
ClientAliveInterval 300
```

#### **✔️ Utilisateurs créés**
```bash
id loopuser1 && id loopuser2 && id loopuser3
```
✅ **Attendu** : Affiche les UID et GID des utilisateurs.

#### **✔️ Fichiers copiés**
```bash
ls -l /tmp/
```
✅ **Attendu** : Les fichiers `.txt` copiés depuis `files/*.txt` doivent être visibles.

#### **✔️ Répertoires et fichiers imbriqués**
```bash
ls -l /home/loopuser1 /home/loopuser2 /home/loopuser3
```
✅ **Attendu** : Chaque utilisateur doit avoir `notes.txt` et `README.md`.

---

### **4. Quitter le conteneur**
Pour sortir du conteneur après la vérification :
```bash
exit
```

---

### **5. Vérifier les logs d’un conteneur (en cas de problème)**
```bash
docker logs <container_id>
```
✅ **Utile pour** voir les erreurs liées au service SSH, aux installations ou aux scripts exécutés.

---

**💡 Astuce :** Si tu veux **accéder directement à un conteneur sans chercher son ID**, utilise :
```bash
docker exec -it $(docker ps -q | head -n 1) /bin/bash
```
Cela ouvrira un terminal dans le **premier conteneur actif** trouvé.

---

### **Résumé**
| Objectif | Commande |
|----------|---------|
| Lister les conteneurs | `docker ps` |
| Entrer dans un conteneur | `docker exec -it <container_id> /bin/bash` |
| Vérifier paquets installés | `which vim git curl` |
| Vérifier SSH config | `cat /etc/ssh/sshd_config | grep -E 'MaxSessions|UseDNS|ClientAliveInterval'` |
| Vérifier utilisateurs | `id loopuser1 && id loopuser2 && id loopuser3` |
| Vérifier fichiers `/tmp/` | `ls -l /tmp/` |
| Vérifier répertoires `/home/` | `ls -l /home/loopuser1 /home/loopuser2 /home/loopuser3` |
| Vérifier logs conteneur | `docker logs <container_id>` |
| Sortir du conteneur | `exit` |

