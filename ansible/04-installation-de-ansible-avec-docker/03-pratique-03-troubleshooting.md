
# Troubleshooting #1 : 

![image](https://github.com/user-attachments/assets/a81cd2cb-4081-42aa-8179-961c37e47b7e)


# Solutions possibles :

### Problèmes observés :
1. **Avertissements concernant l'interpréteur Python** :
   - Chaque hôte (node1, node2, node3, etc.) affiche un avertissement indiquant que l'interpréteur Python utilisé sur ces hôtes a été découvert automatiquement (par exemple, `/usr/bin/python3.11`, `/usr/bin/python3.12`, etc.). Cela peut poser des problèmes si une autre version de Python est installée ultérieurement, modifiant le chemin de l'interpréteur.

2. **Erreur "No such file or directory"** :
   - La commande `ls -lh /tmp/logs.tar.gz` échoue sur tous les nœuds avec le message : `No such file or directory`. Cela signifie que le fichier d'archive `/tmp/logs.tar.gz` n'a pas été créé ou n'existe pas sur les nœuds.

### Solutions :

#### 1. **Spécifier explicitement l'interpréteur Python dans l'inventaire** :
   Pour éviter les avertissements liés à la découverte automatique de l'interpréteur Python, vous pouvez spécifier explicitement le chemin vers l'interpréteur Python dans votre fichier `inventory.ini`. Par exemple :

   ```ini
   node1 ansible_host=192.168.1.101 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   node2 ansible_host=192.168.1.102 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   node3 ansible_host=192.168.1.103 ansible_user=root ansible_python_interpreter=/usr/bin/python3
   ```

   Cela garantira qu'Ansible utilise toujours la bonne version de Python sur chaque hôte.

#### 2. **Vérification de la tâche d'archivage des logs** :
   L'erreur `No such file or directory` indique que l'archive `/tmp/logs.tar.gz` n'a pas été créée comme prévu par votre playbook.

   Voici quelques étapes pour résoudre ce problème :
   
   - **Vérifiez le module `archive`** : Assurez-vous que le module `archive` est bien installé et disponible sur vos nœuds. Ce module fait partie de la collection `community.general`. Si cette collection n'est pas installée, vous pouvez l'ajouter avec la commande suivante :
     ```bash
     ansible-galaxy collection install community.general
     ```

   - **Exécutez uniquement la tâche d'archivage avec plus de détails** : Utilisez les tags pour exécuter uniquement la tâche d'archivage et ajoutez un niveau de verbosité pour voir plus de détails sur ce qui se passe :
     ```bash
     ansible-playbook -i inventory.ini multi-tasks-playbook.yml --tags archive_logs -vvv
     ```

   - **Vérifiez les permissions des répertoires** : Assurez-vous que le répertoire `/var/log` est accessible et que le répertoire `/tmp` est accessible en écriture par Ansible sur tous les nœuds.

#### 3. **Vérification manuelle des fichiers** :
   Après avoir exécuté le playbook, connectez-vous à un des nœuds pour vérifier manuellement si le fichier a été créé correctement :

   ```bash
   ssh root@<node_ip>
   ls -lh /tmp/logs.tar.gz
   ```

En suivant ces étapes, vous devriez être en mesure de résoudre les avertissements Python et de corriger la création de l'archive des logs sur vos nœuds.

