### Comprendre le Comportement des Tags dans Ansible : Ce que Vous Voyez et Pourquoi

Lors de l'exécution d'un playbook avec un tag spécifique ou avec une commande comme `--tags` ou `--skip-tags`, Ansible peut donner l'impression que toutes les tâches ont été exécutées. Cependant, ce n'est pas le cas. Voici un tutoriel pour clarifier ce comportement.

---

### 1. **Comportement de Base d'Ansible avec les Tags**

- **Tags dans Ansible :**
  - Les tags permettent de filtrer les tâches à exécuter.
  - Quand vous utilisez `--tags`, Ansible exécute **uniquement** les tâches associées au(x) tag(s) spécifié(s).
  - Les autres tâches sont **ignorées**, mais cela n'empêche pas Ansible d'analyser tout le playbook.

- **Collecte des Faits (Gathering Facts) :**
  - Avant de commencer à exécuter les tâches, Ansible collecte des informations sur chaque hôte (`Gathering Facts`).
  - Cette étape est indépendante des tags, sauf si explicitement désactivée (`gather_facts: no`).

---

### 2. **Pourquoi Vous Voyez Toutes les Tâches**

Voici pourquoi certaines tâches (sans les tags spécifiés) apparaissent malgré tout dans les logs :

#### **a) Collecte des Faits**
- La collecte des faits est exécutée pour chaque hôte de chaque play, même si aucune tâche avec les tags spécifiés n'est trouvée dans ce play.
- Cela explique pourquoi vous voyez une ligne `TASK [Gathering Facts]` pour tous les hôtes dans chaque play.

#### **b) Analyse de Toutes les Tâches**
- Ansible analyse chaque tâche pour vérifier si elle correspond aux tags spécifiés.
- Si une tâche n'a pas le tag requis, elle est ignorée, mais cette analyse peut donner l'impression que la tâche a été considérée.

#### **c) Réduction de la Confusion :**
- Les tâches sans tags spécifiés sont simplement marquées comme "ignorées" et ne modifient pas l'état de l'hôte.
- Cela se reflète dans le récapitulatif avec des compteurs comme `ok`, `changed`, `skipped`.

---

### 3. **Exemple Pratique**

#### **Playbook Utilisé :**

```yaml
- name: 🚒 Installation et Archivage des Logs
  hosts: all
  become: yes
  tasks:
    - name: 🔧 Installer tmux sur tous les nœuds
      package:
        name: tmux
        state: present
      tags: install_tmux

    - name: 📦 Créer une archive des logs système
      archive:
        path: /var/log
        dest: /tmp/logs.tar.gz
        format: gz
        excludes:
          - '/var/log/wtmp'
          - '/var/log/btmp'
      tags: archive_logs

- name: 🔧 Installation de Git sur les nœuds Ubuntu
  hosts: node1,node5,node6
  become: yes
  tasks:
    - name: 🔄 Mettre à jour le cache APT
      apt:
        update_cache: yes
      tags: update_cache

    - name: 📥 Installer Git
      apt:
        name: git
        state: present
      tags: install_git
```

---

#### **Commande avec `--tags` :**

```bash
ansible-playbook -i inventory.ini playbook.yml --tags install_tmux
```

**Comportement Observé :**
1. **Gathering Facts** est exécuté pour tous les hôtes dans chaque play.
2. Seule la tâche `🔧 Installer tmux sur tous les nœuds` est exécutée.
3. Les autres tâches, même si elles apparaissent dans les logs, sont ignorées.

**Vérification :**
Utilisez une commande ad hoc pour vérifier les installations :

```bash
ansible all -i inventory.ini -m command -a "which tmux"
ansible all -i inventory.ini -m command -a "which git"
```

- `tmux` sera installé.
- `git` ne sera pas installé.

---

#### **Commande avec `--skip-tags` :**

```bash
ansible-playbook -i inventory.ini playbook.yml --skip-tags install_tmux
```

**Comportement Observé :**
1. Les tâches sans le tag `install_tmux` sont exécutées (`archive_logs`, `install_git`, etc.).
2. La tâche `🔧 Installer tmux sur tous les nœuds` est ignorée.

**Vérification :**
- `tmux` ne sera pas installé.
- Les archives et `git` seront correctement configurés.

---

### 4. **Récapitulatif : Ce que Signifient les Logs**

#### Logs Typiques :
```text
PLAY [🚒 Installation et Archivage des Logs] *********************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [node1]
ok: [node2]
ok: [node5]
ok: [node3]
ok: [node4]
ok: [node6]

TASK [🔧 Installer tmux sur tous les nœuds] **********************************************************
ok: [node1]
ok: [node2]
ok: [node5]
ok: [node3]
ok: [node4]
ok: [node6]

PLAY [🔧 Installation de Git sur les nœuds Ubuntu] ***************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [node1]
ok: [node5]
ok: [node6]

PLAY RECAP *******************************************************************************************
node1                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0
node2                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0
node3                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0
node4                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0
node5                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0
node6                      : ok=3    changed=0    unreachable=0    failed=0    skipped=0
```

#### Explications :
1. **`Gathering Facts` :** Toujours exécuté, sauf désactivation.
2. **Tâches exécutées :** Seules celles avec les tags spécifiés sont réellement exécutées.
3. **Récapitulatif :**
   - `ok=3` inclut la collecte des faits et les tâches pertinentes.
   - `skipped` ou `changed=0` indique que d'autres tâches n'ont pas été exécutées.

---

### 5. **Conclusion**

- Ce que vous voyez dans les logs n'indique pas nécessairement que toutes les tâches sont exécutées.
- Ansible analyse tout le playbook, collecte les faits pour chaque hôte, mais n'exécute que les tâches avec les tags spécifiés.
- Vérifiez toujours les installations ou résultats attendus avec des commandes `ad hoc` ou manuelles pour être sûr.




### Question 1


### 6. **Évaluation formative: Comprendre l'Impact des Tags dans Ansible*

#### Objectif :
Cet exercice vous permettra de confirmer que seules les tâches correspondant au tag spécifié sont exécutées dans un playbook Ansible, même si d'autres tâches sont définies.

---

#### Étape 1 : Ajout de Nouvelles Tâches

Modifiez le playbook pour inclure deux nouvelles tâches d'installation de paquets supplémentaires. Donnez-leur des tags différents.

Voici le playbook mis à jour :

```yaml
- name: 🚒 Installation et Archivage des Logs
  hosts: all
  become: yes
  tasks:
    - name: 🔧 Installer tmux sur tous les nœuds
      package:
        name: tmux
        state: present
      tags: install_tmux

    - name: 📦 Créer une archive des logs système
      archive:
        path: /var/log
        dest: /tmp/logs.tar.gz
        format: gz
        excludes:
          - '/var/log/wtmp'
          - '/var/log/btmp'
      tags: archive_logs

    - name: 🔧 Installer un autre package sur tous les nœuds
      package:
        name: tree
        state: present
      tags: install_tree

    - name: 🔧 Installer un autre outil sur tous les nœuds
      package:
        name: htop
        state: present
      tags: install_htop
```

---

#### Étape 2 : Exécution du Playbook avec un Tag Spécifique

Exécutez uniquement les tâches liées au tag `install_tmux` :

```bash
ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags install_tmux
```

---

#### Étape 3 : Vérification des Installations

Après l'exécution, vous devez vérifier si les packages ont été installés ou non. Utilisez les commandes ad hoc suivantes :

- **Vérifiez si `tmux` est installé :**
  ```bash
  ansible all -i inventory.ini -m command -a "which tmux"
  ```

- **Vérifiez si `tree` est installé :**
  ```bash
  ansible all -i inventory.ini -m command -a "which tree"
  ```

- **Vérifiez si `htop` est installé :**
  ```bash
  ansible all -i inventory.ini -m command -a "which htop"
  ```

---

#### Résultats Attendus :

1. **`tmux` doit être installé** sur tous les nœuds, car la tâche associée au tag `install_tmux` a été exécutée.
2. **`tree` et `htop` ne doivent pas être installés**, car leurs tâches associées aux tags `install_tree` et `install_htop` n'ont pas été exécutées.

---

#### Étape 4 : Analyse des Résultats

- Pourquoi `tree` et `htop` ne sont-ils pas installés ?
- Quel serait l'effet si vous exécutiez le playbook avec le tag `install_tree` ou `install_htop` à la place ?

---

#### Étape 5 : Test Supplémentaire (Bonus)

Rejouez le playbook avec un tag différent et vérifiez les résultats. Par exemple :

```bash
ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags install_htop
```

Ensuite, vérifiez à nouveau les installations sur vos nœuds.

---

#### Conclusion

- Cet exercice démontre clairement que les tags permettent un contrôle précis sur les tâches exécutées dans un playbook Ansible. 
- Assurez-vous de bien vérifier les résultats pour confirmer votre compréhension !






---
### Question 2
---

**Pourquoi voit-on toutes les tâches du playbook dans les logs lorsqu'on utilise un tag spécifique avec la commande `ansible-playbook --tags` ? Expliquez ce comportement et donnez un exemple tiré des logs pour montrer que seules les tâches avec le bon tag sont réellement exécutées.**

---

#### Indices à Considérer :
1. **Rôle de la collecte des faits (`Gathering Facts`) :**
   - Pourquoi est-elle toujours exécutée ?
2. **Analyse des tâches dans Ansible :**
   - Pourquoi les tâches sans tags spécifiés apparaissent-elles dans les logs ?
3. **Statut des tâches ignorées (`skipped`) :**
   - Comment les logs marquent-ils une tâche qui n'est pas exécutée ?

#### Objectif de la Question :
Vous devez démontrer que seules les tâches correspondant au tag spécifié modifient l'état des hôtes, et expliquer pourquoi les autres tâches apparaissent dans les logs sans être exécutées.







Oui, **les autres tâches apparaîtront dans les logs**, mais elles ne seront pas exécutées si elles n'ont pas le tag spécifié. Cela peut donner l'impression que toutes les tâches sont prises en compte, mais voici ce qui se passe en réalité :

### Pourquoi les autres tâches apparaissent-elles ?

1. **Analyse des tâches :**
   Ansible **lit** et **analyse** toutes les tâches dans le playbook pour vérifier si elles correspondent au tag spécifié.
   
2. **Affichage dans les logs :**
   - Les tâches sans le tag spécifié sont affichées, mais marquées comme `skipped` (ignorées).
   - Cela peut donner l'impression qu'elles sont "prises en compte", mais elles ne modifient pas l'état des hôtes.

3. **Collecte des faits (`Gathering Facts`) :**
   - Cette étape est toujours exécutée pour tous les hôtes du play, sauf si explicitement désactivée avec `gather_facts: no`.

---

### Exemple de Logs avec d'autres tâches affichées

#### Commande exécutée :
```bash
ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags install_tmux
```

#### Logs générés :
```text
PLAY [🚒 Installation et Archivage des Logs] *********************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [node1]
ok: [node2]
ok: [node3]
ok: [node4]
ok: [node5]
ok: [node6]

TASK [🔧 Installer tmux sur tous les nœuds] **********************************************************
changed: [node1]
changed: [node2]
changed: [node3]
changed: [node4]
changed: [node5]
changed: [node6]

TASK [📦 Créer une archive des logs système] *********************************************************
skipping: [node1]
skipping: [node2]
skipping: [node3]
skipping: [node4]
skipping: [node5]
skipping: [node6]

TASK [🔧 Installer un autre package sur tous les nœuds] **********************************************
skipping: [node1]
skipping: [node2]
skipping: [node3]
skipping: [node4]
skipping: [node5]
skipping: [node6]

PLAY [🔧 Installation de Git sur les nœuds Ubuntu] ***************************************************

TASK [Gathering Facts] *******************************************************************************
ok: [node1]
ok: [node5]
ok: [node6]

TASK [🔄 Mettre à jour le cache APT] *****************************************************************
skipping: [node1]
skipping: [node5]
skipping: [node6]

TASK [📥 Installer Git] ******************************************************************************
skipping: [node1]
skipping: [node5]
skipping: [node6]

PLAY RECAP *******************************************************************************************
node1                      : ok=2    changed=1    unreachable=0    failed=0    skipped=2
node2                      : ok=2    changed=1    unreachable=0    failed=0    skipped=2
node3                      : ok=2    changed=1    unreachable=0    failed=0    skipped=2
node4                      : ok=2    changed=1    unreachable=0    failed=0    skipped=2
node5                      : ok=2    changed=1    unreachable=0    failed=0    skipped=2
node6                      : ok=2    changed=1    unreachable=0    failed=0    skipped=2
```

---

### Analyse des Logs

#### **Ce que vous voyez :**
1. **Tâches affichées :**
   - `Gathering Facts` est exécuté pour tous les hôtes.
   - Toutes les tâches du playbook apparaissent dans les logs.

2. **Tâches exécutées :**
   - La tâche `🔧 Installer tmux sur tous les nœuds` est marquée comme `changed`, car elle correspond au tag `install_tmux`.

3. **Tâches ignorées :**
   - Les autres tâches (`archive_logs`, `install_tree`, etc.) sont marquées comme `skipping`.

4. **Récapitulatif (`PLAY RECAP`) :**
   - Les hôtes ont des tâches marquées comme `ok`, `changed`, et `skipped`.
   - Les tâches marquées comme `skipped` confirment qu'elles ont été ignorées.

---

### **Conclusion pour les Étudiants**

Même si vous voyez toutes les tâches dans les logs, seules celles correspondant au tag spécifié sont exécutées. Pour confirmer cela :

1. Vérifiez les packages installés avec des commandes ad hoc :
   ```bash
   ansible all -i inventory.ini -m command -a "which tmux"
   ansible all -i inventory.ini -m command -a "which tree"
   ansible all -i inventory.ini -m command -a "which htop"
   ```

2. Observez que :
   - `tmux` est installé (tâche exécutée).
   - `tree` et `htop` ne sont pas installés (tâches ignorées).

---

### Bonus : Désactiver l'affichage des tâches ignorées

Pour éviter de voir les tâches ignorées dans les logs, ajoutez cette option dans votre commande :

```bash
ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags install_tmux --skip-tags all
```

Cela affiche uniquement les tâches pertinentes pour le tag spécifié.
