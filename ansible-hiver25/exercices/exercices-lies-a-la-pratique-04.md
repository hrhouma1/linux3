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





------------------------

### **Exercice 7 : Explorer et Filtrer les Facts Ansible**

Vous devrez trouver par vous-mêmes les filtres appropriés pour répondre aux questions ci-dessous. La commande de base est :

```bash
ansible all -m ansible.builtin.setup -a "filter=<votre_filtre>" -i inventory.ini
```

*Je vous présente d'abord 10 exemples de tâches :*


#### **Exemple 1 à tester: Découvrir les Détails des Distributions**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_distribution*" -i inventory.ini
```

**Résultat attendu :**
- Pour les nœuds Ubuntu :
  ```yaml
  ansible_distribution: Ubuntu
  ansible_distribution_version: "20.04"
  ansible_distribution_release: focal
  ```
- Pour les nœuds Debian :
  ```yaml
  ansible_distribution: Debian
  ansible_distribution_version: "11"
  ansible_distribution_release: bullseye
  ```
- Pour les nœuds AlmaLinux :
  ```yaml
  ansible_distribution: AlmaLinux
  ansible_distribution_version: "8.8"
  ansible_distribution_release: "Ootpa"
  ```

---

#### **Exemple 2 à tester: Vérifier les Interfaces Réseau**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_interfaces" -i inventory.ini
```

**Résultat attendu pour chaque nœud :**
```yaml
ansible_interfaces:
  - lo
  - eth0
```

---

#### **Exemple 3 à tester: Récupérer l’Adresse IP de l’Interface `eth0`**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_eth0" -i inventory.ini
```

**Résultat attendu pour chaque nœud :**
```yaml
ansible_eth0:
  active: true
  macaddress: "02:42:ac:14:00:02"  # Exemple
  ipv4:
    address: 172.20.0.2  # Adapté au nœud
    netmask: 255.255.255.0
    network: 172.20.0.0
```

---

#### **Exemple 4 à tester: Explorer les Adresses IPv4 Disponibles**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_all_ipv4_addresses" -i inventory.ini
```

**Résultat attendu pour chaque nœud :**
```yaml
ansible_all_ipv4_addresses:
  - 172.20.0.2  # Adresse assignée au conteneur
```

---

#### **Exemple 5 à tester: Identifier les Processus d’Uptime**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_uptime_seconds" -i inventory.ini
```

**Résultat attendu :**
```yaml
ansible_uptime_seconds: 3600  # Exemple : 1 heure depuis le démarrage
```

---

#### **Exemple 6 à tester: Explorer les Fichiers Systèmes Montés**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_mounts" -i inventory.ini
```

**Résultat attendu :**
```yaml
ansible_mounts:
  - device: "/dev/root"
    fstype: "ext4"
    mount: "/"
    options:
      - rw
      - relatime
  - device: "tmpfs"
    fstype: "tmpfs"
    mount: "/dev"
    options:
      - rw
      - nosuid
```

---

#### **Exemple 7 à tester: Différencier les Systèmes par Architecture**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_architecture" -i inventory.ini
```

**Résultat attendu :**
- Pour tous les nœuds :
  ```yaml
  ansible_architecture: x86_64
  ```

---

#### **Exemple 8 à tester: Extraire la Mémoire Totale et Disponible**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_memtotal_mb,ansible_memfree_mb" -i inventory.ini
```

**Résultat attendu :**
```yaml
ansible_memtotal_mb: 2048
ansible_memfree_mb: 1024
```

---

#### **Exemple 9 à tester: Vérifier le Nombre de CPU**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_processor_count" -i inventory.ini
```

**Résultat attendu :**
```yaml
ansible_processor_count: 2
```

---

#### **Exemple 10 à tester: Vérifier les Propriétés des Volumes de Stockage**

**Commande à exécuter :**
```bash
ansible all -m ansible.builtin.setup -a "filter=ansible_devices" -i inventory.ini
```

**Résultat attendu :**
```yaml
ansible_devices:
  vda:
    size: "10.74 GB"
    rotational: false
    scheduler_mode: "cfq"
```






---

| **Numéro** | **Question**                                                                                                     | **Filtre attendu (à compléter par les étudiants)**  | **Exemple de Résultat attendu**                                                                                                                                                          |
|------------|------------------------------------------------------------------------------------------------------------------|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| 1          | Quel est le nom de la distribution et sa version ?                                                              |                                                    | `ansible_distribution: Ubuntu` <br> `ansible_distribution_version: "20.04"`                                                                        |
| 2          | Quelle est l'adresse IPv4 par défaut de chaque nœud ?                                                           |                                                    | `ansible_default_ipv4.address: 172.20.0.2`                                                                                                         |
| 3          | Listez toutes les adresses IPv4 des interfaces réseau.                                                          |                                                    | `ansible_all_ipv4_addresses: [172.20.0.2]`                                                                                                         |
| 4          | Quelles sont les interfaces réseau disponibles ?                                                                |                                                    | `ansible_interfaces: [lo, eth0]`                                                                                                                   |
| 5          | Quelle est la quantité totale de mémoire (en Mo) sur chaque nœud ?                                              |                                                    | `ansible_memtotal_mb: 2048`                                                                                                                         |
| 6          | Affichez l'uptime (en secondes) de chaque nœud.                                                                 |                                                    | `ansible_uptime_seconds: 3600`                                                                                                                     |
| 7          | Quelle est l'architecture du processeur de chaque nœud ?                                                        |                                                    | `ansible_architecture: x86_64`                                                                                                                     |
| 8          | Listez les disques ou volumes de stockage montés sur chaque nœud.                                               |                                                    | `ansible_mounts: [{"device": "/dev/root", "mount": "/", "fstype": "ext4", ...}]`                                                                    |
| 9          | Quelle est l'adresse MAC de l'interface réseau principale (ex. eth0) ?                                          |                                                    | `ansible_eth0.macaddress: 02:42:ac:14:00:02`                                                                                                       |
| 10         | Trouvez le type et la version exacte du noyau Linux sur chaque nœud.                                             |                                                    | `ansible_kernel: 5.4.0-135-generic`                                                                                                                |
| 11         | Affichez le nombre de processeurs disponibles.                                                                  |                                                    | `ansible_processor_count: 2`                                                                                                                       |
| 12         | Quelles sont les informations sur l’hôte, comme le nom et le nom de domaine complet (FQDN) ?                    |                                                    | `ansible_hostname: node1` <br> `ansible_fqdn: node1.local`                                                                                         |
| 13         | Quelle est la version de Python utilisée par Ansible sur chaque nœud ?                                          |                                                    | `ansible_python.version: {"major": 3, "minor": 8, "patch": 10, ...}`                                                                               |
| 14         | Quelles sont les variables d'environnement configurées sur chaque nœud ?                                        |                                                    | `ansible_env: {"PATH": "/usr/local/sbin:/usr/local/bin:...", "LANG": "en_US.UTF-8"}`                                                                                                     |
| 15         | Quelle est la version de bash installée sur chaque nœud ?                                                       |                                                    | `ansible_bash: 5.0.17(1)-release`                                                                                                                                                        |
| 16         | Trouvez toutes les partitions actives et leur point de montage.                                                 |                                                    | `ansible_mounts: [{"device": "/dev/vda1", "mount": "/", "fstype": "ext4", ...}]`                                                                                                        |
| 17         | Quelle est l’adresse IPv6 associée à chaque nœud ?                                                              |                                                    | `ansible_all_ipv6_addresses: ["fe80::1", "2001:db8::1"]`                                                                                                                               |
| 18         | Quelle est la version des modules Python utilisés ?                                                             |                                                    | `ansible_python_modules: {..., "json": "3.8.10", "os": "3.8.10", ...}`                                                                                                                  |
| 19         | Trouvez le nom des disques physiques attachés à chaque nœud.                                                    |                                                    | `ansible_devices: {"vda": {"size": "10.74 GB", ...}}`                                                                                                                                   |
| 20         | Quelles sont les informations sur les processeurs (marque, fréquence, etc.) ?                                   |                                                    | `ansible_processor: ["Intel(R) Core(TM) i7-8550U CPU @ 1.80GHz", "Intel(R) Core(TM) i7-8550U CPU @ 1.80GHz"]`                                                                            |
| 21         | Trouvez la langue par défaut configurée sur chaque nœud.                                                        |                                                    | `ansible_env.LANG: en_US.UTF-8`                                                                                                                                                        |
| 22         | Quelles sont les informations sur le bootloader et les options de démarrage ?                                   |                                                    | `ansible_cmdline: {"BOOT_IMAGE": "/vmlinuz-5.4.0", "root": "/dev/vda1", ...}`                                                                                                           |
| 23         | Quelle est l'adresse MAC de toutes les interfaces réseau ?                                                      |                                                    | `ansible_all_ipv4_addresses: ["02:42:ac:14:00:02", "02:42:ac:14:00:03"]`                                                                                                                |
| 24         | Affichez le modèle du matériel utilisé (si disponible).                                                         |                                                    | `ansible_product_name: "Virtual Machine"`                                                                                                                                               |
| 25         | Trouvez les informations sur la version et le type de système de fichiers montés sur `/`.                       |                                                    | `ansible_mounts: [{"device": "/dev/root", "fstype": "ext4", "mount": "/", ...}]`                                                                                                       |
| 26         | Quels sont les répertoires définis pour le cache sur chaque nœud ?                                              |                                                    | `ansible_local.cache: "/var/cache/"`                                                                                                                                                     |
| 27         | Quelle est l’heure locale configurée sur chaque nœud ?                                                          |                                                    | `ansible_date_time.timezone: "UTC"`                                                                                                                                                     |
| 28         | Quelles sont les informations sur les utilisateurs connectés à chaque nœud ?                                    |                                                    | `ansible_users: [{"name": "root", "state": "logged_in", ...}]`                                                                                                                           |
| 29         | Quelle est la vitesse des interfaces réseau (si disponible) ?                                                   |                                                    | `ansible_network.speed: "1000 Mbps"`                                                                                                                                                    |
| 30         | Quelle est la liste des packages installés sur chaque nœud (Debian-based uniquement) ?                          |                                                    | `ansible_packages: ["apt", "bash", "coreutils", ...]`                                                                                                                                  |

---

### **Consignes pour l'exercice 7 :**
1. Complétez la colonne **Filtre attendu** avec le bon filtre pour chaque question.
2. Exécutez les commandes pour tester vos réponses et valider les résultats.
3. Prenez note des résultats et comparez-les avec les valeurs attendues.

