### Chapitre 5 : Les Boucles dans Ansible

Dans ce chapitre, vous apprendrez à utiliser les **boucles** dans Ansible pour exécuter une tâche plusieurs fois sans avoir à la réécrire pour chaque itération. C'est une compétence essentielle pour gérer efficacement vos systèmes, comme créer plusieurs utilisateurs, configurer des services ou gérer des fichiers multiples sur vos hôtes.

---

### **1. Introduction aux boucles dans Ansible**
Les boucles permettent de répéter une tâche pour une liste d'éléments ou une plage de valeurs. Ansible utilise le mot-clé `loop` pour itérer sur une liste ou une plage. Pendant chaque itération, la variable spéciale `item` représente l'élément actuel de la boucle.

---

### **2. Boucler sur des listes**
Prenons un exemple simple : afficher une liste de nombres premiers.

#### Exemple : Afficher une liste
```yaml
---
- name: Afficher des nombres premiers
  hosts: node1
  vars:
    prime: [2, 3, 5, 7, 11]
  tasks:
    - name: Afficher les nombres premiers
      debug:
        msg: "{{ item }}"
      loop: "{{ prime }}"
```

#### Explication :
1. La variable `prime` contient une liste de nombres.
2. La tâche utilise `loop: "{{ prime }}"` pour parcourir chaque élément.
3. À chaque itération, la variable `item` prend la valeur de l'élément actuel.

#### Exécution :
```bash
ansible-playbook print-list.yml
```

**Résultat attendu :**
```yaml
TASK [Afficher les nombres premiers] ****
ok: [node1] => (item=2) => { "msg": 2 }
ok: [node1] => (item=3) => { "msg": 3 }
...
```

---

### **3. Cas pratique : Ajouter plusieurs utilisateurs**

#### Playbook : Ajouter des utilisateurs
Voici un playbook qui crée plusieurs utilisateurs sur des serveurs de base de données :

```yaml
---
- name: Ajouter plusieurs utilisateurs
  hosts: dbservers
  vars:
    dbusers:
      - username: brad
        pass: pass1
      - username: david
        pass: pass2
      - username: jason
        pass: pass3
  tasks:
    - name: Ajouter les utilisateurs
      user:
        name: "{{ item.username }}"
        password: "{{ item.pass | password_hash('sha512') }}"
      loop: "{{ dbusers }}"
```

#### Explication :
1. La liste `dbusers` contient des dictionnaires avec les noms d'utilisateur et mots de passe.
2. `item.username` et `item.pass` accèdent aux clés des dictionnaires.
3. Le filtre `password_hash('sha512')` hache les mots de passe avant de les appliquer.

#### Exécution :
```bash
ansible-playbook add-users.yml
```

**Résultat attendu :**
Les utilisateurs sont ajoutés et visibles dans `/etc/passwd`.

---

### **4. Boucler sur des dictionnaires**

Les boucles fonctionnent uniquement sur des listes. Pour itérer sur un dictionnaire, il faut le convertir en liste avec le filtre `dict2items`.

#### Exemple : Parcourir un dictionnaire
```yaml
---
- name: Afficher un dictionnaire
  hosts: node1
  vars:
    employee:
      name: "Elliot Alderson"
      title: "Penetration Tester"
      company: "Linux Handbook"
  tasks:
    - name: Afficher les clés et valeurs
      debug:
        msg: "Clé : {{ item.key }}, Valeur : {{ item.value }}"
      loop: "{{ employee | dict2items }}"
```

#### Résultat attendu :
```yaml
TASK [Afficher les clés et valeurs] ****
ok: [node1] => Clé : name, Valeur : Elliot Alderson
ok: [node1] => Clé : title, Valeur : Penetration Tester
...
```

---

### **5. Boucler sur une plage de nombres**

Utilisez `range()` pour générer une liste de nombres.

#### Exemple : Afficher une plage
```yaml
---
- name: Boucler sur une plage de nombres
  hosts: node1
  tasks:
    - name: Afficher les nombres de 0 à 9
      debug:
        msg: "{{ item }}"
      loop: "{{ range(10) | list }}"
```

#### Personnalisation :
- Pour une plage de 5 à 14 :
  ```yaml
  loop: "{{ range(5, 15) | list }}"
  ```
- Pour des nombres pairs entre 0 et 256 :
  ```yaml
  loop: "{{ range(0, 256, 2) | list }}"
  ```

---

### **6. Boucler sur des inventaires**

Vous pouvez boucler sur les hôtes définis dans votre inventaire grâce à `groups`.

#### Exemple : Pinger tous les hôtes
```yaml
---
- name: Boucler sur l'inventaire
  hosts: node1
  tasks:
    - name: Ping tous les hôtes
      command: ping -c 1 "{{ item }}"
      loop: "{{ groups['all'] }}"
```

#### Explication :
- `groups['all']` contient tous les hôtes de l'inventaire.
- Vous pouvez spécifier un groupe : `groups['webservers']`.

---

### **7. Ajouter une pause dans une boucle**

Utilisez `loop_control` pour ajouter un délai entre les itérations.

#### Exemple : Compte à rebours
```yaml
---
- name: Compte à rebours
  hosts: node1
  tasks:
    - name: Afficher le compte à rebours
      debug:
        msg: "{{ 10 - item }} secondes restantes..."
      loop: "{{ range(10) | list }}"
      loop_control:
        pause: 1
    - name: Afficher "Bon anniversaire !"
      debug:
        msg: "Bon anniversaire !"
```

#### Résultat attendu :
Chaque seconde, un message est affiché, suivi de "Bon anniversaire !" à la fin.

---

### **8. Exercice : Lab**

#### Objectif :
Créer un playbook `lab5.yml` qui :
1. Exécute uniquement sur `localhost`.
2. Affiche tous les nombres pairs entre 20 et 40.

#### Solution :
```yaml
---
- name: Lab Chapitre 5
  hosts: localhost
  tasks:
    - name: Afficher les nombres pairs entre 20 et 40
      debug:
        msg: "{{ item }}"
      loop: "{{ range(20, 41, 2) | list }}"
```

---

### **Résumé**
Dans ce chapitre, vous avez appris :
1. Comment utiliser des boucles pour simplifier vos tâches répétitives.
2. Boucler sur des listes, dictionnaires, plages de nombres et inventaires.
3. Ajouter des pauses dans les boucles.

Les boucles rendent vos playbooks plus dynamiques et réutilisables. Dans le prochain chapitre, nous apprendrons à intégrer la prise de décisions avec des conditions dans Ansible.
