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



---
# Annexe 1 : Commandes et explications détaillées
---

## **🔹 Commandes pour iptables**
```bash
iptables -F  # Efface toutes les règles de filtrage actuelles (Flush)
iptables -X  # Supprime toutes les chaînes utilisateur définies
iptables -P INPUT DROP  # Définit la politique par défaut de la chaîne INPUT sur DROP (bloquer tout)
iptables -P FORWARD DROP  # Bloque tout le trafic transitant par la machine
iptables -P OUTPUT ACCEPT  # Autorise toutes les connexions sortantes

iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT  # Accepte les connexions établies ou liées
iptables -A INPUT -p tcp --dport 22 -j ACCEPT  # Autorise SSH (port 22)
iptables -A INPUT -p tcp --dport 80 -j ACCEPT  # Autorise HTTP (port 80)
iptables -A INPUT -p tcp --dport 443 -j ACCEPT  # Autorise HTTPS (port 443)

iptables -L -v  # Affiche les règles actuelles avec des détails
iptables-save > /etc/iptables.rules  # Sauvegarde les règles iptables
iptables-restore < /etc/iptables.rules  # Restaure les règles sauvegardées

iptables-translate -A INPUT -p tcp --dport 22 -j ACCEPT  # Convertit une règle iptables en syntaxe nftables
```

---

## **🔹 Commandes pour nftables**
```bash
nft flush ruleset  # Supprime toutes les règles nftables actuelles

nft add table inet firewall  # Crée une table firewall pour ipv4 et ipv6
nft add chain inet firewall input { type filter hook input priority 0 \; }  # Crée une chaîne pour gérer le trafic entrant
nft add chain inet firewall forward { type filter hook forward priority 0 \; }  # Chaîne pour le trafic en transit
nft add chain inet firewall output { type filter hook output priority 0 \; }  # Chaîne pour le trafic sortant

nft add rule inet firewall input ct state established,related accept  # Accepte les connexions établies
nft add rule inet firewall input iif lo accept  # Autorise tout le trafic sur l'interface locale (loopback)
nft add rule inet firewall input ip protocol icmp accept  # Autorise les requêtes ICMP (ping)
nft add rule inet firewall input tcp dport { 22, 80, 443 } accept  # Autorise les connexions sur SSH, HTTP et HTTPS
nft add rule inet firewall input drop  # Bloque tout autre trafic entrant

nft list ruleset  # Affiche toutes les règles actuelles
nft list table inet firewall  # Affiche les règles de la table firewall

sudo systemctl enable nftables  # Active nftables au démarrage
sudo systemctl start nftables  # Démarre le service nftables

nft list ruleset > /etc/nftables.conf  # Sauvegarde les règles nftables
```

---

## **🔹 Commandes générales et outils**
```bash
iptables --version  # Vérifie la version d’iptables installée
nft --version  # Vérifie la version de nftables installée

sudo apt update  # Met à jour la liste des paquets sous Debian/Ubuntu
sudo apt install iptables nftables -y  # Installe iptables et nftables sous Debian/Ubuntu

sudo yum install iptables nftables -y  # Installe iptables et nftables sous CentOS/RHEL

sudo systemctl enable nftables  # Active nftables pour qu’il démarre automatiquement au boot
sudo systemctl restart nftables  # Redémarre le service nftables après modification des règles

sudo iptables-save > /etc/iptables/rules.v4  # Sauvegarde les règles iptables (méthode persistante pour Debian)
sudo iptables-restore < /etc/iptables/rules.v4  # Restaure les règles iptables sauvegardées
```

---

## **🔹 Commandes pour suppression des règles**
```bash
iptables -F  # Supprime toutes les règles iptables
iptables -X  # Supprime toutes les chaînes personnalisées
iptables -P INPUT ACCEPT  # Modifie la politique par défaut pour accepter tout le trafic entrant
iptables -P FORWARD ACCEPT  # Modifie la politique par défaut pour autoriser tout le trafic transitant
iptables -P OUTPUT ACCEPT  # Modifie la politique par défaut pour accepter tout le trafic sortant

nft flush ruleset  # Supprime toutes les règles dans nftables
```

---

## **🔹 Commandes avancées**
```bash
iptables -A INPUT -p tcp --dport 22 -s 192.168.1.100 -j ACCEPT  # Autorise SSH uniquement depuis 192.168.1.100
iptables -A INPUT -p tcp --dport 22 -m limit --limit 3/min -j ACCEPT  # Limite SSH à 3 connexions par minute

nft add rule inet firewall input ip saddr 192.168.1.100 tcp dport 22 accept  # Autorise SSH depuis une IP spécifique
nft add rule inet firewall input ip saddr 0.0.0.0/0 tcp dport 22 limit rate 3/minute accept  # Limite les connexions SSH
```

---

## **📌 Résumé**
Ce tableau regroupe les principales commandes et leur utilité :

| Commande | Description |
|----------|------------|
| `iptables -F` | Supprime toutes les règles actuelles |
| `iptables -P INPUT DROP` | Bloque tout le trafic entrant par défaut |
| `iptables -A INPUT -p tcp --dport 22 -j ACCEPT` | Autorise le trafic SSH |
| `iptables-save > /etc/iptables.rules` | Sauvegarde les règles iptables |
| `iptables-restore < /etc/iptables.rules` | Restaure les règles iptables |
| `nft flush ruleset` | Supprime toutes les règles nftables |
| `nft add table inet firewall` | Crée une table de pare-feu |
| `nft add chain inet firewall input { type filter hook input priority 0 \; }` | Crée une chaîne de filtrage pour le trafic entrant |
| `nft add rule inet firewall input tcp dport {22, 80, 443} accept` | Autorise SSH, HTTP et HTTPS |
| `nft list ruleset` | Affiche les règles nftables |
| `sudo systemctl enable nftables` | Active nftables au démarrage |
| `iptables-translate -A INPUT -p tcp --dport 22 -j ACCEPT` | Convertit une règle iptables en syntaxe nftables |

---

## **🚀 Étapes suivantes**
- **Expérimenter avec ces commandes** sur un serveur de test.
- **Créer des règles avancées** pour sécuriser un réseau.
- **Configurer nftables pour la gestion des flux NAT** et la mise en place d’un firewall avancé.


---
# Annexe 2 (optionnel) : Commandes avancées pour iptables et nftables**
---

## **🔹 Forwarding de ports avec iptables**
Le **port forwarding** permet de rediriger le trafic d'un port vers une autre destination.

```bash
iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination 192.168.1.10:80  
# Redirige les connexions entrantes sur le port 8080 de la machine vers l’IP 192.168.1.10 sur le port 80

iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.10 --dport 80 -j MASQUERADE  
# Change l'adresse source pour que la machine cible voie l'IP du routeur
```

✅ **Explication** :
- `-t nat` → Utilise la table NAT.
- `PREROUTING` → Intercepte les paquets avant qu'ils ne soient routés.
- `DNAT` → Change l'adresse de destination.
- `MASQUERADE` → Cache l'adresse IP source.

### **🔹 Forwarding de ports avec nftables**
```bash
nft add table nat
nft add chain nat prerouting { type nat hook prerouting priority 0 \; }
nft add rule nat prerouting tcp dport 8080 dnat to 192.168.1.10:80
```

✅ **Explication** :
- `nat` → Crée une table pour la translation d'adresses réseau.
- `prerouting` → Applique les modifications aux paquets entrants.
- `dnat` → Change l'adresse de destination.

---

## **🔹 Détection et prévention des attaques Man-in-the-Middle (MITM)**
Les attaques **MITM** impliquent un attaquant interposant sa machine entre un client et un serveur. Voici comment les détecter et les prévenir.

### **1️⃣ Vérifier les attaques ARP Spoofing**
```bash
arp -a  # Liste les adresses MAC et IP du réseau
watch -n 1 "arp -a"  # Surveille les changements en direct
```

**Détection avancée :**
```bash
ip -s -s neigh flush all  # Rafraîchir la table ARP pour détecter les changements suspects
```

### **2️⃣ Bloquer les paquets ARP suspects avec iptables**
```bash
iptables -A INPUT -p arp --opcode Reply -m mac ! --mac-source XX:XX:XX:XX:XX:XX -j DROP
# Bloque les paquets ARP qui ne correspondent pas à l'adresse MAC attendue
```

### **3️⃣ Désactiver la redirection des paquets pour éviter le MITM**
```bash
echo 0 > /proc/sys/net/ipv4/ip_forward  # Désactive le routage des paquets
```

Pour appliquer de manière permanente, modifiez :
```bash
sudo nano /etc/sysctl.conf
```
Ajoutez cette ligne :
```
net.ipv4.ip_forward=0
```
Appliquez les changements :
```bash
sudo sysctl -p
```

---

## **🔹 Protection contre le SYN Flooding (attaque DDoS)**
Le **SYN Flood** est une attaque où un attaquant envoie des milliers de requêtes TCP sans les compléter, saturant le serveur.

### **1️⃣ Activer la protection SYN Flood avec iptables**
```bash
iptables -A INPUT -p tcp --syn -m limit --limit 1/s --limit-burst 3 -j ACCEPT  
# Limite les connexions SYN à 1 par seconde avec un burst initial de 3
```

### **2️⃣ Protection contre SYN Flood avec nftables**
```bash
nft add rule inet firewall input tcp flags syn limit rate 1/second accept
```

---

## **🔹 Protection contre le Ping of Death**
Le **Ping of Death** est une attaque qui envoie des paquets ICMP trop grands.

### **Bloquer les pings excessifs avec iptables**
```bash
iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s --limit-burst 2 -j ACCEPT  
# Limite les pings à 1 par seconde avec un burst de 2
iptables -A INPUT -p icmp --icmp-type echo-request -j DROP  
# Bloque les pings excessifs
```

### **Bloquer les pings excessifs avec nftables**
```bash
nft add rule inet firewall input icmp type echo-request limit rate 1/second accept
```

---

## **🔹 Protection contre le Scan de Ports**
Les attaquants utilisent souvent des **scanners de ports** comme **nmap** pour identifier les services ouverts.

### **1️⃣ Bloquer le scan avec iptables**
```bash
iptables -N SCAN_BLOCK  
iptables -A SCAN_BLOCK -j LOG --log-prefix "Scan de ports détecté: "  
iptables -A SCAN_BLOCK -j DROP  
iptables -A INPUT -p tcp --tcp-flags ALL FIN,URG,PSH -j SCAN_BLOCK  
iptables -A INPUT -p tcp --tcp-flags ALL NONE -j SCAN_BLOCK  
iptables -A INPUT -p tcp --tcp-flags SYN,RST SYN,RST -j SCAN_BLOCK  
iptables -A INPUT -p tcp --tcp-flags SYN,FIN SYN,FIN -j SCAN_BLOCK  
```

### **2️⃣ Bloquer le scan avec nftables**
```bash
nft add rule inet firewall input tcp flags fin urg psh drop
nft add rule inet firewall input tcp flags syn,fin drop
nft add rule inet firewall input tcp flags syn,rst drop
```

---

## **🔹 Désactivation de certains services dangereux**
Certains services peuvent être exploités par des attaquants.

### **1️⃣ Désactiver IPv6 si non utilisé**
```bash
echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf
sudo sysctl -p
```

### **2️⃣ Désactiver les paquets ICMP Redirect**
```bash
echo "net.ipv4.conf.all.accept_redirects = 0" >> /etc/sysctl.conf
echo "net.ipv4.conf.all.send_redirects = 0" >> /etc/sysctl.conf
sudo sysctl -p
```

---

## **📌 Résumé des commandes avancées**

| Fonction | Commande |
|----------|---------|
| **Forward de ports avec iptables** | `iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination 192.168.1.10:80` |
| **Forward de ports avec nftables** | `nft add rule nat prerouting tcp dport 8080 dnat to 192.168.1.10:80` |
| **Désactiver le forwarding (MITM)** | `echo 0 > /proc/sys/net/ipv4/ip_forward` |
| **Protection contre le SYN Flood** | `iptables -A INPUT -p tcp --syn -m limit --limit 1/s --limit-burst 3 -j ACCEPT` |
| **Bloquer les scans de ports** | `iptables -A INPUT -p tcp --tcp-flags ALL FIN,URG,PSH -j DROP` |
| **Limiter les pings** | `iptables -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s --limit-burst 2 -j ACCEPT` |
| **Désactiver IPv6** | `echo "net.ipv6.conf.all.disable_ipv6 = 1" >> /etc/sysctl.conf && sudo sysctl -p` |
| **Bloquer les ICMP Redirects** | `echo "net.ipv4.conf.all.accept_redirects = 0" >> /etc/sysctl.conf && sudo sysctl -p` |

---

## **🚀 Étapes suivantes**
- Tester ces configurations sur un serveur isolé.
- Simuler des attaques pour vérifier l’efficacité des règles.
- Automatiser la protection avec des scripts.

