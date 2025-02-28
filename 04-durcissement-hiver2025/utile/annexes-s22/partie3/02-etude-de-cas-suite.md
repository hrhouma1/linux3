### **Étude de cas 2 : Problème de connexion SSL sur le réseau de l’école**

#### **Problème rencontré**
Le site **ainsighterzone.com** fonctionne normalement sur un réseau mobile (cellulaire), mais ne charge pas correctement sur le réseau de l’école. L’erreur affichée est :

> **"Your connection is not private"**  
> **net::ERR_CERT_AUTHORITY_INVALID**  

Cela signifie que le problème ne vient probablement **pas du certificat SSL lui-même**, mais plutôt du réseau de l’école.

---

## **Analyse du problème et hypothèses possibles**
L’erreur **net::ERR_CERT_AUTHORITY_INVALID** peut être causée par des restrictions du réseau de l’école, un filtrage de contenu, ou un problème de configuration SSL incompatible avec l’infrastructure réseau. Voici les causes possibles et les solutions associées.

---

### **1. Le réseau de l'école bloque certaines autorités de certification**
Certains administrateurs réseau bloquent les sites utilisant des certificats délivrés par certaines **autorités de certification (CA)**, notamment les certificats gratuits comme **Let’s Encrypt**.

**Test** :
- Essayez d’accéder à d’autres sites qui utilisent Let’s Encrypt, par exemple :
  - https://letsencrypt.org/
  - https://valid-isrgrootx1.letsencrypt.org/
- Si ces sites sont également bloqués, cela signifie que l’école ne fait pas confiance à cette autorité.

**Solution** :
- Si le problème vient d’une restriction du réseau, vous pouvez essayer d’utiliser un **certificat SSL d’une autre autorité**, comme **DigiCert** ou **Cloudflare SSL**.
- Demandez à l’administrateur réseau d’ajouter Let's Encrypt à la liste des autorités de confiance.

---

### **2. Le réseau utilise un proxy SSL ou un pare-feu qui intercepte les connexions HTTPS**
Certains établissements scolaires utilisent un **proxy transparent** ou un **pare-feu d’inspection SSL** qui intercepte et modifie les connexions HTTPS. Cela peut provoquer une erreur SSL, car le certificat présenté au navigateur ne correspond pas à celui du site.

**Test** :
- Ouvrez **le détail du certificat** dans votre navigateur :
  1. Cliquez sur l’icône 🔒 du navigateur dans la barre d’adresse.
  2. Vérifiez **l’émetteur du certificat**.
  3. Si ce n’est pas Let’s Encrypt (ou votre autorité de certification), cela signifie que le proxy du réseau modifie la connexion.

**Solution** :
- Si un **proxy SSL** est en place, l’administrateur réseau doit ajouter votre site à la **liste des domaines de confiance**.
- Vous pouvez aussi essayer un **certificat Cloudflare SSL**, qui est mieux supporté par certains pare-feux.

---

### **3. Cache DNS du réseau de l'école**
Le réseau de l’école peut avoir une **version obsolète du DNS**, qui pointe vers une ancienne configuration du site.

**Test** :
- Vérifiez la résolution DNS avec la commande :
  ```sh
  nslookup ainsighterzone.com
  ```
- Comparez les résultats avec ceux obtenus sur un autre réseau (cellulaire, VPN).
- Si les adresses IP sont différentes, cela signifie que le réseau de l’école utilise une version **obsolète** du DNS.

**Solution** :
- Demandez aux administrateurs réseau de **vider leur cache DNS**.
- Essayez d’accéder au site avec une autre connexion Internet.

---

### **4. Problème avec TLS ou la configuration SSL**
Certains réseaux restreignent l’accès aux sites qui n’utilisent pas les **protocoles TLS 1.2 ou 1.3**.

**Test** :
- Vérifiez si votre site supporte bien **TLS 1.2 et 1.3** avec les commandes suivantes :
  ```sh
  openssl s_client -connect ainsighterzone.com:443 -tls1_2
  ```
  ```sh
  openssl s_client -connect ainsighterzone.com:443 -tls1_3
  ```
- Si le test échoue, cela signifie que le réseau refuse votre configuration TLS.

**Solution** :
- Activez **TLS 1.2 et TLS 1.3** dans votre serveur.  
  Sur **Nginx**, ajoutez dans `/etc/nginx/nginx.conf` :
  ```nginx
  ssl_protocols TLSv1.2 TLSv1.3;
  ```
  Puis redémarrez le serveur :
  ```sh
  sudo systemctl restart nginx
  ```

---

### **5. Bloqueur de contenu ou politique de filtrage de l'école**
Certaines écoles bloquent **les nouveaux domaines** ou ceux qui ne sont pas classés comme "sécurisés" par leur pare-feu.

**Test** :
- Essayez d’accéder à **ainsighterzone.com** depuis un autre site de test, comme **https://www.webpagetest.org/**.
- Si le site fonctionne normalement ailleurs, c'est un blocage du réseau.

**Solution** :
- Contactez l’administrateur réseau de l’école pour demander **l’ajout de votre site à la liste blanche**.

---

## **Conclusion**
Si le site fonctionne ailleurs mais **pas sur le réseau de l'école**, les causes probables sont :
1. **Un proxy SSL** qui intercepte la connexion.
2. **Un cache DNS obsolète** du réseau.
3. **Un filtrage du certificat SSL** par l'école.
4. **Une configuration TLS non supportée** par le réseau.
5. **Une politique de blocage stricte** de l’école.

### **Solution rapide**
- **Testez avec un VPN** sur le réseau de l’école.
- **Regardez le certificat SSL** dans votre navigateur pour voir s’il est modifié.
- **Essayez un autre certificat**, comme Cloudflare SSL.
- **Contactez l’administrateur réseau** pour ajouter le site à la liste blanche.

Si le VPN permet d’accéder au site, cela confirme que **le problème vient du réseau de l’école et non de votre configuration SSL**.
