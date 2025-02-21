## **Annexe : Qu’est-ce que HAProxy ?**  

### **Introduction**  

HAProxy (High Availability Proxy) est un logiciel qui sert d’**équilibreur de charge** et de **proxy**. Il est couramment utilisé pour répartir le trafic réseau entre plusieurs serveurs afin d’améliorer la performance et la disponibilité des services web.  

Il est souvent employé pour gérer le trafic HTTP/HTTPS et TCP, notamment dans des architectures où plusieurs serveurs doivent fonctionner ensemble pour supporter un grand nombre de connexions.  

---

### **Pourquoi utiliser HAProxy ?**  

Dans un environnement informatique, lorsqu’un site web ou une application reçoit beaucoup de visiteurs, un seul serveur peut ne pas suffire pour gérer la charge. HAProxy permet de :  

1. **Répartir la charge** entre plusieurs serveurs pour éviter qu’un seul soit surchargé.  
2. **Assurer la haute disponibilité** en détectant les serveurs en panne et en redirigeant automatiquement le trafic vers ceux qui fonctionnent.  
3. **Sécuriser les connexions** en filtrant le trafic et en ajoutant des fonctionnalités comme le chiffrement SSL/TLS.  

---

### **Comment fonctionne HAProxy ?**  

#### **1. Équilibrage de charge**  

HAProxy reçoit les requêtes des utilisateurs et décide vers quel serveur les envoyer en fonction d’une stratégie définie. Il peut utiliser différentes méthodes :  

- **Round Robin** : Il envoie chaque requête successivement à chaque serveur en liste.  
- **Least Connection** : Il envoie la requête au serveur qui a le moins de connexions actives.  
- **Source IP Hash** : Il redirige toujours un même utilisateur vers le même serveur en fonction de son adresse IP.  

Exemple de fonctionnement :  

1. Un utilisateur visite un site web.  
2. HAProxy intercepte la requête et choisit un serveur web disponible.  
3. La requête est envoyée au serveur sélectionné.  
4. Le serveur traite la demande et envoie la réponse à HAProxy.  
5. HAProxy transmet la réponse à l’utilisateur.  

Ainsi, l’utilisateur ne sait même pas qu’il y a plusieurs serveurs derrière HAProxy, il voit le site fonctionner normalement.  

---

### **2. Détection des serveurs en panne (Health Checks)**  

HAProxy vérifie en permanence si les serveurs fonctionnent bien. Si un serveur tombe en panne, il est retiré automatiquement de la liste des serveurs actifs, et le trafic est redirigé vers les autres.  

Exemple d’un **health check** dans HAProxy :  
```haproxy
backend web_servers
   balance roundrobin
   server web1 192.168.1.10:80 check
   server web2 192.168.1.11:80 check
```
Dans cette configuration :  
- HAProxy vérifie régulièrement si `web1` et `web2` sont toujours disponibles.  
- Si un serveur ne répond plus, HAProxy arrête de lui envoyer du trafic.  

---

### **3. Proxy TCP et HTTP**  

HAProxy peut fonctionner en mode **TCP (transport brut)** ou en mode **HTTP (avec inspection du protocole web)**.  

- **Mode TCP** : Convient pour équilibrer la charge de bases de données, applications en temps réel.  
- **Mode HTTP** : Permet de gérer les en-têtes HTTP, les redirections, et les optimisations spécifiques aux sites web.  

Exemple d’une **configuration HAProxy pour un site web** :  
```haproxy
frontend http_front
   bind *:80
   default_backend web_servers

backend web_servers
   balance roundrobin
   server web1 192.168.1.10:80 check
   server web2 192.168.1.11:80 check
```
Explication :  
- **frontend http_front** : C’est l’interface qui reçoit les connexions des utilisateurs.  
- **bind *:80** : HAProxy écoute sur le port 80 (HTTP).  
- **default_backend web_servers** : Le trafic est redirigé vers les serveurs du backend nommé "web_servers".  
- **backend web_servers** : Liste des serveurs qui recevront les requêtes.  
- **balance roundrobin** : HAProxy utilise la méthode de répartition "round robin".  
- **server web1 192.168.1.10:80 check** : Le premier serveur web.  
- **server web2 192.168.1.11:80 check** : Le second serveur web.  

---

### **Cas d’usage courants de HAProxy**  

1. **Équilibrage de charge pour un site web à fort trafic**  
   - Exemple : Un site e-commerce reçoit des milliers de visiteurs. HAProxy répartit le trafic sur plusieurs serveurs web.  

2. **Haute disponibilité pour une application d’entreprise**  
   - Exemple : Une application critique doit toujours être disponible. HAProxy redirige les connexions vers un serveur en cas de panne.  

3. **Sécurisation et accélération des requêtes web**  
   - HAProxy peut être configuré comme un **reverse proxy** pour optimiser les performances d’un site et ajouter une couche de protection.  

---

### **Conclusion**  

HAProxy est un outil essentiel pour gérer efficacement le trafic réseau, assurer la haute disponibilité et améliorer les performances d’un site ou d’une application. Il est robuste, léger et très utilisé dans les grandes infrastructures, des petits sites web aux plateformes cloud complexes.  

Il permet de garantir que les services restent accessibles même en cas de panne, et optimise la distribution des connexions pour éviter la surcharge des serveurs.

# Références: 
- https://www.linkedin.com/pulse/understanding-difference-between-proxy-reverse-servers-rehouma-jgwde/
