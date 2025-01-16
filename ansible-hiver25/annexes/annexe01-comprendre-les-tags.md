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
