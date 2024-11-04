
# DRAFT - EN CONSTRUCTION 

- Le contenu de notre fichier `inventory` est le suivant :

```plaintext
master ansible_connection=local
node1 ansible_host=192.168.2.12 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node2 ansible_host=192.168.2.13 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node3 ansible_host=192.168.2.14 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node4 ansible_host=192.168.2.15 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve

[web]
node1
node4

[mail]
node2

[proxy]
node3

[allservers:children]
web
mail
proxy
```

### Explication du Contenu

- **Définition des hôtes** :
  - `master` : Configuré pour une connexion locale.
  - `node1` à `node4` : Configurés pour des connexions SSH avec l'utilisateur `eleve` et le mot de passe `eleve`.
  
- **Groupes d'hôtes** :
  - `[web]` : Contient `node1` et `node4`.
  - `[mail]` : Contient `node2`.
  - `[proxy]` : Contient `node3`.
  
- **Groupe de groupes** :
  - `[allservers:children]` : Inclut les groupes `web`, `mail`, et `proxy`.

Ce fichier `inventory` est bien structuré pour Ansible, permettant d’organiser les hôtes en groupes et sous-groupes pour faciliter la gestion des configurations.




# Partie 02

```plaintext
root@master:~/exercices-ansible# nano inventory
root@master:~/exercices-ansible# ansible ungrouped -i inventory --list-hosts
  hosts (1):
    master
root@master:~/exercices-ansible# ansible web -i inventory --list-hosts
  hosts (2):
    node1
    node4
root@master:~/exercices-ansible# ansible mail -i inventory --list-hosts
  hosts (1):
    node2
```

### Explication des Commandes

1. **Modification de l'inventaire** :
   - `nano inventory` : Ouvre le fichier `inventory` avec l’éditeur de texte `nano`.

2. **Lister les hôtes non groupés** :
   - `ansible ungrouped -i inventory --list-hosts` : Affiche la liste des hôtes qui ne font partie d'aucun groupe spécifique. Ici, le seul hôte non groupé est `master`.

3. **Lister les hôtes du groupe `web`** :
   - `ansible web -i inventory --list-hosts` : Affiche la liste des hôtes dans le groupe `web`, qui inclut `node1` et `node4`.

4. **Lister les hôtes du groupe `mail`** :
   - `ansible mail -i inventory --list-hosts` : Affiche la liste des hôtes dans le groupe `mail`, qui inclut `node2`.

Ces commandes permettent de vérifier quels hôtes appartiennent à chaque groupe défini dans le fichier `inventory`.


-----------


# PARTIE 03

```plaintext
nano inventory
ansible ungrouped -i inventory --list-hosts
ansible web -i inventory --list-hosts
ansible mail -i inventory --list-hosts
ansible allservers -i inventory --list-hosts
ansible ungrouped -i inventory --list-hosts
wc -l /etc/ansible/ansible.cfg
ansible node1 -m command -a "uptime"
ansible node1 -m command -a "uptime" -i inventory
ansible node1 -m command -a "date" -i inventory
``` 

### Explication des Commandes

1. **nano inventory** : Ouvre le fichier `inventory` avec l'éditeur `nano`.

2. **ansible ungrouped -i inventory --list-hosts** : Affiche les hôtes non regroupés dans l'inventaire `inventory`.

3. **ansible web -i inventory --list-hosts** : Liste les hôtes dans le groupe `web` du fichier `inventory`.

4. **ansible mail -i inventory --list-hosts** : Liste les hôtes dans le groupe `mail` du fichier `inventory`.

5. **ansible allservers -i inventory --list-hosts** : Liste les hôtes dans le groupe `allservers` du fichier `inventory`.

6. **ansible ungrouped -i inventory --list-hosts** (répété) : Affiche à nouveau les hôtes non regroupés dans l'inventaire `inventory`.

7. **wc -l /etc/ansible/ansible.cfg** : Compte le nombre de lignes dans le fichier de configuration `ansible.cfg`.

8. **ansible node1 -m command -a "uptime"** : Exécute la commande `uptime` sur `node1` sans spécifier d'inventaire, ce qui génère un avertissement.




# exécuter les commandes time et date sur les machines locales.



9. **ansible node1 -m command -a "uptime" -i inventory** : Exécute la commande `uptime` sur `node1` en utilisant le fichier `inventory`.

Ces commandes permettent de gérer et vérifier l'inventaire, lister les hôtes par groupe et exécuter des commandes sur des nœuds spécifiques.


# Partie 04



```plaintext
ansible allservers -i inventory --list-hosts
ansible ungrouped -i inventory --list-hosts
wc -l /etc/ansible/ansible.cfg
ansible node1 -m command -a "uptime"
ansible node1 -m command -a "uptime" -i inventory
ansible node2 -m command -a "uptime" -i inventory
ansible node2 -m command -a "time" -i inventory
ansible node2 -m command -a "date" -i inventory
ansible node1 -m command -a "date" -i inventory
ansible-doc -l | wc -l
```

### Explication des Commandes

1. **Lister les hôtes du groupe `allservers`** :
   ```bash
   ansible allservers -i inventory --list-hosts
   ```

2. **Lister les hôtes non regroupés** :
   ```bash
   ansible ungrouped -i inventory --list-hosts
   ```

3. **Compter les lignes dans le fichier `ansible.cfg`** :
   ```bash
   wc -l /etc/ansible/ansible.cfg
   ```

4. **Exécuter la commande `uptime` sur `node1` sans spécifier d'inventaire (génère un avertissement)** :
   ```bash
   ansible node1 -m command -a "uptime"
   ```

5. **Exécuter la commande `uptime` sur `node1` en utilisant l'inventaire** :
   ```bash
   ansible node1 -m command -a "uptime" -i inventory
   ```

6. **Exécuter la commande `uptime` sur `node2` en utilisant l'inventaire** :
   ```bash
   ansible node2 -m command -a "uptime" -i inventory
   ```

7. **Essayer d'exécuter la commande `time` sur `node2` (échec car `time` n'est pas une commande valide dans ce contexte)** :
   ```bash
   ansible node2 -m command -a "time" -i inventory
   ```

8. **Exécuter la commande `date` sur `node2` en utilisant l'inventaire** :
   ```bash
   ansible node2 -m command -a "date" -i inventory
   ```

9. **Exécuter la commande `date` sur `node1` en utilisant l'inventaire** :
   ```bash
   ansible node1 -m command -a "date" -i inventory
   ```

10. **Lister tous les modules disponibles avec `ansible-doc` et compter les lignes de sortie** :
    ```bash
    ansible-doc -l | wc -l
    ```

Ces commandes montrent comment lister les hôtes dans les groupes, exécuter des commandes à distance sur des nœuds spécifiques et utiliser des outils pour consulter la configuration et les modules disponibles dans Ansible.

