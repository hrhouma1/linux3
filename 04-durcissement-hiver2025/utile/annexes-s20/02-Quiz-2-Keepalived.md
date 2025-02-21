# **Quiz : Keepalived et Haute Disponibilité**  

*Ce quiz permet de tester la compréhension des principes de Keepalived, de son protocole VRRP et de son utilisation en haute disponibilité. Il aide à assimiler les concepts clés tout en préparant aux scénarios réels de déploiement et de gestion de basculement automatique.*

## **Partie 1 : Concepts de Base de Keepalived**  

1.1. Quel est l’objectif principal de Keepalived ?  
   a) Répartir les requêtes entre plusieurs serveurs  
   b) Assurer un basculement automatique en cas de panne d’un serveur  
   c) Accélérer le débit d’un réseau  
   d) Remplacer les pare-feu traditionnels  

1.2. Keepalived fonctionne avec quel protocole ?  
   a) HTTP  
   b) VRRP (Virtual Router Redundancy Protocol)  
   c) DNS  
   d) SMTP  

1.3. Dans une architecture Keepalived, qu’est-ce qu’une **VIP (Virtual IP Address)** ?  
   a) Une adresse IP dynamique qui change toutes les secondes  
   b) Une adresse IP partagée entre plusieurs serveurs pour assurer la haute disponibilité  
   c) Une adresse IP uniquement utilisée pour le trafic sortant  
   d) Une adresse IP qui empêche les connexions non sécurisées  

1.4. Dans une configuration Keepalived, que représente l’état **MASTER** ?  
   a) Un serveur en veille qui attend que le serveur principal tombe en panne  
   b) Le serveur principal qui détient l’IP virtuelle et gère le trafic  
   c) Un serveur en mode maintenance  
   d) Un serveur qui a la priorité la plus basse  

1.5. Quel est le rôle du serveur en état **BACKUP** dans Keepalived ?  
   a) Il fonctionne toujours en parallèle avec le serveur principal  
   b) Il prend automatiquement le relais si le serveur principal (MASTER) tombe en panne  
   c) Il bloque les connexions non autorisées  
   d) Il surveille uniquement les journaux système sans intervenir  

---

## **Partie 2 : Configuration et Fonctionnement**  

2.1. Dans une configuration Keepalived, que signifie la directive `priority` ?  
   a) Elle définit la priorité d’un serveur dans la gestion de l’IP virtuelle  
   b) Elle limite le nombre de connexions simultanées  
   c) Elle accélère le basculement entre serveurs  
   d) Elle détermine la vitesse du processeur du serveur  

2.2. Quel est l’effet de cette configuration sur Keepalived ?  
```bash
vrrp_instance VI_1 {
   state MASTER
   interface eth0
   virtual_router_id 51
   priority 120
   virtual_ipaddress {
       192.168.1.100
   }
}
```
   a) Le serveur devient le principal et détient l’adresse IP virtuelle `192.168.1.100`  
   b) Le serveur est en veille et n’utilisera pas l’adresse IP virtuelle  
   c) Keepalived est configuré pour bloquer le trafic réseau  
   d) Cette configuration empêche le basculement en cas de panne  

2.3. Pourquoi est-il important que les serveurs Keepalived utilisent la même valeur `virtual_router_id` ?  
   a) Pour éviter les conflits avec d’autres services réseau  
   b) Pour que les serveurs puissent partager la même adresse IP virtuelle  
   c) Pour garantir que le serveur MASTER ait toujours la plus grande priorité  
   d) Pour empêcher le serveur BACKUP d’accéder à la VIP  

2.4. Dans une configuration Keepalived, que signifie `advert_int` ?  
   a) Il définit le délai en secondes avant qu’un serveur prenne le relais  
   b) Il limite le nombre de paquets envoyés par seconde  
   c) Il configure l’authentification entre les serveurs  
   d) Il bloque les connexions réseau en cas de surcharge  

2.5. Quelle commande permet de voir les journaux Keepalived sur un serveur Linux ?  
   a) `journalctl -u keepalived`  
   b) `iptables -L`  
   c) `netstat -an`  
   d) `ping 192.168.1.100`  

---

## **Partie 3 : Scénarios et Mise en Pratique**  

3.1. Vous avez deux serveurs :  
- `Server1` a une priorité de **100** et est en état `MASTER`.  
- `Server2` a une priorité de **90** et est en état `BACKUP`.  
Que se passe-t-il si `Server1` tombe en panne ?  
   a) `Server2` prend automatiquement l’IP virtuelle et devient `MASTER`  
   b) L’adresse IP virtuelle est supprimée et le service devient inaccessible  
   c) Les utilisateurs doivent changer l’adresse IP pour accéder au service  
   d) Keepalived redémarre automatiquement `Server1`  

3.2. Comment Keepalived détecte-t-il qu’un serveur est en panne ?  
   a) Il analyse l’utilisation du processeur  
   b) Il surveille l’état des connexions réseau  
   c) Il effectue des tests périodiques sur l’interface réseau du serveur principal  
   d) Il bloque le trafic entrant pour vérifier les réponses  

3.3. Un administrateur réseau veut ajouter un troisième serveur en **BACKUP** avec une priorité de **80**. Que se passe-t-il si le serveur `MASTER` et le premier `BACKUP` tombent en panne ?  
   a) Le troisième serveur prend la VIP et devient `MASTER`  
   b) La VIP disparaît et aucun serveur ne prend le relais  
   c) La VIP est transférée au premier serveur BACKUP même s’il est hors ligne  
   d) Keepalived cesse de fonctionner et doit être redémarré manuellement  

3.4. Un administrateur remarque que la VIP ne bascule pas en cas de panne du `MASTER`. Quelle est l’erreur la plus probable ?  
   a) La priorité du serveur `BACKUP` est plus élevée que celle du `MASTER`  
   b) Les serveurs ne partagent pas le même `virtual_router_id`  
   c) `Keepalived` n’est pas installé sur le serveur `BACKUP`  
   d) Le `MASTER` bloque le `BACKUP` avec iptables  

3.5. Quelle solution permettrait de tester la bascule de Keepalived sans réellement éteindre un serveur ?  
   a) Déconnecter temporairement l’interface réseau du `MASTER`  
   b) Redémarrer complètement le serveur `MASTER`  
   c) Ajouter une deuxième VIP dans la configuration Keepalived  
   d) Exécuter `ping` sur l’adresse VIP  

