### **Exercices Pratiques : Travailler avec les Variables, Facts et Registers**

------------------------
# Les variables
------------------------

#### **Exercice 1 : Variables Simples**
1. Créez un playbook appelé `exercise1-variables.yml`.
2. Définissez les variables suivantes :
   - `project_name`: Un nom pour un projet fictif.
   - `version`: Un numéro de version (ex. `1.0.0`).
   - `is_active`: Une variable booléenne indiquant si le projet est actif ou non.
3. Affichez les valeurs des variables en utilisant le module `debug`.

**Résultat attendu :**
```
Le projet {{ project_name }} version {{ version }} est actif : {{ is_active }}
```

---

#### **Exercice 2 : Listes et Dictionnaires**
1. Créez un playbook appelé `exercise2-lists-dictionaries.yml`.
2. Définissez les variables suivantes :
   - Une liste `services` contenant les noms de trois services fictifs (ex. `nginx`, `mysql`, `redis`).
   - Un dictionnaire `configs` contenant :
     - `app_name`: Nom de l'application.
     - `db_user`: Nom d'utilisateur de la base de données.
     - `db_password`: Mot de passe (utilisez une valeur sécurisée).
3. Créez des tâches pour :
   - Afficher le deuxième service de la liste.
   - Afficher le nom d'utilisateur de la base de données.

---

#### **Exercice 3 : Variables dans des Fichiers Externes**
1. Créez un fichier de variables externe nommé `exercise3-vars.yml` contenant les informations suivantes :
   - Une liste `ports` (ex. `22`, `80`, `443`).
   - Un dictionnaire `server_info` avec les clés `hostname` et `ip_address`.
2. Créez un playbook `exercise3-playbook.yml` qui inclut ce fichier et affiche :
   - Le premier port de la liste.
   - Le nom d’hôte et l’adresse IP définis dans `server_info`.

------------------------
# Les Registers
------------------------

#### **Exercice 4 : Utilisation des Registers**
1. Créez un playbook nommé `exercise4-registers.yml`.
2. Ajoutez une tâche qui exécute la commande suivante sur les hôtes cibles :
   - `uname -r` (pour obtenir la version du noyau Linux).
3. Stockez la sortie dans un register nommé `kernel_version`.
4. Créez une autre tâche pour afficher le message suivant :
   - "La version du noyau est : {{ kernel_version.stdout }}".

---

#### **Exercice 5 : Conditions Basées sur les Registers**
1. Créez un playbook `exercise5-conditions.yml`.
2. Ajoutez une tâche qui vérifie si le service `nginx` est installé :
   - Utilisez la commande `dpkg -l nginx` sur les systèmes Debian ou `yum list installed nginx` sur les systèmes RedHat.
   - Enregistrez la sortie dans un register nommé `nginx_status`.
3. Créez une condition pour installer `nginx` uniquement si la commande précédente retourne un code différent de 0 (`rc != 0`).
4. Affichez un message indiquant si `nginx` a été installé ou s'il était déjà présent.



------------------------
# Les Facts
------------------------

#### **Exercice 6 : Utiliser les Facts pour Personnaliser les Tâches**
1. Créez un playbook `exercise6-facts.yml`.
2. Ajoutez une tâche qui installe un paquet spécifique en fonction du système d'exploitation détecté :
   - Pour les systèmes basés sur Debian, installez `curl` via `apt`.
   - Pour les systèmes basés sur RedHat, installez `curl` via `yum`.
3. Affichez un message indiquant quel paquet a été installé et sur quel système d'exploitation.
