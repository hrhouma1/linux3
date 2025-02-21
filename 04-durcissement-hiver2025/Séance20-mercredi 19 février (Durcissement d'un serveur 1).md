# **🛡️ Mise en place et configuration de iptables et nftables sur Linux**
Ce guide vous aidera à comprendre et configurer **iptables** et **nftables**, les outils essentiels pour la gestion du pare-feu sous Linux.

---

## Table des matières
1. **Introduction à iptables et nftables**
2. **Installation et vérification des outils**
3. **Configuration de base avec iptables**
4. **Sauvegarde et restauration des règles iptables**
5. **Passage de iptables à nftables**
6. **Configuration de base avec nftables**
7. **Comparaison entre iptables et nftables**
8. **Exercices pratiques**
9. **Conclusion**
10. **Annexes et scripts utiles**

---

## **1️⃣ Introduction à iptables et nftables**
### 🔸 **Qu’est-ce qu’iptables ?**
`iptables` est un outil permettant de configurer le **pare-feu** sous Linux en définissant des règles pour accepter, bloquer ou rediriger le trafic réseau.

### 🔸 **Qu’est-ce que nftables ?**
`nftables` est le successeur de `iptables`, introduit pour simplifier la gestion du pare-feu avec une meilleure performance et une syntaxe plus lisible.

### **📌 Différence entre les deux**
| Fonctionnalité | iptables | nftables |
|--------------|---------|---------|
| Complexité | Plus complexe | Plus simple |
| Performance | Moins optimisé | Plus rapide et efficace |
| Syntaxe | Basée sur plusieurs tables et chaînes | Syntaxe plus unifiée |
| Future | Remplacé progressivement | Recommandé pour les nouvelles installations |

---

## **2️⃣ Installation et vérification des outils**
Avant de commencer, vérifions si **iptables** et **nftables** sont installés.

### Vérifier l’installation
```bash
iptables --version
nft --version
```

### Installer iptables et nftables (si nécessaire)
Sur Debian/Ubuntu :
```bash
sudo apt update
sudo apt install iptables nftables -y
```

Sur CentOS/RHEL :
```bash
sudo yum install iptables nftables -y
```

---

## **3️⃣ Configuration de base avec iptables**
### Structure d’iptables
- `INPUT` : Trafic entrant
- `OUTPUT` : Trafic sortant
- `FORWARD` : Trafic traversant la machine

### Exemple de configuration de base
Créez un script `iptables-setup.sh` :

```bash
#!/bin/bash

# Remettre les règles à zéro
iptables -F
iptables -X

# Définir la politique par défaut
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Autoriser les connexions existantes et liées
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Autoriser SSH (port 22)
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Autoriser HTTP/HTTPS (ports 80 et 443)
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Afficher les règles
iptables -L -v
```

### Exécution du script
```bash
chmod +x iptables-setup.sh
./iptables-setup.sh
```

---

## **4️⃣ Sauvegarde et restauration des règles iptables**
### Sauvegarder les règles
```bash
sudo iptables-save > /etc/iptables.rules
```

### Charger les règles au démarrage
Créer un script `iptables-restore.sh` :
```bash
#!/bin/bash
iptables-restore < /etc/iptables.rules
```

Le rendre exécutable :
```bash
chmod +x iptables-restore.sh
```

Pour charger automatiquement les règles au démarrage, ajoutez cette ligne dans `/etc/rc.local` :
```bash
bash /root/iptables-restore.sh
```

---

## **5️⃣ Passage de iptables à nftables**
**Pourquoi migrer ?**
- Meilleure gestion des performances
- Syntaxe plus lisible
- Prise en charge avancée du filtrage

### Convertir des règles iptables en nftables
```bash
iptables-translate -A INPUT -p tcp --dport 22 -j ACCEPT
```
Exemple de sortie :
```nft
nft add rule ip filter INPUT tcp dport 22 accept
```

---

## **6️⃣ Configuration de base avec nftables**
### Activer nftables
```bash
sudo systemctl enable nftables
sudo systemctl start nftables
```

### Exemple de configuration de nftables
Créer un script `nftables-setup.sh` :
```bash
#!/bin/bash

nft flush ruleset

nft add table inet firewall
nft add chain inet firewall input { type filter hook input priority 0 \; }
nft add chain inet firewall forward { type filter hook forward priority 0 \; }
nft add chain inet firewall output { type filter hook output priority 0 \; }

# Règles
nft add rule inet firewall input ct state established,related accept
nft add rule inet firewall input iif lo accept
nft add rule inet firewall input ip protocol icmp accept
nft add rule inet firewall input tcp dport { 22, 80, 443 } accept
nft add rule inet firewall input drop

echo "Configuration nftables appliquée."
```

### Exécuter le script
```bash
chmod +x nftables-setup.sh
./nftables-setup.sh
```

### Sauvegarder la configuration
```bash
sudo nft list ruleset > /etc/nftables.conf
```

### Charger nftables au démarrage
```bash
sudo systemctl enable nftables
```

---

## **7️⃣ Comparaison entre iptables et nftables**
| Fonction | iptables | nftables |
|----------|---------|---------|
| Syntaxe | Complexe | Plus simple |
| Performance | Standard | Optimisée |
| Gestion des règles | Basée sur des chaînes multiples | Unifiée en une seule table |
| Migration facile | Non | Oui (via `iptables-translate`) |

---

## **8️⃣ Exercices pratiques**
1. **Créer et appliquer une règle iptables bloquant l’accès au port 8080.**
2. **Migrer une règle iptables existante vers nftables.**
3. **Créer une configuration nftables bloquant tout sauf le trafic SSH et HTTP.**
4. **Ajouter une règle pour restreindre l’accès SSH à une IP spécifique.**
5. **Mettre en place une règle nftables pour limiter le nombre de connexions simultanées sur un port.**

---

## **9️⃣ Conclusion**
Nous avons exploré :
- **iptables** et sa configuration de base.
- **nftables**, son avantage sur iptables et sa syntaxe simplifiée.
- **Les scripts pour gérer les règles et les restaurer automatiquement.**
- **Une migration en douceur de iptables vers nftables.**

Pour une gestion moderne et efficace, **nftables est recommandé**.

---

## **🔟 Annexes et scripts utiles**
### **Annexe 1 : Suppression des règles iptables**
```bash
iptables -F
iptables -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```

### **Annexe 2 : Suppression des règles nftables**
```bash
nft flush ruleset
```

---

## **📌 Étapes suivantes**
- **Configurer un pare-feu avancé avec nftables** incluant NAT et QoS.
- **Intégrer nftables avec fail2ban** pour bloquer les attaques SSH.
- **Utiliser nftables pour gérer un cluster avec Kubernetes.**


