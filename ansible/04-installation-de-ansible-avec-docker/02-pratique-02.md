# Chapitre 2

## 📝 Étape 1 : Créer l'inventaire Ansible

Créez le fichier `inventory.ini` et ajoutez les groupes et les hôtes avec les adresses IP de chaque conteneur. Cet inventaire vous permettra de tester les connexions et d'organiser les conteneurs en groupes pour des rôles spécifiques.

```bash
nano inventory.ini
```

Contenu de `inventory.ini` :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root
node3 ansible_host=172.20.0.4 ansible_user=root
node4 ansible_host=172.20.0.5 ansible_user=root
node5 ansible_host=172.20.0.6 ansible_user=root
node6 ansible_host=172.20.0.7 ansible_user=root

[web]
node1
node5

[database]
node2
node3

[mail]
node4
node6
```

---

## 📝 Étape 2 : Lister les hôtes pour chaque groupe

Ces commandes vous permettront de vérifier quels conteneurs appartiennent à chaque groupe défini dans `inventory.ini`.

1. **Lister les hôtes dans le groupe `web`** :
   ```bash
   ansible web -i inventory.ini --list-hosts
   ```

2. **Lister les hôtes dans le groupe `mail`** :
   ```bash
   ansible mail -i inventory.ini --list-hosts
   ```

3. **Lister tous les hôtes définis dans `inventory.ini`** :
   ```bash
   ansible all -i inventory.ini --list-hosts
   ```

4. **Lister les détails de `node1` uniquement** :
   ```bash
   ansible node1 -i inventory.ini --list-hosts
   ```

5. **Lister les détails de `node2` uniquement** :
   ```bash
   ansible node2 -i inventory.ini --list-hosts
   ```

---

## 📝 Étape 3 : Tester la connectivité et exécuter des commandes

Ensuite, vous pouvez tester la connectivité et exécuter des commandes sur des hôtes spécifiques.

1. **Tester la connectivité avec `ping` pour tous les conteneurs** :
   ```bash
   ansible all -m ping -i inventory.ini
   ```

2. **Exécuter une commande pour afficher la date actuelle sur `node1`** :
   ```bash
   ansible node1 -m command -a "date" -i inventory.ini
   ```

3. **Exécuter une commande pour afficher la date actuelle sur `node2`** :
   ```bash
   ansible node2 -m command -a "date" -i inventory.ini
   ```

4. **Exécuter une commande pour afficher la date sur tous les conteneurs** :
   ```bash
   ansible all -m command -a "date" -i inventory.ini
   ```

---

## 📝 Étape 4 : Tester des actions spécifiques sur des groupes

Vous pouvez aussi exécuter des commandes spécifiques pour certains groupes.

1. **Redémarrer le service Apache sur le groupe `web`** :
   ```bash
   ansible web -m service -a "name=apache2 state=restarted" -i inventory.ini
   ```

2. **Vérifier l'uptime sur le groupe `mail`** :
   ```bash
   ansible mail -m command -a "uptime" -i inventory.ini
   ```

3. **Vérifier l'utilisation du disque sur le groupe `database`** :
   ```bash
   ansible database -m command -a "df -h" -i inventory.ini
   ```

