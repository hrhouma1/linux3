## Étude de cas : exemple d'un site non sécurisé 

# **Étude de cas : Comment rendre un site sécurisé et optimiser sa redirection HTTPS**

![image](https://github.com/user-attachments/assets/03b7a165-c3e5-4e8a-ba7c-199937284a37)

![image](https://github.com/user-attachments/assets/772267b1-c9ff-41b3-9e76-9ebb62f79b4e)


- Pour sécuriser **https://ai-insighter.ca** et résoudre le problème de "Non sécurisé", vous devez configurer correctement un certificat SSL/TLS valide pour ce domaine. 
- Voici les étapes détaillées pour sécuriser ce site.

---

# **Étape 1 : Identifier la cause de "Non sécurisé"**
1. **Certificat SSL manquant ou mal configuré** :
   - Le domaine n'a pas de certificat SSL valide ou celui configuré ne couvre pas `ai-insighter.ca`.
   
2. **Certificat SSL expiré** :
   - Si un certificat existant est expiré, les navigateurs ne le reconnaissent plus comme valide.

3. **Contenu mixte** :
   - Si des ressources (images, scripts, CSS) sont chargées en HTTP au lieu de HTTPS, cela peut déclencher l'avertissement "Non sécurisé".

4. **Proxy Cloudflare** :
   - Si Cloudflare est activé mais mal configuré, cela peut également causer le problème.

---

# **Étape 2 : Étapes pour sécuriser le domaine**
Voici comment configurer un certificat SSL/TLS pour résoudre ce problème :

---

# **Option 1 : Utiliser `bncert-tool` pour un certificat Let's Encrypt (Bitnami)**

1. **Exécutez `bncert-tool` sur votre serveur :**
   ```bash
   sudo /opt/bitnami/bncert-tool
   ```

2. **Ajoutez tous les domaines** :
   - Lors de la configuration, entrez :
     ```
     ai-insighter.ca www.ai-insighter.ca
     ```

3. **Validez les étapes :**
   - Acceptez d'activer les redirections HTTP → HTTPS.
   - Configurez les options comme HSTS (recommandé pour renforcer la sécurité).

4. **Tester le certificat** :
   - Une fois la configuration terminée, testez en visitant **https://ai-insighter.ca**.

---

# **Option 2 : Utiliser un certificat Cloudflare Origin**

Si vous utilisez **Cloudflare**, voici les étapes pour sécuriser le domaine avec un certificat Cloudflare Origin.

1. **Générer un certificat Origin depuis Cloudflare** :
   - Connectez-vous à Cloudflare.
   - Allez dans **SSL/TLS > Origin Server**.
   - Cliquez sur **Create Certificate**.
   - Incluez `ai-insighter.ca` et `www.ai-insighter.ca`.

2. **Téléchargez le certificat et la clé privée** :
   - Placez ces fichiers dans un dossier sécurisé, par exemple :
     ```bash
     sudo mkdir -p /etc/ssl/cloudflare
     sudo cp origin.pem /etc/ssl/cloudflare/server.crt
     sudo cp origin.key /etc/ssl/cloudflare/server.key
     ```

3. **Configurer Apache pour utiliser ces certificats** :
   - Modifiez le fichier `bitnami.conf` :
     ```apache
     SSLCertificateFile "/etc/ssl/cloudflare/server.crt"
     SSLCertificateKeyFile "/etc/ssl/cloudflare/server.key"
     ```

4. **Redémarrez Apache** :
   ```bash
   sudo /opt/bitnami/ctlscript.sh restart apache
   ```

---

# **Option 3 : Utiliser Certbot pour Let's Encrypt**

1. **Installez Certbot** :
   ```bash
   sudo apt-get update
   sudo apt-get install certbot
   ```

2. **Obtenez un certificat pour le domaine** :
   ```bash
   sudo certbot certonly --apache -d ai-insighter.ca -d www.ai-insighter.ca
   ```

3. **Configurer Apache pour utiliser les certificats** :
   - Modifiez `bitnami.conf` pour pointer vers les certificats générés :
     ```apache
     SSLCertificateFile "/etc/letsencrypt/live/ai-insighter.ca/fullchain.pem"
     SSLCertificateKeyFile "/etc/letsencrypt/live/ai-insighter.ca/privkey.pem"
     ```

4. **Redémarrez Apache** :
   ```bash
   sudo /opt/bitnami/ctlscript.sh restart apache
   ```

---

## **Étape 3 : Vérifiez la redirection HTTPS**
Assurez-vous que toutes les requêtes HTTP redirigent automatiquement vers HTTPS. Vous pouvez ajouter cette configuration dans votre fichier `bitnami.conf` :

```apache
<VirtualHost *:80>
    ServerName ai-insighter.ca
    ServerAlias www.ai-insighter.ca
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/(.*)$ https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>
```

Redémarrez Apache après cette modification :
```bash
sudo /opt/bitnami/ctlscript.sh restart apache
```

---

## **Étape 4 : Vérifiez et corrigez le contenu mixte**
Si votre site charge des ressources via HTTP, modifiez leur URL pour les servir en HTTPS. Utilisez un outil comme [Why No Padlock?](https://www.whynopadlock.com/) pour identifier les ressources non sécurisées.

---

## **Étape 5 : Tester le certificat et la sécurité**
1. Accédez à **https://ai-insighter.ca** dans un navigateur.
2. Utilisez [SSL Labs](https://www.ssllabs.com/ssltest/) pour analyser votre configuration SSL/TLS.
3. Vérifiez que votre site affiche un cadenas sécurisé.

---

## **Résumé des solutions**
| **Solution**                     | **Avantages**                                           | **Inconvénients**                                       |
|-----------------------------------|-------------------------------------------------------|-------------------------------------------------------|
| **bncert-tool (Let's Encrypt)**   | Automatisé, intégré avec Bitnami.                     | Nécessite de désactiver temporairement Cloudflare.    |
| **Cloudflare Origin Certificate** | Compatible avec Cloudflare en mode proxy (nuage orange). | Ne fonctionne qu'avec Cloudflare activé.             |
| **Certbot (Let's Encrypt)**       | Flexible, compatible avec tous les serveurs.          | Configuration manuelle requise.                      |

---

## **Recommandation**
- Si vous utilisez **Cloudflare** : Optez pour un **Cloudflare Origin Certificate**.
- Si vous voulez une gestion automatique avec Let's Encrypt : Essayez **bncert-tool** ou **Certbot**.


----------------------
# Annexe :
----------------------



Si vous souhaitez **rediriger votre domaine `https://ai-insighter.ca`** vers un autre domaine (par exemple, `https://ai-insighter.com`), voici une méthode simple et efficace. La redirection est une bonne alternative si vous ne voulez pas configurer de certificat SSL pour `ai-insighter.ca`.

---

## **Étape 1 : Configuration de la redirection dans Apache**
Vous pouvez configurer une redirection permanente (301) dans le fichier Apache de votre serveur, généralement dans `bitnami.conf`.

# **1. Ouvrir le fichier Apache de configuration :**
```bash
sudo nano /opt/bitnami/apache/conf/bitnami/bitnami.conf
```

# **2. Ajouter la redirection pour le domaine `ai-insighter.ca` :**
Recherchez la section HTTPS (port 443) ou créez un nouveau bloc dédié pour `ai-insighter.ca`. Ajoutez cette configuration :

```apache
<VirtualHost *:443>
    ServerName ai-insighter.ca
    ServerAlias www.ai-insighter.ca

    # Redirection vers ai-insighter.com
    Redirect 301 / https://ai-insighter.com/

    SSLEngine on
    SSLCertificateFile "/etc/ssl/cloudflare/origin.pem"  # Exemple de certificat (Cloudflare ou autre)
    SSLCertificateKeyFile "/etc/ssl/cloudflare/origin.key"
</VirtualHost>
```

---

## **Étape 2 : Forcer les requêtes HTTP (port 80) à rediriger vers HTTPS**
Ajoutez également une redirection HTTP → HTTPS dans le fichier :

```apache
<VirtualHost *:80>
    ServerName ai-insighter.ca
    ServerAlias www.ai-insighter.ca

    # Redirection HTTP vers HTTPS
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/(.*)$ https://ai-insighter.ca/$1 [R,L]
</VirtualHost>
```

Ensuite, HTTPS redirigera vers `ai-insighter.com` comme configuré ci-dessus.

---

## **Étape 3 : Redémarrer Apache pour appliquer les modifications**
Après avoir enregistré les modifications, redémarrez Apache :
```bash
sudo /opt/bitnami/ctlscript.sh restart apache
```

---

## **Option 2 : Configuration de la redirection via Cloudflare**
Si vous utilisez **Cloudflare** pour gérer le DNS de votre domaine, vous pouvez configurer une redirection au niveau de Cloudflare sans toucher à votre serveur.

# **1. Accédez à votre tableau de bord Cloudflare :**
- Connectez-vous à [Cloudflare](https://www.cloudflare.com/).
- Sélectionnez le domaine `ai-insighter.ca`.

# **2. Configurez une règle de redirection (Page Rules) :**
1. Allez dans **Rules > Page Rules**.
2. Cliquez sur **Create Page Rule**.
3. Configurez la redirection comme suit :
   - **URL à matcher** : `https://ai-insighter.ca/*`
   - **Action** : **Forwarding URL** (Redirection permanente 301).
   - **Destination** : `https://ai-insighter.com/$1`.

4. Sauvegardez la règle.

---

## **Étape 4 : Tester la redirection**
1. Accédez à `http://ai-insighter.ca` ou `https://ai-insighter.ca`.
2. Vérifiez que vous êtes redirigé automatiquement vers `https://ai-insighter.com`.

---

### **Résumé des options**
| **Méthode**                     | **Avantages**                                           | **Inconvénients**                                   |
|----------------------------------|-------------------------------------------------------|---------------------------------------------------|
| **Redirection Apache**           | Directe, gérée au niveau du serveur.                  | Nécessite de configurer Apache manuellement.      |
| **Redirection Cloudflare**       | Simple, pas besoin de toucher au serveur.             | Dépend de Cloudflare pour fonctionner.            |

---

### **Recommandation**
- **Si vous utilisez Cloudflare** : La redirection via Cloudflare est la méthode la plus simple.
- **Si vous voulez tout gérer sur le serveur** : Configurez une redirection via Apache.



