![image](https://github.com/user-attachments/assets/a2cd1ba8-90d8-47ac-b383-1bdfe2c3671d)

```bash
apt update
apt install ansible
ansible version 
ansible localhost -m 'ping'
```

Cependant, il y a un avertissement dans la sortie :

```
[WARNING]: No inventory was parsed, only implicit localhost is available
```

Cela signifie qu'Ansible n'a pas pu trouver un fichier d'inventaire, et il a utilisé par défaut `localhost`. Si tu souhaites éviter cet avertissement à l'avenir, tu peux soit spécifier un inventaire explicitement, soit créer un fichier d'inventaire.



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



```bash
hostname master
bash
hostnamectl
hostnamectl
```



```bash
nano /etc/hostname
```


```bash
pwd
mkdir exercices
rm -rf exercices/
mkdir exercices-ansible
cd exercices-ansible/
nano inventory
```


