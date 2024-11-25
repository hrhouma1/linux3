# Partie-01 

1. **Quel est le rôle du fichier `inventory.ini` dans Ansible ?**  
   a) Installer les conteneurs Docker.  
   b) Lister les hôtes gérés par Ansible.  
   c) Exécuter des commandes shell.  
   d) Définir les utilisateurs locaux.

2. **Quelle commande vérifie la connectivité des hôtes depuis Ansible ?**  
   a) `ansible-playbook`.  
   b) `ansible-ping`.  
   c) `ansible all -m ping`.  
   d) `ansible-playbook -i inventory.ini ping`.

3. **Dans un fichier `docker-compose.yml`, à quoi sert le champ `networks` ?**  
   a) À définir les conteneurs à déployer.  
   b) À configurer la communication réseau des conteneurs.  
   c) À spécifier les volumes montés.  
   d) À exposer des ports.

4. **Quel module Ansible est utilisé pour installer des paquets sur un conteneur Ubuntu ?**  
   a) `yum`.  
   b) `apt`.  
   c) `package`.  
   d) `dpkg`.

5. **Dans un playbook Ansible, que permet le champ `vars_files` ?**  
   a) Inclure des fichiers de tâches.  
   b) Importer des fichiers de variables externes.  
   c) Définir des conditions pour les tâches.  
   d) Créer des fichiers dans les conteneurs.

6. **Quel module Ansible est utilisé pour copier un fichier dans un conteneur ?**  
   a) `file`.  
   b) `copy`.  
   c) `fetch`.  
   d) `archive`.

7. **Dans le fichier `docker-compose.yml`, quel champ est nécessaire pour exposer les ports d’un conteneur ?**  
   a) `expose`.  
   b) `ports`.  
   c) `command`.  
   d) `networks`.

8. **Quelle commande exécute un playbook Ansible ?**  
   a) `ansible-exec`.  
   b) `ansible-run`.  
   c) `ansible-playbook`.  
   d) `ansible-deploy`.

9. **Quel est le rôle d’un fichier `playbook.yml` dans Ansible ?**  
   a) Lister les conteneurs Docker.  
   b) Automatiser des tâches sur les hôtes gérés.  
   c) Configurer les utilisateurs SSH.  
   d) Mettre à jour les conteneurs.

10. **Quelle directive dans un playbook est utilisée pour exécuter des tâches avec des privilèges root ?**  
    a) `sudo`.  
    b) `become`.  
    c) `root`.  
    d) `admin`.

11. **Que fait la commande `docker-compose up -d` ?**  
    a) Installe Docker.  
    b) Démarre les conteneurs définis dans le fichier `docker-compose.yml`.  
    c) Supprime les conteneurs arrêtés.  
    d) Exécute des tests sur les conteneurs.

12. **Dans un inventaire Ansible, que signifie `ansible_host` ?**  
    a) Le nom de l'utilisateur SSH.  
    b) L'adresse IP de l'hôte cible.  
    c) Le mot de passe pour SSH.  
    d) Le rôle de l'hôte.

13. **Que permet de faire le module Ansible `debug` ?**  
    a) D’afficher des messages pendant l'exécution d’un playbook.  
    b) D’arrêter l’exécution d’un playbook en cas d’erreur.  
    c) D’exécuter des commandes shell sur un hôte.  
    d) D’afficher les logs des conteneurs Docker.

14. **Dans Ansible, que permet la directive `register` ?**  
    a) Inclure des fichiers externes.  
    b) Stocker la sortie d'une tâche pour une utilisation ultérieure.  
    c) Définir une liste de tâches à exécuter.  
    d) Tester la connectivité des hôtes.

15. **Quelle commande liste les hôtes d’un groupe spécifique dans un inventaire ?**  
    a) `ansible-playbook -i inventory.ini list-hosts`.  
    b) `ansible <groupe> -i inventory.ini --list-hosts`.  
    c) `ansible all --list`.  
    d) `ansible all -m list`.

16. **Comment désactiver la vérification de clé SSH pour un hôte Ansible ?**  
    a) Ajouter `StrictHostKeyChecking=no` dans `ssh_config`.  
    b) Utiliser `ansible_host_check=false` dans l'inventaire.  
    c) Activer le module `disable_key_check`.  
    d) Modifier le fichier `known_hosts`.

17. **Dans un fichier de variables Ansible, quel type est utilisé pour représenter une liste ?**  
    a) `dictionary`.  
    b) `array`.  
    c) `sequence`.  
    d) `list`.

18. **Quel module Ansible est utilisé pour archiver des fichiers ?**  
    a) `tar`.  
    b) `archive`.  
    c) `compress`.  
    d) `zip`.

19. **Que fait la directive `when` dans un playbook Ansible ?**  
    a) Détermine les conditions d'exécution d'une tâche.  
    b) Définit les dépendances entre les tâches.  
    c) Inclut un fichier de variables.  
    d) Vérifie les erreurs de syntaxe.

20. **Quel est le rôle du champ `ansible_python_interpreter` dans l'inventaire ?**  
    a) Spécifier l'interpréteur Python à utiliser sur l'hôte.  
    b) Définir l'utilisateur SSH par défaut.  
    c) Configurer le port SSH pour Ansible.  
    d) Définir la version de Python pour le système hôte.

