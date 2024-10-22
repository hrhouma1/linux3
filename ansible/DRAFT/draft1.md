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


# 17


# 17


# 17


# 17



# 17


# 17




# 17


# 17


# 17


# 17


# 17



# 17


# 17




# 17


# 17


# 17


# 17


# 17



# 17


# 17



# 17


# 17


# 17


# 17


# 17



# 17


# 17



# 17


# 17


# 17


# 17


# 17



# 17


# 17



# 17


# 17


# 17


# 17


# 17



# 17


# 17


# 17


# 17


# 17


# 17


# 17



# 17


# 17






