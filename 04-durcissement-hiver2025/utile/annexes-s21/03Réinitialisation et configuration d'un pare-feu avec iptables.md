### Exemple complet : Réinitialisation et configuration d'un pare-feu avec `iptables`

L'exemple ci-dessous montre comment :
1. **Réinitialiser complètement `iptables`** (`iptables -F` et `iptables -X`).
2. **Définir une politique par défaut** (`DROP` par défaut, sauf pour `OUTPUT`).
3. **Autoriser certaines connexions** :
   - SSH (`port 22`).
   - HTTP (`port 80`) et HTTPS (`port 443`).
   - DNS (`port 53` UDP).
   - Réponses aux connexions initiées (`RELATED,ESTABLISHED`).

---

### Étape 1 : Réinitialiser `iptables`
```bash
iptables -F        # Supprime toutes les règles existantes
iptables -X        # Supprime toutes les chaînes personnalisées
iptables -t nat -F # Vide la table NAT
iptables -t mangle -F # Vide la table Mangle
iptables -t raw -F # Vide la table Raw
```

---

### Étape 2 : Définir une politique stricte par défaut
```bash
iptables -P INPUT DROP    # Bloque toutes les connexions entrantes par défaut
iptables -P FORWARD DROP  # Bloque tout le forwarding (utile pour un routeur)
iptables -P OUTPUT ACCEPT # Autorise toutes les connexions sortantes
```

---

### Étape 3 : Autoriser les connexions essentielles
#### Autoriser le trafic loopback (localhost)
```bash
iptables -A INPUT -i lo -j ACCEPT
```

#### Autoriser les connexions déjà établies
```bash
iptables -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
```

#### Autoriser SSH (connexion distante)
```bash
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

#### Autoriser HTTP et HTTPS (serveur web)
```bash
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```

#### Autoriser le DNS (résolution des noms de domaine)
```bash
iptables -A INPUT -p udp --dport 53 -j ACCEPT
```

---

### Étape 4 : Sauvegarder la configuration
Si tu veux rendre les règles persistantes après un redémarrage :

**Sur Debian/Ubuntu :**
```bash
iptables-save > /etc/iptables/rules.v4
```

**Sur CentOS/RHEL :**
```bash
service iptables save
```

---

### Vérification finale
Voir les règles appliquées :
```bash
iptables -L -v -n
```

---

### Explication
1. `iptables -F` et `iptables -X` effacent tout pour partir de zéro.
2. On applique des politiques par défaut strictes (`DROP` sur `INPUT` et `FORWARD`).
3. On ajoute des règles pour :
   - Le loopback (`lo`) : nécessaire pour les communications internes.
   - Les connexions établies (`RELATED,ESTABLISHED`).
   - Les services essentiels (SSH, HTTP, HTTPS, DNS).
4. On sauvegarde la configuration pour qu'elle persiste après un reboot.



**Résumé :** Ce script met en place un pare-feu sécurisé, autorisant uniquement les services nécessaires. Tu peux ajouter ou modifier les ports selon tes besoins.
