---------
# 01 - C'est quoi procps-ng ?

**`procps-ng`** est une collection d'outils système pour Linux, utilisés pour surveiller les processus en cours d'exécution et la gestion des ressources système. Ces outils font partie des utilitaires essentiels dans de nombreux systèmes Linux et permettent d’obtenir des informations détaillées sur les performances du système, la mémoire, l'utilisation CPU, les processus, etc.

Voici un aperçu des outils principaux fournis par **`procps-ng`** :

### Outils principaux de `procps-ng`

1. **`ps`** : Affiche les processus en cours d’exécution avec des informations détaillées (PID, utilisateur, mémoire, CPU).
   - Exemple : `ps aux` pour lister tous les processus.

2. **`top`** : Affiche en temps réel l’utilisation de la CPU et de la mémoire pour chaque processus.
   - Utilisation courante : exécuter simplement `top` pour démarrer l'affichage interactif.

3. **`free`** : Affiche l'utilisation de la mémoire (RAM et SWAP).
   - Exemple : `free -h` pour des informations lisibles (en Go, Mo).

4. **`vmstat`** : Montre des statistiques système, comme l'utilisation de la CPU, la mémoire, et les opérations de disque.
   - Exemple : `vmstat 1` pour des mises à jour toutes les secondes.

5. **`pmap`** : Affiche l’utilisation de la mémoire d'un processus particulier.
   - Exemple : `pmap <PID>` pour obtenir la carte mémoire d’un processus.

6. **`pidof`** : Donne le PID (identifiant de processus) d'un programme en cours d'exécution.
   - Exemple : `pidof bash` pour obtenir les PID de tous les processus `bash`.

7. **`kill`** : Envoie un signal à un processus, souvent utilisé pour terminer un processus.
   - Exemple : `kill -9 <PID>` pour forcer l’arrêt d'un processus.

8. **`uptime`** : Affiche depuis combien de temps le système est en marche et la charge moyenne du système.
   - Utilisation : exécuter simplement `uptime`.

9. **`watch`** : Permet d’exécuter une commande de façon répétée à intervalle régulier et d'en afficher les résultats.
   - Exemple : `watch -n 1 df -h` pour surveiller l’espace disque chaque seconde.

### Utilité de `procps-ng`

Ces outils sont essentiels pour les administrateurs systèmes et les développeurs qui veulent comprendre et gérer les performances des systèmes Linux. Ils permettent de surveiller et d’analyser en temps réel les ressources système, et sont particulièrement utiles pour diagnostiquer les problèmes de performances.

En somme, `procps-ng` regroupe des outils polyvalents pour la gestion et la surveillance des processus et des ressources système, souvent inclus dans les distributions Linux pour faciliter la gestion quotidienne du système.


---------
# 02 - Sur quelle distribution ?

**`procps-ng`** est disponible sur la plupart des distributions Linux modernes, car c'est un paquet essentiel pour la gestion des processus et des ressources système. On le retrouve notamment sur :

1. **Debian et Ubuntu** : `procps-ng` est généralement installé par défaut dans les distributions basées sur Debian, comme Ubuntu. Vous pouvez l'installer ou le mettre à jour avec :
   ```bash
   sudo apt update
   sudo apt install procps
   ```

2. **Red Hat, CentOS, et Fedora** : Ces distributions utilisent aussi `procps-ng` par défaut, étant donné son importance pour la gestion du système. L’installation ou la mise à jour se fait avec :
   ```bash
   sudo dnf install procps-ng  # Pour Fedora, CentOS 8, et RHEL 8+
   sudo yum install procps-ng  # Pour les versions antérieures de CentOS et RHEL
   ```

3. **Arch Linux** : Sur Arch et ses dérivés, `procps-ng` est également inclus dans les paquets de base et peut être installé ou mis à jour via :
   ```bash
   sudo pacman -S procps-ng
   ```

4. **SUSE et openSUSE** : `procps-ng` est aussi intégré dans les distributions SUSE pour la surveillance des processus et la gestion système, et vous pouvez le gérer avec :
   ```bash
   sudo zypper install procps
   ```

Dans la plupart des cas, `procps-ng` est déjà présent par défaut dans les installations Linux, car il fournit des outils essentiels (`ps`, `top`, `free`, etc.) pour l’administration et la surveillance du système.
