### Tutoriel complet pour configurer et utiliser Ansible avec les commandes extraites

Ce tutoriel vous guide pas à pas dans la configuration d'Ansible, la création d'un fichier d'inventaire, l'exécution de commandes Ansible, et la gestion de serveurs distants via SSH. Nous avons inclus toutes les commandes, les problèmes rencontrés, et les corrections appliquées, avec des explications détaillées.

#### Étape 1 : Installation d'Ansible
Pour commencer, nous devons installer Ansible. Si Ansible est déjà installé, vous pouvez passer cette étape.

```bash
sudo apt update
sudo apt install ansible
```

#### Étape 2 : Configuration d'Ansible

1. Créer un répertoire de travail pour Ansible :

```bash
mkdir exercices-ansible
cd exercices-ansible
```

2. Créer un fichier d'inventaire avec la liste de vos serveurs :

```bash
nano inventory
```

Voici un exemple de fichier d'inventaire pour 4 nœuds :

```ini
master  ansible_connection=local
node1   ansible_host=192.168.2.12 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node2   ansible_host=192.168.2.13 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node3   ansible_host=192.168.2.14 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
node4   ansible_host=192.168.2.15 ansible_connection=ssh ansible_user=eleve ansible_ssh_pass=eleve
```

3. Configurer Ansible pour désactiver la vérification des clés hôtes :

```bash
nano /etc/ansible/ansible.cfg
```

Ajouter le contenu suivant dans le fichier :

```ini
[defaults]
host_key_checking = false
```

#### Étape 3 : Tester la connectivité avec les nœuds

1. Tester la connectivité avec tous les nœuds en utilisant `ping` :

```bash
ansible all -m 'ping' -i inventory
```

Si vous obtenez une erreur indiquant que `sshpass` est manquant, installez-le :

```bash
sudo apt install sshpass
```

2. Tester des groupes spécifiques de nœuds (comme `web`, `mail`, `proxy`) :

```bash
ansible web -m 'ping' -i inventory
ansible mail -m 'ping' -i inventory
ansible proxy -m 'ping' -i inventory
```

#### Étape 4 : Résoudre les problèmes de connectivité réseau

Si un nœud est injoignable avec un message d'erreur tel que **"No route to host"**, voici les étapes pour résoudre ce problème :

1. Vérifier la connectivité réseau avec la commande `ping` :

```bash
ping 192.168.2.13
```

2. Si la machine n'est pas accessible, vérifier la configuration réseau avec `nmtui` :

```bash
nmtui
```

- Choisissez **Edit a connection** et configurez manuellement l'IP, la passerelle, et le serveur DNS si nécessaire.

#### Étape 5 : Exécution de commandes sur les nœuds via Ansible

1. Exécuter des commandes de diagnostic comme `uptime`, `date`, ou `free` :

```bash
ansible node1 -m command -a "uptime" -i inventory
ansible node2 -m command -a "date" -i inventory
ansible all -m command -a "free -h" -i inventory
```

2. Exécuter des commandes système comme `df -h` pour vérifier l'espace disque :

```bash
ansible all -m command -a "df -h" -i inventory
```

3. Exécuter des commandes pour redémarrer un nœud :

```bash
ansible node1 -m command -a "reboot" -i inventory
```

#### Étape 6 : Gestion de l'inventaire et des groupes

1. Lister les nœuds dans chaque groupe ou l'inventaire complet :

```bash
ansible allservers -i inventory --list-hosts
ansible mail -i inventory --list-hosts
ansible ungrouped -i inventory --list-hosts
```

2. Exécuter des commandes sur des groupes de nœuds :

```bash
ansible allservers -m 'ping' -i inventory
```

#### Étape 7 : Exemples avancés d'utilisation d'Ansible

1. Exécuter une commande pour afficher les 5 premiers résultats de `lscpu` (affiche les informations CPU) :

```bash
ansible node2 -m command -a "lscpu | head -n 5" -i inventory
```

2. Exécuter un script de mise à jour sur tous les nœuds :

```bash
ansible all -m command -a "sudo apt update && sudo apt upgrade -y" -i inventory
```

3. Lister tous les packages installés sur un nœud :

```bash
ansible node1 -m command -a "dpkg -l" -i inventory
```

#### Étape 8 : Diagnostic du système et journalisation

1. Exécuter une commande pour lister les processus en cours d'exécution :

```bash
ansible all -m command -a "ps aux" -i inventory
```

2. Vérifier les journaux système récents :

```bash
ansible all -m command -a "journalctl -n 100" -i inventory
```

3. Exécuter une commande pour afficher les erreurs du disque :

```bash
ansible all -m command -a "sudo dmesg | grep -i error" -i inventory
```

### Conclusion

Ce tutoriel couvre l'installation et la configuration d'Ansible, la gestion des nœuds via SSH, ainsi que des commandes courantes pour diagnostiquer et gérer les serveurs à distance. N'hésitez pas à adapter ces commandes à vos besoins spécifiques, et à ajouter des fonctionnalités supplémentaires comme la gestion des utilisateurs ou l'optimisation des performances du système.

Si vous rencontrez d'autres erreurs ou si vous avez besoin de plus de fonctionnalités, vous pouvez toujours étendre ce guide avec des commandes supplémentaires comme la gestion de pare-feu ou des scripts Ansible plus complexes.
