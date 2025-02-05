Pour supprimer les **known hosts** en cas de problème SSH, voici les commandes à utiliser :

### 1. Supprimer une entrée spécifique des `known_hosts`
Si vous avez un message du type :
```
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
```
Vous pouvez supprimer l'entrée spécifique du fichier `~/.ssh/known_hosts` en utilisant :

```bash
ssh-keygen -R <hostname ou IP>
```
Exemple :
```bash
ssh-keygen -R 192.168.1.10
```

### 2. Supprimer complètement le fichier `known_hosts`
Si vous souhaitez supprimer toutes les entrées :
```bash
rm -f ~/.ssh/known_hosts
```

Ou, pour plus de sécurité, renommer au lieu de supprimer :
```bash
mv ~/.ssh/known_hosts ~/.ssh/known_hosts.bak
```

### 3. Supprimer une ligne spécifique manuellement
Vous pouvez aussi éditer le fichier manuellement avec :
```bash
nano ~/.ssh/known_hosts
```
Puis supprimer la ligne correspondant à l’hôte problématique.

---

Après avoir supprimé les entrées problématiques, tentez de vous reconnecter :
```bash
ssh user@host
```
Il vous demandera d'ajouter la clé SSH de nouveau (`yes`).
