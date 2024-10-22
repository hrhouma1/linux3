# 1

![image](https://github.com/user-attachments/assets/70f1ae12-890b-4b58-a7c0-c0b26602a03d)

# 2

La commande extraite de l'image est :

```bash
ansible localhost -m 'ping'
```

Cependant, il y a un avertissement dans la sortie :

```
[WARNING]: No inventory was parsed, only implicit localhost is available
```

Cela signifie qu'Ansible n'a pas pu trouver un fichier d'inventaire, et il a utilisé par défaut `localhost`. Si tu souhaites éviter cet avertissement à l'avenir, tu peux soit spécifier un inventaire explicitement, soit créer un fichier d'inventaire.


# 3

![image](https://github.com/user-attachments/assets/93bcf2ec-f548-4bb3-9415-b25b9d232d0a)

Voici les commandes extraites de l'image :

```bash
sudo su -
hostname worker1
hostnamectl
su
hostname worker1
hostnamectl
nano /etc/hostname
bash
hostnamectl
```

# 4

![image](https://github.com/user-attachments/assets/53666eab-781a-46d7-919d-efad0cc681a2)

Voici les commandes extraites de l'image :

```bash
hostname master
bash
hostnamectl
hostnamectl
```

# 5

![image](https://github.com/user-attachments/assets/b565b4fe-68dc-4036-9d6f-467432329799)

Voici la commande extraite de l'image :

```bash
nano /etc/hostname
```

Dans ce fichier, tu peux effectivement écrire `master` pour changer le nom d'hôte de la machine.

# 6

![image](https://github.com/user-attachments/assets/d8ea4387-c576-40a4-a631-cfba103d3d74)

Voici les commandes extraites de l'image :

```bash
pwd
mkdir exercices
rm -rf exercices/
mkdir exercices-ansible
cd exercices-ansible/
nano inventory
```

![image](https://github.com/user-attachments/assets/7db8a3f6-84f0-4723-9856-324071d3ce07)

# 7

Voici la commande extraite de l'image :

```bash
ansible all -m 'ping' -i inventory
```

L'erreur est la suivante :

```
FAILED! => {
    "msg": "to use the 'ssh' connection type with passwords, you must install the sshpass program"
}
```

L'erreur indique que pour utiliser la connexion SSH avec un mot de passe, il est nécessaire d'installer le programme **sshpass**. Pour corriger cela, tu peux exécuter la commande suivante pour installer `sshpass` :

```bash
sudo apt install sshpass
```

Une fois installé, tu devrais pouvoir exécuter la commande Ansible sans cette erreur.


# 8

![image](https://github.com/user-attachments/assets/06f64b5b-6ea6-4df9-aaa7-d85a3073f762)


Voici les commandes extraites de l'image :

```bash
nano /etc/ansible/ansible.cfg
cd
```

![image](https://github.com/user-attachments/assets/5b56a9bf-e2f1-4bfd-9512-a58174f5887c)


Voici les commandes extraites de l'image :

```bash
nano /etc/ansible/ansible.cfg
cd /etc/ansible
ansible --version
```

# 9


```bash
nano /etc/ansible/ansible.cfg
cd /etc/ansible
ansible --version
ansible-config dump
```

![image](https://github.com/user-attachments/assets/9140b19f-5f09-4cd2-93df-18d77471a25a)

# 10

Voici la commande extraite de l'image :

```bash
sudo apt remove ansible
```


# 11

Pour régler ce problème, voici la configuration extraite de l'image :

Créer un fichier `ansible.cfg` dans le répertoire de votre projet (par exemple `ansible/ansible.cfg`) avec le contenu suivant :

```ini
[defaults]
host_key_checking = false
```

Cela désactivera la vérification des clés hôtes lors de la connexion aux cibles.

![image](https://github.com/user-attachments/assets/95815f11-6e40-4168-9a2b-e616f001ea20)


- https://stackoverflow.com/questions/42462435/ansible-provisioning-error-using-a-ssh-password-instead-of-a-key-is-not-possibl
- https://stackoverflow.com/questions/23074412/how-to-set-host-key-checking-false-in-ansible-inventory-file



# 12

![image](https://github.com/user-attachments/assets/1acaca32-6f02-4040-9675-3b2863412807)

![image](https://github.com/user-attachments/assets/f5fd18a7-ba6a-46c0-9b83-cf3790ed127f)

![image](https://github.com/user-attachments/assets/e7fec34e-43fe-49a9-9c06-1c6e2be923f4)

![image](https://github.com/user-attachments/assets/7c68f749-2f46-46ad-a622-49b28b1049ef)


# 13 


```bash
nano /etc/ansible/ansible.cfg
cd
cd exercices-ansible/
ansible all -m 'ping' -i inventory
nano /etc/ansible/ansible.cfg
```


# 14

![image](https://github.com/user-attachments/assets/8ca5bdc6-42a9-42ed-a1d4-a1e0d61eb70e)

Il semble que tu as oublié d'inclure la section `[defaults]` dans le fichier de configuration Ansible. Voici l'extrait correct avec la section `[defaults]` :

```ini
[defaults]
host_key_checking = false
```

Assure-toi que cette section est bien présente en haut de ton fichier `ansible.cfg` pour éviter les erreurs.


# 15

![image](https://github.com/user-attachments/assets/01314c13-231e-478b-a991-fc352345d5cd)

Voici les commandes extraites de l'image :

```bash
nano /etc/ansible/ansible.cfg
cd
cd exercices-ansible/
ansible all -m 'ping' -i inventory
nano /etc/ansible/ansible.cfg
cd
cd exercices-ansible/
ansible all -m 'ping' -i inventory
```




# 16

![image](https://github.com/user-attachments/assets/71415795-a91d-4686-9d69-4814d8227b1e)

Voici le contenu extrait du fichier `inventory` :

```
master  ansible_connection=local
node1   ansible_host=192.168.2.12 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node2   ansible_host=192.168.2.13 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
```

Cela définit un fichier d'inventaire pour Ansible avec une connexion locale pour le master et des connexions SSH pour `node1` et `node2`, utilisant l'utilisateur et le mot de passe `eleve`.

![image](https://github.com/user-attachments/assets/1046c5ca-f3b8-4896-8845-308affe5e3a6)

Voici les commandes extraites de l'image :

```bash
nano inventory
ansible all -m 'ping' -i inventory
```

![image](https://github.com/user-attachments/assets/5bc5b176-7c89-41b8-aa2a-871650c8c1f9)

Le problème avec le **node2** semble être lié à la connexion SSH et au message d'erreur suivant :

```
node2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 192.168.2.13 port 22: No route to host",
    "unreachable": true
}
```

L'erreur "No route to host" indique que la machine hôte ne parvient pas à atteindre l'adresse IP du **node2** (192.168.2.13) sur le port 22 (utilisé pour SSH).

Voici quelques pistes pour résoudre ce problème :

1. **Vérification du réseau** : Assure-toi que le **node2** est bien en ligne et accessible depuis le **master**. Tu peux utiliser la commande `ping` pour tester la connectivité :

   ```bash
   ping 192.168.2.13
   ```

   Si tu obtiens un message comme **"Destination Host Unreachable"**, cela signifie qu'il y a un problème réseau entre le **master** et le **node2**.

2. **Vérification de SSH sur node2** : Vérifie que le service SSH est bien actif et configuré correctement sur le **node2**. Tu peux redémarrer le service SSH sur le **node2** en exécutant :

   ```bash
   sudo systemctl restart ssh
   ```

3. **Pare-feu** : Assure-toi que le pare-feu sur le **node2** ne bloque pas les connexions sur le port 22. Si nécessaire, ajoute une règle pour autoriser SSH :

   ```bash
   sudo ufw allow 22/tcp
   ```

4. **Route statique ou réseau** : Si tu utilises des routes statiques ou des interfaces multiples, assure-toi que la configuration réseau est correcte et que le **node2** peut être atteint depuis le **master**.

Essaye ces étapes et teste à nouveau la connectivité avec Ansible après avoir corrigé les éventuels problèmes.


# 17
![image](https://github.com/user-attachments/assets/e579e13f-54a9-4899-ab1d-a5736c0c208d)

![image](https://github.com/user-attachments/assets/f825381c-ba10-45b4-bf3d-01525782c766)

![image](https://github.com/user-attachments/assets/6bd4d2ee-5de9-4f6e-8c38-0870957f5da7)

![image](https://github.com/user-attachments/assets/e086ccfd-2b0a-4b05-ae28-c16779388016)

![image](https://github.com/user-attachments/assets/276d2215-aed1-444b-9ec2-0ea36924a60c)


1. **Commande `ping`** (qui échoue car le réseau est inaccessible) :

   ```bash
   ping 192.168.2.13
   ```

2. **Utilisation de `nmtui` pour configurer le réseau :**

   - Exécuter la commande pour ouvrir l'interface de configuration réseau :

     ```bash
     nmtui
     ```

   - Dans l'interface **NetworkManager TUI**, sélectionne **Edit a connection**.
   - Choisir l'interface réseau à configurer, ici `enp0s3`.
   - Configurer l'interface manuellement avec les paramètres suivants :
     - **Adresse IP** : `192.168.2.13/24`
     - **Passerelle** : `192.168.2.1`
     - **Serveur DNS** : `8.8.8.8` et supprimer les autres si nécessaire.
   - Sauvegarder et appliquer les modifications.

Ces étapes permettent de configurer correctement la connexion réseau pour que le node2 soit accessible.

![image](https://github.com/user-attachments/assets/e38071a0-83b8-4711-9947-580f76ba3392)

Dans cette image, nous voyons l'interface réseau **enp0s3** sous la section **Wired connection 1** dans l'outil de configuration **nmtui**.

Pour éditer cette connexion, tu peux suivre ces étapes :

1. **Sélectionner l'interface enp0s3** avec les touches directionnelles.
2. Cliquer sur **Edit** pour entrer dans la configuration de cette connexion.
3. Une fois dans la configuration, tu pourras ajuster les paramètres réseau comme l'adresse IP, la passerelle, et les DNS, comme vu précédemment.

Cela permet d'ajuster la configuration réseau pour rendre le node accessible.





# 17

![image](https://github.com/user-attachments/assets/63d9ab13-5ff6-48e6-9440-e6e9508491c6)

![image](https://github.com/user-attachments/assets/b3036de6-42c2-4a0c-8035-35b1e565e197)


1. **Commande `ip a`** pour afficher les interfaces réseau et leur configuration :

   ```bash
   ip a
   ```

   Cela montre que l'interface **enp0s3** est configurée avec l'adresse IP `192.168.2.13/24`.

2. **Commande `ping` pour tester la connectivité vers un autre hôte sur le réseau** :

   ```bash
   ping 192.168.2.10
   ```

   Le résultat montre que le ping est réussi, confirmant que la connectivité réseau est bien établie entre les machines.

3. **Commande Ansible pour exécuter un ping sur tous les nœuds définis dans l'inventaire** :

   ```bash
   ansible all -m 'ping' -i inventory
   ```

   Le résultat montre que **master**, **node1**, et **node2** sont tous accessibles et répondent correctement avec un `ping: pong`, indiquant que la configuration réseau et SSH est désormais fonctionnelle sur tous les nœuds.

![image](https://github.com/user-attachments/assets/6f66b2f3-e2c3-4b8d-92f5-99de6bb4cfbc)


Voici le contenu du fichier `inventory` extrait après l'édition avec `nano` :

```
master  ansible_connection=local
node1   ansible_host=192.168.2.12 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node2   ansible_host=192.168.2.13 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
```

Ce fichier définit le **master** avec une connexion locale, et les **nodes 1** et **2** avec une connexion SSH en utilisant l'utilisateur `eleve` et le mot de passe `eleve`.

Voici le contenu du fichier `inventory` extrait après l'édition avec `nano` :

```ini
master  ansible_connection=local
node1   ansible_host=192.168.2.12 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node2   ansible_host=192.168.2.13 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node3   ansible_host=192.168.2.14 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node4   ansible_host=192.168.2.15 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve

[web]
node1

[mail]
node2

[proxy]
node3

[allservers:children]
web
mail
proxy
```

Ce fichier d'inventaire organise les serveurs en groupes tels que **web**, **mail**, et **proxy**, et utilise un groupe parent **allservers** qui inclut tous les sous-groupes. Les connexions SSH sont configurées pour chaque nœud avec l'utilisateur `eleve` et le mot de passe `eleve`.

![image](https://github.com/user-attachments/assets/07eedd14-bbfe-4fe0-979e-460b5f466c17)


Voici les commandes extraites de l'image :

```bash
ansible node2 -m 'ping' -i inventory
nano inventory
ansible web -m 'ping' -i inventory
ansible mail -m 'ping' -i inventory
ansible allservers -m 'ping' -i inventory
```

# 17


Voici la commande complète pour le **node1** ainsi que celle pour **node2** dans Ansible :

```bash
ansible node1 -m 'ping' -i inventory
ansible node2 -m 'ping' -i inventory
``` 

Cela te permet de tester individuellement la connectivité avec chaque nœud via Ansible en utilisant la commande `ping`.

![image](https://github.com/user-attachments/assets/997fb891-890b-4d8f-b2b4-ee9563c443af)

Voici les commandes extraites de l'image :

```bash
ansible allservers -m 'ping' -i inventory
ansible allservers -m 'ping' -i inventory
```

Le résultat montre que **node1** et **node2** sont accessibles, mais **node3** est "UNREACHABLE". Cela signifie qu'il y a un problème de connexion SSH avec **node3**, avec l'erreur suivante :

```
"msg": "Failed to connect to the host via ssh: ssh: connect to host 192.168.2.14 port 22: No route to host",
"unreachable": true
```

Cela peut être causé par un problème réseau ou une mauvaise configuration de SSH sur **node3**.

![image](https://github.com/user-attachments/assets/6d2d5344-aa20-4da7-8d82-6616472953c5)

# 17

Voici la correction du fichier `inventory` pour inclure correctement les nœuds dans les groupes, en ajustant la configuration :

```ini
master  ansible_connection=local
node1   ansible_host=192.168.2.12 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node2   ansible_host=192.168.2.13 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node3   ansible_host=192.168.2.14 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node4   ansible_host=192.168.2.15 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve

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

Cela corrige l'erreur dans le groupe **web** en incluant **node4** et permet d'avoir tous les nœuds bien organisés dans leurs groupes respectifs.

![image](https://github.com/user-attachments/assets/ef53fe97-4f5a-4266-9ceb-8291fcecd98c)

Voici les commandes extraites de l'image :

```bash
nano inventory
ansible ungrouped -i inventory --list-hosts
ansible web -i inventory --list-hosts
ansible mail -i inventory --list-hosts
ansible allservers -i inventory --list-hosts
ansible ungrouped -i inventory --list-hosts
wc -l /etc/ansible/ansible.cfg
```

Voici les commandes extraites de l'image :

```bash
ansible mail -i inventory --list-hosts
ansible allservers -i inventory --list-hosts
ansible ungrouped -i inventory --list-hosts
wc -l /etc/ansible/ansible.cfg
ansible node1 -m command -a "uptime"
ansible node1 -m command -a "uptime" -i inventory
ansible node2 -m command -a "uptime" -i inventory
ansible node2 -m command -a "time" -i inventory
ansible node2 -m command -a "date" -i inventory
```

![image](https://github.com/user-attachments/assets/aef9348c-c51e-46e0-8526-ce775ddc247f)

Voici les commandes extraites de l'image :

```bash
ip a
ping 192.168.2.10
date
```

![image](https://github.com/user-attachments/assets/737e4b44-46b3-47d7-9421-64f41ba7da02)

Voici les commandes extraites de l'image sur la machine Ubuntu :

```bash
time
date
uptime
```


# 17


Voici les commandes extraites de l'image, ainsi que quelques autres que tu peux ajouter pour des tests plus complets :

### Commandes extraites :
```bash
ansible ungrouped -i inventory --list-hosts
wc -l /etc/ansible/ansible.cfg
ansible node1 -m command -a "uptime" -i inventory
ansible node2 -m command -a "uptime" -i inventory
ansible node2 -m command -a "time" -i inventory
ansible node2 -m command -a "date" -i inventory
ansible node1 -m command -a "date" -i inventory
ansible-doc -l | wc -l
ansible node2 -m command -a "lscpu | head -n 5" -i inventory
```

### Commandes supplémentaires à ajouter :
1. **Tester la mémoire disponible** :
   ```bash
   ansible all -m command -a "free -h" -i inventory
   ```

2. **Lister les processus actifs** :
   ```bash
   ansible all -m command -a "ps aux" -i inventory
   ```

3. **Vérifier l'espace disque** :
   ```bash
   ansible all -m command -a "df -h" -i inventory
   ```

4. **Redémarrer un nœud** :
   ```bash
   ansible node1 -m command -a "reboot" -i inventory
   ```

5. **Mettre à jour le système** :
   ```bash
   ansible all -m command -a "sudo apt update && sudo apt upgrade -y" -i inventory
   ```

Ces commandes te permettront d'effectuer une gestion complète et un diagnostic de tes nœuds dans Ansible.


Voici encore plus de commandes que tu peux ajouter pour étendre tes capacités de gestion et de diagnostic des nœuds avec Ansible :

### Commandes réseau :

1. **Vérifier les interfaces réseau** :
   ```bash
   ansible all -m command -a "ip a" -i inventory
   ```

2. **Vérifier la table de routage** :
   ```bash
   ansible all -m command -a "netstat -rn" -i inventory
   ```

3. **Tester la connectivité vers un autre hôte** :
   ```bash
   ansible all -m command -a "ping -c 4 8.8.8.8" -i inventory
   ```

### Commandes système et sécurité :

4. **Lister les utilisateurs du système** :
   ```bash
   ansible all -m command -a "cat /etc/passwd" -i inventory
   ```

5. **Vérifier les services en cours d'exécution** :
   ```bash
   ansible all -m command -a "systemctl list-units --type=service --state=running" -i inventory
   ```

6. **Vérifier les journaux système** :
   ```bash
   ansible all -m command -a "journalctl -n 100" -i inventory
   ```

### Commandes pour l'optimisation du système :

7. **Libérer la mémoire cache** :
   ```bash
   ansible all -m command -a "sudo sync; echo 3 | sudo tee /proc/sys/vm/drop_caches" -i inventory
   ```

8. **Vérifier la température du CPU** (si le matériel supporte) :
   ```bash
   ansible all -m command -a "sensors" -i inventory
   ```

9. **Vérifier l'état de la batterie (pour les portables)** :
   ```bash
   ansible all -m command -a "upower -i /org/freedesktop/UPower/devices/battery_BAT0" -i inventory
   ```

### Commandes de diagnostic du disque :

10. **Lister les partitions de disque** :
   ```bash
   ansible all -m command -a "lsblk" -i inventory
   ```

11. **Vérifier les erreurs de disque** :
   ```bash
   ansible all -m command -a "sudo dmesg | grep -i error" -i inventory
   ```

12. **Vérifier l'état des disques (si disponible)** :
   ```bash
   ansible all -m command -a "sudo smartctl -a /dev/sda" -i inventory
   ```

### Commandes de gestion des utilisateurs et permissions :

13. **Ajouter un nouvel utilisateur** :
   ```bash
   ansible all -m command -a "sudo useradd -m newuser" -i inventory
   ```

14. **Modifier les permissions d'un fichier** :
   ```bash
   ansible all -m command -a "sudo chmod 755 /path/to/file" -i inventory
   ```

15. **Vérifier les fichiers avec des permissions spéciales** :
   ```bash
   ansible all -m command -a "find / -perm /4000" -i inventory
   ```

### Commandes pour gérer les packages :

16. **Lister les packages installés** :
   ```bash
   ansible all -m command -a "dpkg -l" -i inventory   # Pour systèmes Debian
   ansible all -m command -a "rpm -qa" -i inventory   # Pour systèmes RedHat
   ```

17. **Installer un nouveau package** :
   ```bash
   ansible all -m command -a "sudo apt install -y package_name" -i inventory   # Pour systèmes Debian
   ansible all -m command -a "sudo yum install -y package_name" -i inventory   # Pour systèmes RedHat
   ```

18. **Supprimer un package** :
   ```bash
   ansible all -m command -a "sudo apt remove -y package_name" -i inventory   # Pour systèmes Debian
   ansible all -m command -a "sudo yum remove -y package_name" -i inventory   # Pour systèmes RedHat
   ```

Ces commandes te permettent d'approfondir l'administration des systèmes, le diagnostic et la gestion via Ansible.






