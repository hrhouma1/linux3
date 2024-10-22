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











