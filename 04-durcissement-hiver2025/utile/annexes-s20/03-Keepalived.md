# **Annexe : Qu’est-ce que Keepalived ?**  

## **Introduction**  

Keepalived est un outil permettant d’assurer la **haute disponibilité** d’un service en garantissant qu’une adresse IP virtuelle (VIP) bascule automatiquement d’un serveur à un autre en cas de panne.  

Il est principalement utilisé dans des infrastructures où plusieurs serveurs doivent travailler ensemble pour assurer une **continuité de service**, même si l’un d’eux tombe en panne.  

Keepalived fonctionne en utilisant un protocole appelé **VRRP (Virtual Router Redundancy Protocol)**, qui permet à plusieurs serveurs de partager une même adresse IP.  

---

## **Pourquoi utiliser Keepalived ?**  

Dans un réseau, il est important qu’un service (site web, base de données, API, etc.) reste toujours disponible. Si un serveur tombe en panne, Keepalived permet :  

1. **D’attribuer dynamiquement une adresse IP virtuelle (VIP)** à un serveur fonctionnel.  
2. **D’assurer un basculement automatique (failover)** vers un autre serveur en cas de panne.  
3. **D’améliorer la tolérance aux pannes** en supprimant les points de défaillance uniques (Single Point of Failure).  

---

## **Comment fonctionne Keepalived ?**  

### **1. L’IP virtuelle (VIP) et le basculement**  

Keepalived attribue une **adresse IP virtuelle (VIP)** qui est toujours associée à un serveur en bon état.  

- Un **serveur principal (MASTER)** détient cette adresse IP et traite les requêtes.  
- Un ou plusieurs **serveurs secondaires (BACKUP)** surveillent l’état du serveur principal.  
- Si le serveur principal tombe en panne, un serveur secondaire prend automatiquement la VIP.  

Ce mécanisme permet d’assurer que les utilisateurs continuent d’accéder aux services, même en cas de panne.  

### **2. Le protocole VRRP**  

Keepalived utilise le **Virtual Router Redundancy Protocol (VRRP)** pour gérer l’attribution de l’adresse IP virtuelle.  

- **Chaque serveur a une priorité.**  
- Le serveur avec la **plus haute priorité** détient la VIP.  
- En cas de défaillance du serveur principal, le serveur avec la **priorité la plus élevée suivante** prend le relais.  

---

## **Exemple de fonctionnement**  

1. Un utilisateur se connecte à une application via l’adresse IP `192.168.1.100`.  
2. Cette adresse IP est gérée par Keepalived et attribuée au serveur `Server1`.  
3. Si `Server1` tombe en panne, Keepalived transfère automatiquement la VIP à `Server2`.  
4. L’utilisateur continue d’accéder au service **sans interruption**, car l’adresse IP reste la même.  

---

## **Exemple de configuration Keepalived**  

Fichier de configuration Keepalived sur le serveur principal (`Server1`) :  
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
Explication :  
- `state MASTER` : Ce serveur est le serveur principal (détient la VIP).  
- `interface eth0` : Interface réseau utilisée.  
- `virtual_router_id 51` : Identifiant VRRP unique pour ce groupe.  
- `priority 100` : Priorité la plus haute (plus la valeur est élevée, plus le serveur est prioritaire).  
- `advert_int 1` : Intervalle de vérification (1 seconde).  
- `virtual_ipaddress { 192.168.1.100 }` : Adresse IP virtuelle partagée.  

Sur le serveur de secours (`Server2`), la configuration est similaire mais avec une **priorité plus faible** :  
```bash
vrrp_instance VI_1 {
   state BACKUP
   interface eth0
   virtual_router_id 51
   priority 90
   advert_int 1
   virtual_ipaddress {
       192.168.1.100
   }
}
```
Explication :  
- `state BACKUP` : Ce serveur est en attente.  
- `priority 90` : Priorité inférieure au MASTER.  
- En cas de panne du serveur MASTER, ce serveur devient automatiquement le nouveau MASTER.  

---

## **Scénarios d’utilisation**  

1. **Haute disponibilité pour un site web**  
   - Un serveur web détient l’IP virtuelle et sert les pages aux visiteurs.  
   - Si ce serveur tombe, un autre serveur web prend la relève.  

2. **Équilibrage de charge avec HAProxy**  
   - Keepalived est utilisé pour fournir une IP virtuelle à HAProxy.  
   - Si le serveur HAProxy principal tombe, un autre serveur HAProxy prend la main.  

3. **Haute disponibilité pour une base de données**  
   - Keepalived est utilisé pour garantir qu’une seule base de données principale est active à tout moment.  

---

## **Conclusion**  

Keepalived est un outil puissant permettant d’assurer la haute disponibilité en cas de panne de serveur. Il est facile à configurer et fonctionne sur la plupart des distributions Linux.  

Grâce à Keepalived et au protocole VRRP, il est possible d’avoir un **basculement automatique** d’un serveur à un autre sans que les utilisateurs s’en aperçoivent.
