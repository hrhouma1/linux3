# **Quiz : Filtrage de Paquets, Répartition de la Tâche et Haute Disponibilité**  

*Ce quiz couvre les concepts fondamentaux du **filtrage de paquets**, de la **répartition de la charge** et de la **haute disponibilité** en réseau. Le but est de tester votre compréhension des mécanismes de sécurité et d’optimisation des performances des infrastructures informatiques.*

## **Partie 1 : Filtrage de Paquets**  

### **1. Concepts de base du filtrage de paquets**  

1.1. Qu’est-ce que le filtrage de paquets ?  
   a) Une méthode de stockage des paquets réseau  
   b) Une technique de contrôle du trafic réseau basée sur des règles  
   c) Un protocole de communication entre routeurs  
   d) Une technologie de compression des paquets  

1.2. Quel est l’objectif principal d’un pare-feu de filtrage de paquets ?  
   a) Accélérer la transmission des paquets  
   b) Empêcher toute communication sur le réseau  
   c) Sécuriser le réseau en autorisant ou bloquant certains paquets  
   d) Remplacer les routeurs  

1.3. Un pare-feu qui applique des règles fixes sans tenir compte des connexions en cours est un :  
   a) Pare-feu stateful  
   b) Pare-feu stateless  
   c) Pare-feu dynamique  
   d) Proxy  

### **2. Techniques de filtrage de paquets**  

2.1. Quelle est la différence entre un filtrage stateful et un filtrage stateless ?  
   a) Le filtrage stateful prend en compte l’état des connexions en cours, tandis que le filtrage stateless analyse chaque paquet indépendamment  
   b) Le filtrage stateless est plus sécurisé que le filtrage stateful  
   c) Le filtrage stateful bloque tout le trafic entrant par défaut  
   d) Il n’y a aucune différence entre les deux  

2.2. Quelle est la principale fonctionnalité du Deep Packet Inspection (DPI) ?  
   a) Filtrer les paquets en fonction des adresses IP uniquement  
   b) Inspecter le contenu des paquets pour détecter des signatures spécifiques  
   c) Accélérer la transmission des paquets  
   d) Modifier l’adresse IP source d’un paquet  

2.3. Quelle commande iptables permet d’autoriser uniquement les connexions SSH entrantes ?  
   a) `iptables -A INPUT -p tcp --dport 22 -j ACCEPT`  
   b) `iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT`  
   c) `iptables -A FORWARD -p tcp --dport 22 -j DROP`  
   d) `iptables -A INPUT -p udp --dport 22 -j ACCEPT`  

## **Partie 2 : Répartition de la Tâche et Haute Disponibilité**  

### **3. Principes généraux**  

3.1. Quel est l’objectif principal de la répartition de charge ?  
   a) Réduire la consommation d’énergie des serveurs  
   b) Augmenter le nombre de connexions simultanées en répartissant le trafic entre plusieurs serveurs  
   c) Bloquer les connexions indésirables  
   d) Remplacer les routeurs  

3.2. Un système de haute disponibilité permet de :  
   a) Augmenter la vitesse de connexion des utilisateurs  
   b) Maintenir le service en cas de panne d’un serveur  
   c) Empêcher les utilisateurs d’accéder aux services  
   d) Supprimer les fichiers inutilisés sur un serveur  

3.3. La redondance dans un réseau signifie :  
   a) Avoir plusieurs copies de données ou services disponibles en cas de panne  
   b) Désactiver les services inutilisés pour optimiser la bande passante  
   c) Réduire le nombre de serveurs pour économiser des ressources  
   d) Supprimer les connexions inactives après un certain délai  

### **4. HAProxy et l’équilibrage de charge**  

4.1. Quel est le rôle principal de HAProxy ?  
   a) Convertir les adresses IP privées en adresses publiques  
   b) Répartir le trafic entre plusieurs serveurs pour éviter la surcharge  
   c) Accélérer le débit internet d’un utilisateur  
   d) Bloquer les attaques réseau  

4.2. Quelle méthode de répartition HAProxy utilise-t-il pour envoyer les requêtes de manière équilibrée ?  
   a) Round Robin  
   b) FIFO (First In, First Out)  
   c) NAT (Network Address Translation)  
   d) ICMP Echo Request  

4.3. Dans HAProxy, que signifie la directive `balance roundrobin` ?  
   a) Les requêtes sont envoyées de manière aléatoire  
   b) Chaque serveur reçoit une requête à tour de rôle  
   c) Un seul serveur est utilisé pour toutes les requêtes  
   d) Les requêtes sont envoyées en fonction de la vitesse du serveur  

4.4. Que permet la directive `check` dans une configuration HAProxy ?  
   a) Vérifier si un serveur backend est actif et le retirer en cas de panne  
   b) Configurer les adresses IP virtuelles  
   c) Bloquer certaines adresses IP  
   d) Définir une priorité entre les serveurs  

4.5. Quelle est l’utilité de Keepalived dans un environnement haute disponibilité ?  
   a) Configurer les connexions sans fil  
   b) Assurer la bascule automatique entre plusieurs serveurs en cas de panne  
   c) Bloquer les requêtes non sécurisées  
   d) Modifier dynamiquement les ports des services  

4.6. Dans une configuration Keepalived, que signifie la directive `priority` ?  
   a) Le nombre maximal de connexions autorisées  
   b) La priorité d’un serveur dans la gestion de l’IP virtuelle  
   c) Le temps d’attente avant une connexion  
   d) Le nombre de paquets pouvant être transmis par seconde  

---

## **Partie 3 : Scénarios et mise en pratique**  

5.1. Vous gérez un site web à fort trafic avec trois serveurs web. Le site doit rester disponible même si l’un des serveurs tombe en panne. Quelle solution mettez-vous en place ?  
   a) Un proxy Squid  
   b) Un équilibrage de charge avec HAProxy et Keepalived  
   c) Un filtrage de paquets avec iptables  
   d) Un pare-feu sans règles  

5.2. Un administrateur veut s’assurer que seules les connexions HTTPS sont acceptées sur son serveur web. Quelle configuration est correcte ?  
   a) `iptables -A INPUT -p tcp --dport 443 -j ACCEPT`  
   b) `iptables -A INPUT -p tcp --dport 80 -j DROP`  
   c) `iptables -A INPUT -p tcp --dport 22 -j ACCEPT`  
   d) `iptables -A OUTPUT -p tcp --dport 443 -j DROP`  

5.3. Un administrateur réseau souhaite implémenter un système où une adresse IP virtuelle bascule automatiquement entre deux serveurs en cas de panne. Quel outil est le plus adapté ?  
   a) HAProxy  
   b) Keepalived  
   c) iptables  
   d) pfSense  

5.4. Lorsqu’un serveur tombe en panne dans une configuration HAProxy, que se passe-t-il si `check` est activé dans le backend ?  
   a) Le trafic est redirigé vers un autre serveur disponible  
   b) HAProxy arrête de fonctionner immédiatement  
   c) Tous les serveurs sont mis hors ligne  
   d) Le serveur en panne continue de recevoir du trafic  

5.5. Un site web subit une attaque DDoS et les connexions malveillantes proviennent de plusieurs adresses IP différentes. Quelle solution permettrait de limiter l’impact ?  
   a) Utiliser iptables pour limiter le nombre de connexions par adresse IP  
   b) Supprimer le serveur affecté  
   c) Désactiver temporairement le site web  
   d) Modifier le nom de domaine  


