### **1. La table `mangle` dans `iptables`**
La table `mangle` est une des cinq tables disponibles dans `iptables` (les autres étant `filter`, `nat`, `raw` et `security` - *Voir Annexe 1*). Son rôle est principalement de **modifier les paquets** en transit. Contrairement aux tables `filter` (qui applique des règles de filtrage) et `nat` (qui gère la traduction d'adresses réseau), `mangle` est utilisée pour changer des parties spécifiques des paquets, comme :

- Modifier la **qualité de service (QoS)** avec `TOS` (Type of Service) ou `DSCP` (Differentiated Services Code Point).
- Changer le **TTL** (Time To Live) d’un paquet.
- Marquer des paquets pour un traitement spécial par le système.

#### **Exemple d'utilisation de `mangle`**
Si tu veux marquer tous les paquets entrants sur l'interface `eth0` avec une valeur spécifique :
```bash
iptables -t mangle -A INPUT -i eth0 -j MARK --set-mark 1
```
Cela peut être utile pour la gestion avancée du trafic, comme appliquer des règles de QoS ou rediriger du trafic vers une file d’attente spécifique.

#### **Vider la table `mangle`**
Si tu veux réinitialiser la table `mangle`, utilise :
```bash
iptables -t mangle -F
```

---

### **2. Les chaînes personnalisées dans `iptables`**
Dans `iptables`, une **chaîne** est un ensemble de règles qui s’appliquent aux paquets lorsqu’ils correspondent à un certain critère. Il existe deux types de chaînes :

1. **Les chaînes prédéfinies** (créées automatiquement) :
   - `INPUT` : Trafic entrant vers le système local.
   - `OUTPUT` : Trafic sortant du système local.
   - `FORWARD` : Trafic qui traverse le système (utile pour un routeur).
   - `PREROUTING` et `POSTROUTING` : Utilisées principalement dans la table `nat` et `mangle`.

2. **Les chaînes personnalisées** (créées par l’utilisateur) :
   - Tu peux créer tes propres chaînes pour organiser les règles et simplifier la gestion.
   - Une chaîne personnalisée peut être appelée par une autre chaîne, ce qui permet une gestion plus propre et modulaire.

#### **Exemple : Création et utilisation d'une chaîne personnalisée**
1. **Créer une chaîne personnalisée appelée `LOGGING`** :
   ```bash
   iptables -N LOGGING
   ```
2. **Ajouter une règle dans `INPUT` pour envoyer certains paquets vers cette chaîne** :
   ```bash
   iptables -A INPUT -p tcp --dport 22 -j LOGGING
   ```
3. **Ajouter une règle dans `LOGGING` pour journaliser et ensuite rejeter les paquets** :
   ```bash
   iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IPTables-Dropped: " --log-level 4
   iptables -A LOGGING -j DROP
   ```
   - Ici, on envoie les paquets vers `LOGGING` au lieu de les rejeter directement.
   - La première règle écrit un message dans `/var/log/syslog` avant de bloquer le paquet.
   - La seconde règle `DROP` les paquets après la journalisation.

4. **Supprimer une chaîne personnalisée**
   ```bash
   iptables -X LOGGING
   ```
   - Avant de supprimer une chaîne, il faut d'abord enlever toutes les règles qui y font référence (`iptables -F` avant `iptables -X`).

---

### **Résumé**
- **La table `mangle`** est utilisée pour modifier des propriétés des paquets, comme leur priorité ou leur TTL.
- **Les chaînes personnalisées** permettent de structurer les règles `iptables` en les regroupant pour une meilleure organisation et lisibilité.
- **`iptables -X`** supprime uniquement les chaînes personnalisées vides.



# *Annexe :Les 5 tables disponibles dans `iptables`*
Dans `iptables`, chaque règle appartient à une **table**, qui définit son rôle et ses actions possibles. Il existe **5 tables principales** :

| Table       | Rôle principal |
|-------------|----------------|
| `filter`    | Filtrage des paquets (autorisation ou blocage). |
| `nat`       | Traduction d'adresses réseau (NAT, DNAT, SNAT). |
| `mangle`    | Modification des paquets (QoS, TTL, marquage). |
| `raw`       | Contournement du suivi de connexion (`conntrack`). |
| `security`  | Sécurité avancée via les **SELinux labels**. |

---

### **1. Table `filter` (par défaut)**
La table `filter` est utilisée pour **autoriser ou bloquer** les paquets. C’est la table **par défaut** si aucune autre table n’est spécifiée.

#### **Chaînes disponibles dans `filter`**
- `INPUT` : Gère les paquets entrant sur la machine locale.
- `FORWARD` : Gère les paquets qui traversent la machine (utile pour un routeur).
- `OUTPUT` : Gère les paquets envoyés par la machine.

#### **Exemple d'utilisation**
Bloquer tout trafic entrant sauf SSH (port 22) :
```bash
iptables -P INPUT DROP
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

---

### **2. Table `nat` (Network Address Translation)**
Cette table est utilisée pour la **traduction d’adresses réseau**. Elle est principalement utilisée pour modifier l’adresse IP source ou destination d’un paquet.

#### **Chaînes disponibles dans `nat`**
- `PREROUTING` : Modifie les paquets dès leur arrivée.
- `OUTPUT` : Modifie les paquets générés par la machine.
- `POSTROUTING` : Modifie les paquets juste avant qu’ils quittent la machine.

#### **Exemple d'utilisation**
Rediriger tous les paquets entrant sur le port 80 vers un autre serveur (DNAT) :
```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 192.168.1.100:80
```

---

### **3. Table `mangle` (Modification des paquets)**
La table `mangle` permet de **modifier des propriétés** des paquets, comme le TTL, la priorité, ou d'ajouter des marques pour un traitement spécial.

#### **Chaînes disponibles dans `mangle`**
- `PREROUTING` : Modifie les paquets avant leur routage.
- `INPUT` : Modifie les paquets avant qu’ils atteignent une application locale.
- `FORWARD` : Modifie les paquets en transit.
- `OUTPUT` : Modifie les paquets envoyés par la machine.
- `POSTROUTING` : Modifie les paquets avant qu’ils quittent la machine.

#### **Exemple d'utilisation**
Réduire le TTL des paquets sortants pour éviter leur routage au-delà d’un certain nombre de sauts :
```bash
iptables -t mangle -A POSTROUTING -j TTL --ttl-set 64
```

---

### **4. Table `raw` (Bypass du suivi de connexion)**
La table `raw` est utilisée pour **désactiver le suivi de connexion** (`conntrack`) sur certains paquets afin d'améliorer les performances.

#### **Chaînes disponibles dans `raw`**
- `PREROUTING` : Appliqué avant toute décision de routage.
- `OUTPUT` : Appliqué aux paquets générés localement avant qu’ils soient suivis.

#### **Exemple d'utilisation**
Exclure le trafic SSH du suivi de connexion pour améliorer les performances :
```bash
iptables -t raw -A PREROUTING -p tcp --dport 22 -j NOTRACK
```

---

### **5. Table `security` (Sécurité avancée)**
La table `security` est utilisée pour **appliquer des politiques de sécurité basées sur les labels SELinux**.

#### **Chaînes disponibles dans `security`**
- `INPUT` : Applique des règles de sécurité aux paquets entrants.
- `FORWARD` : Applique des règles aux paquets en transit.
- `OUTPUT` : Applique des règles aux paquets sortants.

#### **Exemple d'utilisation**
Marquer les paquets entrants sur le port 22 avec un label SELinux spécifique :
```bash
iptables -t security -A INPUT -p tcp --dport 22 -j SECMARK --selctx system_u:object_r:ssh_packet_t:s0
```

---

### **Résumé des tables**
| Table       | Utilisation principale |
|-------------|-----------------------|
| `filter`    | Bloquer ou autoriser le trafic. |
| `nat`       | Modifier les adresses IP et les ports des paquets. |
| `mangle`    | Modifier les propriétés des paquets (TTL, QoS, marquage). |
| `raw`       | Désactiver le suivi de connexion (`conntrack`). |
| `security`  | Appliquer des politiques SELinux aux paquets. |


