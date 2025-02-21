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
