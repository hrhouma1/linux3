Voici un récapitulatif détaillé de toutes les commandes exécutées, les problèmes rencontrés et les corrections appliquées lors de tes sessions avec Ansible et le système.

### Historique des commandes avec les problèmes et corrections

1. **Changer le nom d'hôte** :
   ```bash
   hostname worker1
   hostnamectl
   ```
   **Problème** : Permission refusée, l'utilisateur doit être root.
   **Correction** : Passer en utilisateur root avec `su` puis changer le nom d'hôte.

2. **Modifier le fichier `/etc/hostname`** :
   ```bash
   nano /etc/hostname
   ```
   **Problème** : Aucun problème spécifique rencontré ici.
   
3. **Créer des répertoires et fichiers pour Ansible** :
   ```bash
   mkdir exercices
   rm -rf exercices/
   mkdir exercices-ansible
   cd exercices-ansible
   nano inventory
   ```
   **Problème** : Aucun problème spécifique.

4. **Tester Ansible avec l'inventaire** :
   ```bash
   ansible all -m 'ping' -i inventory
   ```
   **Problème** : SSH nécessite `sshpass` pour l'utilisation de mots de passe.
   **Correction** : Installer `sshpass` avec `sudo apt install sshpass`.

5. **Modifier la configuration Ansible** :
   ```bash
   nano /etc/ansible/ansible.cfg
   ```
   **Problème** : Fichier `ansible.cfg` mal formé (manque de section [defaults]).
   **Correction** : Ajouter la section `[defaults]` au fichier et réessayer.

6. **Supprimer Ansible et le réinstaller** :
   ```bash
   sudo apt remove ansible
   sudo apt install ansible
   ```
   **Problème** : Aucun problème spécifique.

7. **Configurer `host_key_checking` à `false`** :
   ```bash
   nano /etc/ansible/ansible.cfg
   ```
   **Problème** : Malformation du fichier de configuration.
   **Correction** : Vérifier la bonne syntaxe du fichier, notamment la présence de la section `[defaults]`.

8. **Exécuter des commandes Ping via Ansible** :
   ```bash
   ansible all -m 'ping' -i inventory
   ```
   **Problème** : `node2` injoignable (pas de route vers l'hôte).
   **Correction** : Vérifier la configuration réseau de `node2` et s'assurer que la connexion SSH est bien configurée.

9. **Configurer la connexion réseau avec `nmtui`** :
   ```bash
   nmtui
   ```
   **Problème** : Pas de connectivité réseau.
   **Correction** : Configurer manuellement l'IP, la passerelle, et le serveur DNS via l'interface `nmtui`.

10. **Pinger le serveur de passerelle** :
    ```bash
    ping 192.168.2.1
    ```
    **Problème** : Connectivité rétablie après reconfiguration réseau.

11. **Modifier l'inventaire Ansible** :
    ```bash
    nano inventory
    ```
    **Problème** : Aucun problème spécifique. Ajout de plusieurs nœuds avec configuration SSH.

12. **Tester le Ping sur des groupes de nœuds** :
    ```bash
    ansible web -m 'ping' -i inventory
    ansible mail -m 'ping' -i inventory
    ansible proxy -m 'ping' -i inventory
    ansible allservers -m 'ping' -i inventory
    ```
    **Problème** : `node3` injoignable (SSH failed).
    **Correction** : Vérifier et corriger la configuration SSH sur `node3`.

13. **Lister les hôtes par groupe** :
    ```bash
    ansible ungrouped -i inventory --list-hosts
    ansible web -i inventory --list-hosts
    ansible mail -i inventory --list-hosts
    ansible allservers -i inventory --list-hosts
    ```

14. **Vérifier l'état des serveurs avec des commandes Ansible** :
    ```bash
    ansible node1 -m command -a "uptime" -i inventory
    ansible node2 -m command -a "uptime" -i inventory
    ansible node1 -m command -a "date" -i inventory
    ansible node2 -m command -a "date" -i inventory
    ```
    **Problème** : `node2` : erreur "Aucun fichier ou dossier de ce type" pour la commande `time`.
    **Correction** : Utiliser `date` au lieu de `time`.

15. **Lister les CPU via Ansible** :
    ```bash
    ansible node2 -m command -a "lscpu | head -n 5" -i inventory
    ```

16. **Vérifier l'état réseau des machines** :
    ```bash
    ip a
    ping 192.168.2.1
    ```

17. **Afficher les informations sur le temps d'exécution et la date** :
    ```bash
    time
    date
    uptime
    ```

### Problèmes majeurs rencontrés :
- **SSH injoignable** sur certains nœuds : Problème résolu en configurant correctement le réseau et le SSH.
- **Mauvaise configuration d'Ansible** : Notamment l'absence de la section `[defaults]` dans `ansible.cfg`.
- **Commande `time` non valide** : Problème corrigé en utilisant `date` pour obtenir l'heure actuelle.
- **Problèmes réseau sur `node2`** : Résolu en reconfigurant l'adresse IP avec `nmtui`.

### Résumé des corrections appliquées :
- Ajout de la section `[defaults]` dans `ansible.cfg`.
- Configuration des nœuds réseau manuellement avec `nmtui`.
- Vérification des configurations SSH pour s'assurer que tous les nœuds sont accessibles.
- Utilisation de commandes alternatives lorsqu'un nœud retourne une erreur (exemple : `date` au lieu de `time`).

Cela couvre toutes les commandes et ajustements réalisés pour résoudre les divers problèmes rencontrés.
