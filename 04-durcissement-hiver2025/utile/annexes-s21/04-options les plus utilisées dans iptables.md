## Introduction

- *Je vous propose une **table exhaustive** des options les plus utilisées dans `iptables`*



## 📌 **Table des options iptables**

| Option | Signification | Explication |
|---------|--------------|-------------|
| **-A** | **Append (Ajouter une règle)** | Ajoute une règle à la fin d'une chaîne existante (`INPUT`, `OUTPUT`, `FORWARD`, etc.). |
| **-I** | **Insert (Insérer une règle)** | Insère une règle à une position spécifique dans une chaîne (par défaut en première position). |
| **-D** | **Delete (Supprimer une règle)** | Supprime une règle spécifique d’une chaîne (en précisant son index ou son contenu). |
| **-R** | **Replace (Remplacer une règle)** | Remplace une règle existante à un index donné dans une chaîne. |
| **-L** | **List (Lister les règles)** | Affiche les règles existantes d'une chaîne (par défaut `INPUT`, `FORWARD` et `OUTPUT`). |
| **-F** | **Flush (Vider une chaîne ou toutes les règles)** | Supprime toutes les règles d'une chaîne donnée ou de toutes les chaînes. |
| **-X** | **Delete Chain (Supprimer une chaîne personnalisée)** | Supprime une chaîne utilisateur vide (créée manuellement avec `-N`). |
| **-N** | **New Chain (Créer une nouvelle chaîne)** | Crée une chaîne utilisateur personnalisée pour une gestion avancée des règles. |
| **-P** | **Policy (Définir une politique par défaut)** | Définit le comportement par défaut (`ACCEPT` ou `DROP`) d'une chaîne (`INPUT`, `FORWARD`, `OUTPUT`). |
| **-E** | **Rename Chain (Renommer une chaîne)** | Renomme une chaîne utilisateur existante. |

---

## 📌 **Options pour définir les paquets ciblés**
| Option | Signification | Explication |
|---------|--------------|-------------|
| **-p** | **Protocol (Protocole)** | Spécifie le protocole (`tcp`, `udp`, `icmp`, `all`). |
| **-s** | **Source (Adresse source)** | Filtre les paquets provenant d'une adresse IP ou d'un réseau (`-s 192.168.1.0/24`). |
| **-d** | **Destination (Adresse de destination)** | Filtre les paquets à destination d'une IP ou d'un réseau (`-d 10.0.0.1`). |
| **--sport** | **Source Port (Port source)** | Filtre les paquets selon leur port source (`--sport 22`). |
| **--dport** | **Destination Port (Port de destination)** | Filtre les paquets selon leur port de destination (`--dport 80`). |
| **-m** | **Match (Modules complémentaires)** | Active des modules supplémentaires (`connlimit`, `state`, `multiport`, etc.). |
| **-j** | **Jump (Action à exécuter sur le paquet)** | Spécifie l'action (`ACCEPT`, `DROP`, `REJECT`, `LOG`, `RETURN`, etc.). |

---

## 📌 **Modules (Extensions avec `-m`)**
| Module (`-m`) | Description | Exemple |
|--------------|-------------|---------|
| **state** | Vérifie l'état de connexion (`NEW`, `ESTABLISHED`, etc.). | `iptables -A INPUT -m state --state ESTABLISHED -j ACCEPT` |
| **connlimit** | Limite le nombre de connexions simultanées. | `iptables -A INPUT -p tcp --dport 22 -m connlimit --connlimit-above 3 -j DROP` |
| **multiport** | Permet de spécifier plusieurs ports en une seule règle. | `iptables -A INPUT -p tcp -m multiport --dports 22,80,443 -j ACCEPT` |
| **limit** | Limite le taux d'arrivée des paquets (anti-DDoS). | `iptables -A INPUT -p icmp -m limit --limit 1/s -j ACCEPT` |
| **mac** | Filtre selon l’adresse MAC. | `iptables -A INPUT -m mac --mac-source AA:BB:CC:DD:EE:FF -j ACCEPT` |
| **iprange** | Filtre un intervalle d’adresses IP. | `iptables -A INPUT -m iprange --src-range 192.168.1.100-192.168.1.200 -j ACCEPT` |

---

## 📌 **Cibles (`-j`) : Actions appliquées aux paquets**
| Cible (`-j`) | Description | Explication |
|--------------|-------------|-------------|
| **ACCEPT** | Accepte le paquet et l’autorise à passer. | Laisse passer le trafic. |
| **DROP** | Supprime le paquet sans réponse. | Bloque le trafic sans avertir l’émetteur. |
| **REJECT** | Bloque le paquet et envoie une réponse `ICMP` d’erreur. | Bloque avec une notification (`icmp-port-unreachable`). |
| **LOG** | Enregistre le paquet dans les logs du système. | Utilisé pour diagnostiquer les problèmes réseau. |
| **RETURN** | Renvoie le paquet à la chaîne précédente. | Permet de sortir d'une chaîne utilisateur. |

---

## 📌 **Exemples pratiques**

1️⃣ **Bloquer toutes les connexions entrantes sauf SSH et HTTP**  
```bash
iptables -P INPUT DROP
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```

2️⃣ **Limiter le nombre de connexions SSH simultanées à 3**  
```bash
iptables -A INPUT -p tcp --dport 22 -m connlimit --connlimit-above 3 -j DROP
```

3️⃣ **Limiter le nombre de paquets ICMP pour éviter un ping flood**  
```bash
iptables -A INPUT -p icmp -m limit --limit 1/s --limit-burst 5 -j ACCEPT
iptables -A INPUT -p icmp -j DROP
```

---

### 📌 **Résumé**
- `-A`, `-I`, `-D`, `-R` : Ajouter, insérer, supprimer ou remplacer des règles.
- `-L`, `-F`, `-X`, `-N` : Lister, vider, supprimer ou créer des chaînes.
- `-p`, `-s`, `-d`, `--dport`, `--sport` : Filtrer les paquets selon l’IP, le protocole ou le port.
- `-m` : Charger des modules (`state`, `connlimit`, `multiport`, etc.).
- `-j` : Définir l’action (`ACCEPT`, `DROP`, `REJECT`, `LOG`, etc.).

