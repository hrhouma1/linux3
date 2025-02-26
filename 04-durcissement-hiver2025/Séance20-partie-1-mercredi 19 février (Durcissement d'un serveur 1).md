# **Filtrage de Paquets, Répartition de la Tâche et Haute Disponibilité**

---

## **Table des matières**  

1. **Introduction au filtrage de paquets**  
   1.1 Définition  
   1.2 Objectifs et enjeux  
   1.3 Fonctionnement général  

2. **Techniques et mécanismes de filtrage de paquets**  
   2.1 Filtrage statique et dynamique  
   2.2 Filtrage par état (stateful filtering)  
   2.3 Filtrage par inspection approfondie des paquets (DPI)  
   2.4 Filtrage par application  

3. **Répartition de la charge et haute disponibilité**  
   3.1 Définition et concepts  
   3.2 Réplication et redondance  
   3.3 Clustering et agrégation  
   3.4 Mécanismes de relève et de basculement  

4. **Études de cas et exemples d’implémentation**  
   4.1 Configuration d’un firewall avec iptables  
   4.2 Mise en place d’un proxy avec HAProxy  
   4.3 Implémentation de la redondance avec Keepalived  
   4.4 Analyse et test de basculement  

---

## **1. Introduction au filtrage de paquets**  

### **1.1 Définition**  

Le filtrage de paquets est une technique qui permet de contrôler le trafic réseau en appliquant des règles à chaque paquet circulant entre différentes machines. Ces règles sont définies en fonction de plusieurs critères, tels que l’adresse IP source ou destination, le port utilisé, le protocole, ou encore le contenu du paquet.  

### **1.2 Objectifs et enjeux**  

Le filtrage de paquets vise principalement à :  

- Sécuriser un réseau en empêchant l’entrée de paquets malveillants ou non autorisés.  
- Optimiser les performances en limitant le trafic inutile.  
- Appliquer des politiques d’accès et de restriction pour les utilisateurs.  
- Détecter et empêcher certains types d’attaques, comme les scans de ports ou les tentatives d’intrusion.  

### **1.3 Fonctionnement général**  

Chaque paquet entrant ou sortant passe par un ensemble de règles définies sur un pare-feu ou un routeur. Ces règles spécifient si le paquet doit être autorisé, rejeté ou enregistré pour une analyse ultérieure.  

---

## **2. Techniques et mécanismes de filtrage de paquets**  

### **2.1 Filtrage statique et dynamique**  

- **Filtrage statique** : Les règles sont définies à l’avance et appliquées sans tenir compte de l’état des connexions. Exemples : règles iptables simples, ACL sur un routeur.  
- **Filtrage dynamique (stateful)** : Le pare-feu garde une trace des connexions en cours et applique les règles en tenant compte du contexte. Exemples : Netfilter sur Linux, pfSense.  

### **2.2 Filtrage par état (Stateful Filtering)**  

Le filtrage par état analyse les paquets en fonction de leur état dans une connexion. Trois états principaux existent :  

- **NOUVEAU** : Premier paquet d’une connexion.  
- **ÉTABLI** : Connexion active et déjà validée.  
- **LIÉ** : Connexion associée à une autre (exemple : une connexion FTP passive liée à une connexion principale).  

Exemple de règle avec iptables :  
```bash
iptables -A INPUT -m state --state NEW,ESTABLISHED -p tcp --dport 22 -j ACCEPT
```
Cette règle autorise les connexions SSH entrantes si elles sont nouvelles ou déjà établies.  

### **2.3 Filtrage par inspection approfondie des paquets (DPI)**  

Le Deep Packet Inspection (DPI) permet d’examiner le contenu des paquets pour détecter des signatures spécifiques (virus, protocoles interdits, tentatives d’intrusion). Outils courants : Snort, Suricata.  

### **2.4 Filtrage par application**  

Plutôt que de filtrer uniquement en fonction des adresses et ports, ce filtrage fonctionne au niveau des applications. Il peut bloquer certains services comme Facebook ou interdire les connexions à des sites web spécifiques. Exemple : proxy Squid, firewall Palo Alto.  

---

## **3. Répartition de la charge et haute disponibilité**  

### **3.1 Définition et concepts**  

La répartition de charge et la haute disponibilité visent à assurer le bon fonctionnement des services informatiques en répartissant le trafic entre plusieurs machines et en prévoyant des solutions en cas de panne.  

### **3.2 Réplication et redondance**  

- **Réplication** : Copier les données et services sur plusieurs serveurs pour garantir leur accessibilité en cas de panne.  
- **Redondance** : Prévoir un serveur de secours qui prendra le relais en cas de défaillance du principal.  

Exemple : Mise en place d’un proxy web en redondance avec deux serveurs Squid et Keepalived.  

### **3.3 Clustering et agrégation**  

Un cluster regroupe plusieurs machines qui fonctionnent ensemble comme un seul système. Avantages :  
- Équilibrage de charge  
- Résilience aux pannes  
- Amélioration des performances  

Exemple : Un cluster HAProxy répartit les connexions HTTP sur plusieurs serveurs backend.  

### **3.4 Mécanismes de relève et de basculement**  

Le basculement permet qu’un système secondaire prenne automatiquement le relais en cas de défaillance du primaire. Techniques utilisées :  
- **VRRP avec Keepalived** pour attribuer une adresse IP virtuelle  
- **Heartbeat et Pacemaker** pour gérer des services critiques  

---

## **4. Études de cas et exemples d’implémentation**  

### **4.1 Configuration d’un firewall avec iptables**  

Exemple de règles de base pour un pare-feu Linux :  

```bash
iptables -P INPUT DROP
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
```
Cette configuration :  
- Bloque tout par défaut  
- Autorise SSH (port 22)  
- Autorise HTTP (port 80)  
- Accepte les connexions déjà établies  

### **4.2 Mise en place d’un proxy avec HAProxy**  

Fichier de configuration HAProxy pour équilibrer la charge entre deux serveurs web :  

```haproxy
frontend http_front
   bind *:80
   default_backend web_servers

backend web_servers
   balance roundrobin
   server web1 192.168.1.10:80 check
   server web2 192.168.1.11:80 check
```
Ce système distribue les connexions entrantes de manière équilibrée sur deux serveurs web.  

### **4.3 Implémentation de la redondance avec Keepalived**  

Fichier de configuration Keepalived pour assurer une IP virtuelle partagée :  

```bash
vrrp_instance VI_1 {
   state MASTER
   interface eth0
   virtual_router_id 51
   priority 100
   advert_int 1
   virtual_ipaddress {
       192.168.1.100
   }
}
```
Si le serveur principal tombe, le serveur secondaire prendra automatiquement l’adresse IP virtuelle.  

### **4.4 Analyse et test de basculement**  

Pour vérifier si la haute disponibilité fonctionne, on peut :  
- Désactiver manuellement le serveur principal et voir si le secondaire prend le relais.  
- Utiliser la commande `ip a` pour voir quelle machine possède l’IP virtuelle.  
- Analyser les journaux de Keepalived (`journalctl -u keepalived`).  

---

## **Conclusion**  

Le filtrage de paquets et la haute disponibilité sont des éléments fondamentaux pour garantir la sécurité et la continuité des services. Un bon pare-feu, combiné à une infrastructure redondante, permet de minimiser les risques de panne et d’améliorer la performance globale d’un réseau.


---
# Annexe 1 - filtrage des paquets - (version vulgarisée avec l'analogie de l'aéroport)
---






# ✈️ **Filtrage de Paquets, Répartition de la Tâche et Haute Disponibilité - Version Aéroport**

## **1. Introduction : L’aéroport et le contrôle des passagers**

Imaginez un aéroport. Chaque jour, des milliers de passagers entrent et sortent, transportant des bagages. Mais pour éviter les dangers et garantir un bon fonctionnement, l’aéroport applique un **filtrage rigoureux** des entrées et sorties.

Dans un réseau informatique, c’est la même chose ! Chaque "passager" représente un **paquet de données**, et les pare-feux (firewalls) jouent le rôle de la **sécurité aéroportuaire**.

## **2. Les Contrôles de Sécurité (Filtrage de Paquets)**  

Dans un aéroport, il y a plusieurs niveaux de filtrage :  

- **Filtrage statique (simple contrôle d’identité) 🆔**  
  - Un agent regarde si un passager a bien son passeport et un billet.  
  - En informatique, cela revient à bloquer ou autoriser un paquet en fonction de l’adresse IP et du port.  

- **Filtrage dynamique (contrôle des allées et venues) 🔄**  
  - Si un passager est déjà passé par le premier contrôle et revient 10 minutes plus tard avec le même billet, on sait qu’il est déjà vérifié.  
  - C'est comme un pare-feu qui reconnaît une connexion déjà existante et la laisse passer.  

- **Inspection approfondie des bagages (Deep Packet Inspection - DPI) 🎒🔍**  
  - Ici, la sécurité scanne non seulement le passeport, mais aussi ce qu’il y a dans le sac (interdiction des briquets, liquides, etc.).  
  - En informatique, cela revient à analyser en détail le contenu des paquets pour détecter des virus ou du trafic suspect.  

- **Contrôle par application (Passe VIP) 🌟**  
  - Certains passagers ont un accès spécial (pilotes, diplomates).  
  - En informatique, certaines applications sont autorisées alors que d’autres sont bloquées (exemple : bloquer YouTube sur un réseau d’entreprise).  

## **3. Répartition de la Charge (Gestion des Files d’Attente dans l’Aéroport) 🏃‍♂️🏃‍♀️**  

Imaginez qu’un seul agent doit contrôler 10 000 passagers... l’aéroport exploserait ! Pour éviter cela, il répartit la charge :  

- **Plusieurs files d’attente avec plusieurs agents** 👮‍♂️👮‍♀️  
  - Comme dans un réseau, le trafic est réparti sur plusieurs serveurs pour éviter la surcharge.  
  - Un système comme **HAProxy** joue ce rôle en envoyant les passagers vers différents comptoirs de contrôle.  

- **Couloir rapide pour les VIPs (Priorisation du trafic) 🚀**  
  - Certains passagers prioritaires (ex : personnel de bord) ont une file rapide.  
  - Sur un réseau, cela signifie que certains types de trafic (exemple : appels d’urgence) sont priorisés.  

## **4. Haute Disponibilité (Prévoir les Pannes pour Garder l’Aéroport Ouvert) 🚨**  

Un aéroport ne peut pas se permettre de fermer à cause d’un problème technique. Il met donc en place des **plans de secours** :  

- **Deux tours de contrôle en cas de panne** 🏢➡️🏢  
  - Si une tour a un problème, l’autre prend le relais.  
  - Sur un réseau, un outil comme **Keepalived** attribue une adresse IP virtuelle à un serveur de secours en cas de panne.  

- **Générateurs d’urgence** ⚡🔄  
  - En cas de coupure de courant, un générateur prend le relais.  
  - Sur un réseau, cela correspond aux **backups et redondances des serveurs**.  

- **Basculement automatique (Failover) 🔄**  
  - Si un tapis roulant tombe en panne, les passagers sont automatiquement redirigés vers un autre.  
  - En réseau, une machine secondaire prend immédiatement le relais si la principale échoue.  

## **5. Exemple Pratique : Mise en Place d’un Aéroport Sécurisé (Firewall et Répartition de Charge)**  

### **5.1 Contrôle de Sécurité de Base (iptables – Filtrage de paquets)**  

```bash
iptables -P INPUT DROP
iptables -A INPUT -p tcp --dport 22 -j ACCEPT  # Autoriser SSH
iptables -A INPUT -p tcp --dport 80 -j ACCEPT  # Autoriser HTTP
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT  # Connexions établies OK
```
👉 **Tout est bloqué par défaut sauf les connexions nécessaires** (comme refuser l’entrée aux passagers sans billet).  

### **5.2 Répartition de la Charge (HAProxy – Gestion des files d’attente)**  

```haproxy
frontend http_front
   bind *:80
   default_backend web_servers

backend web_servers
   balance roundrobin
   server web1 192.168.1.10:80 check
   server web2 192.168.1.11:80 check
```
👉 **Les passagers sont envoyés alternativement vers deux contrôles de sécurité pour éviter la surcharge.**  

### **5.3 Haute Disponibilité (Keepalived – Prévoir les Pannes)**  

```bash
vrrp_instance VI_1 {
   state MASTER
   interface eth0
   virtual_router_id 51
   priority 100
   advert_int 1
   virtual_ipaddress {
       192.168.1.100
   }
}
```
👉 **Si un serveur tombe en panne, un autre prend immédiatement sa place !**  

---

## **Conclusion**  

Un **réseau bien sécurisé et optimisé fonctionne comme un aéroport** :  

✔️ **Filtrage efficace** pour empêcher les menaces d’entrer.  
✔️ **Répartition de la charge** pour éviter la saturation.  
✔️ **Haute disponibilité** pour assurer un service sans interruption.  

Ainsi, en combinant un bon pare-feu, une répartition intelligente du trafic et des solutions de secours, on garantit un **réseau rapide, sécurisé et fiable** ! 🚀


