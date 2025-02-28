## Références:

 - https://securityheaders.com/?q=https%3A%2F%2Fenloja.ca%2F&followRedirects=on
 - https://www.ssllabs.com/ssltest/
 - https://www.ssllabs.com/
 - https://www.fortiguard.com/faq/appctrlsubmit


---
# Partie 1
---

### Cours : Les En-têtes de Sécurité Web

---

#### **Bienvenue dans le monde de la sécurité web !**
Imagine que tu possèdes une maison. Pour protéger cette maison, tu peux mettre une serrure à la porte, un judas pour voir qui frappe, et des barreaux aux fenêtres. Eh bien, sur internet, **les en-têtes de sécurité** jouent un rôle similaire pour protéger ton site web et ses visiteurs contre les "voleurs" numériques.

---

### **Qu’est-ce qu’un en-tête de sécurité ?**
Un **en-tête HTTP** est comme une petite note secrète que le serveur (l'ordinateur qui héberge ton site web) envoie au navigateur (comme Chrome ou Firefox) chaque fois qu'une page web est chargée. Cette note donne des instructions au navigateur pour :

1. **Protéger les visiteurs** contre les attaques.
2. **Restreindre ce que le navigateur peut faire avec ton site**.
3. **Empêcher les mauvaises surprises** (comme charger un virus déguisé en image).

Les **en-têtes de sécurité**, en particulier, disent au navigateur **comment naviguer en toute sécurité** sur ton site.

---

### **Pourquoi en avons-nous besoin ?**
Sans ces en-têtes, ton site web est un peu comme une maison avec une porte grande ouverte. Cela peut permettre :

- **Des pirates informatiques** de voler des informations sensibles.
- **Des scripts malveillants** d'infecter les ordinateurs des visiteurs.
- **Des tiers malveillants** de manipuler ton site.

En bref : **pas d’en-têtes de sécurité = vulnérabilité**.

---

### **Les en-têtes de sécurité expliqués simplement**

#### 1. **X-Frame-Options : Protéger ton site contre le "vol de clics"**
Imagine qu’un site malveillant charge ton site en cachette, comme une vitre qui cache une trappe piégée. Si quelqu'un clique, il pourrait envoyer de l'argent ou donner des informations privées sans s'en rendre compte.  
👉 Cet en-tête empêche ton site d'être affiché dans une "vitre" (iframe) sur un autre site.

- **Option :** `SAMEORIGIN`  
   Cela signifie : **Seul ton site peut afficher ses propres pages.**

---

#### 2. **X-XSS-Protection : Bloquer les scripts méchants**
Les pirates adorent utiliser des "scripts malicieux" pour voler des mots de passe ou pirater des comptes. C’est comme un cambrioleur qui pose des faux panneaux "ici, entrez votre code PIN".  
👉 Cet en-tête demande au navigateur de **bloquer les pages suspectes** qui semblent contenir ces scripts.

- **Option :** `1; mode=block`  
   Cela signifie : **Si tu détectes quelque chose de bizarre, bloque tout !**

---

#### 3. **X-Content-Type-Options : Ne pas se laisser berner**
Parfois, un fichier qui ressemble à une image peut en fait être un virus déguisé. C’est comme recevoir un colis "apparemment" inoffensif, mais qui cache une bombe.  
👉 Cet en-tête dit au navigateur de **ne pas deviner** ce qu'un fichier contient. Il doit respecter ce que le serveur déclare.

- **Option :** `nosniff`  
   Cela signifie : **Ne prends aucun risque, reste strict.**

---

#### 4. **Referrer-Policy : Garder les secrets pour toi**
Quand tu passes d’un site à un autre, tu laisses souvent des traces sur l'origine (comme un courrier avec une adresse de retour). Parfois, cela peut révéler des informations sensibles.  
👉 Cet en-tête contrôle **les informations que ton site partage** lorsqu’un visiteur clique sur un lien.

- **Option :** `strict-origin-when-cross-origin`  
   Cela signifie : **Ne partage que ce qui est strictement nécessaire.**

---

#### 5. **Content-Security-Policy (CSP) : Le chef d’orchestre de la sécurité**
Imagine que ton site web est une grande scène. CSP agit comme un garde du corps et dit :  
- "Ok, ces scripts sont autorisés."  
- "Ces images viennent d’une source sûre."  
- "Personne ne peut faire n’importe quoi ici."

👉 Cet en-tête contrôle **tout ce que ton site est autorisé à charger**, comme des scripts, des images, ou des styles.

- **Option :** `default-src 'self' http: https: data: blob: 'unsafe-inline'`  
   Cela signifie :  
   - `'self'` : Charge seulement les fichiers venant de ton site.  
   - `http:` et `https:` : Autorise les ressources en ligne.  
   - `data:` et `blob:` : Permet les fichiers intégrés.  
   - `'unsafe-inline'` : (Attention !) Autorise les scripts en ligne (utile parfois, mais risqué).

---

### **Comment ajouter ces en-têtes ?**
Si ton site utilise **Nginx** (un serveur web), voici comment faire :

1. **Ouvre le fichier de configuration de ton site :**
   ```bash
   sudo nano /etc/nginx/sites-available/mon-site.com
   ```

2. **Ajoute les en-têtes de sécurité dans le bloc `server {}` :**
   ```nginx
   server {
       listen 80;
       server_name mon-site.com;

       # En-têtes de sécurité
       add_header X-Frame-Options "SAMEORIGIN";
       add_header X-XSS-Protection "1; mode=block";
       add_header X-Content-Type-Options "nosniff";
       add_header Referrer-Policy "strict-origin-when-cross-origin";
       add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'";

       root /var/www/mon-site.com;
       index index.html;
   }
   ```

3. **Redémarre Nginx pour appliquer les changements :**
   ```bash
   sudo systemctl restart nginx
   ```

---

### **Vérifie que tout fonctionne**
Tu peux vérifier si les en-têtes sont actifs avec la commande suivante :

```bash
curl -I http://mon-site.com
```

Regarde si les en-têtes comme `X-Frame-Options` ou `Content-Security-Policy` apparaissent dans la réponse.

---

### **Conclusion**
Les en-têtes de sécurité sont un **bouclier invisible** qui protège ton site et tes visiteurs. C'est une petite étape simple mais **puissante** pour renforcer la sécurité de ton site web. Et n’oublie pas : mieux vaut prévenir que guérir, surtout sur internet !

👨‍🏫 **Prochaine étape ?** Améliore ta configuration en explorant des outils comme HTTPS, les certificats SSL, et les firewalls. La sécurité, c’est un chemin, pas une destination ! 🌟


----------------
# Résumé:
----------------

Les **en-têtes de sécurité** sont des directives que les serveurs web envoient dans leurs réponses HTTP pour protéger les utilisateurs contre différentes attaques courantes, comme le **clickjacking**, les attaques par injection, ou encore l'exposition non intentionnelle de données sensibles.

Voici une explication des en-têtes que vous mentionnez :

---

### 1. **X-Frame-Options**
   - **Valeur utilisée : `"SAMEORIGIN"`**
   - **Fonction** : Protège contre les attaques de type **clickjacking**, où un site malveillant tente de charger votre site dans une iframe pour inciter les utilisateurs à cliquer sur des éléments cachés.
   - **Effet** :
     - `SAMEORIGIN` : Permet de charger le contenu dans une iframe uniquement si l'origine est la même que celle de la page.
     - Empêche les sites tiers de charger votre site dans une iframe.

---

### 2. **X-XSS-Protection**
   - **Valeur utilisée : `"1; mode=block"`**
   - **Fonction** : Active le filtre de protection contre les attaques XSS (Cross-Site Scripting) intégré dans les navigateurs modernes.
   - **Effet** :
     - `1` : Active le filtre XSS.
     - `mode=block` : Si une attaque XSS est détectée, la page est bloquée au lieu d'être chargée partiellement.

---

### 3. **X-Content-Type-Options**
   - **Valeur utilisée : `"nosniff"`**
   - **Fonction** : Empêche les navigateurs de **deviner** (ou de "sniffer") le type de contenu des fichiers téléchargés, même si l'en-tête `Content-Type` est incorrect ou absent.
   - **Effet** :
     - Protège contre certaines attaques où un fichier malveillant est interprété différemment de ce qui est prévu (exemple : un script malveillant déguisé en fichier image).

---

### 4. **Referrer-Policy**
   - **Valeur utilisée : `"strict-origin-when-cross-origin"`**
   - **Fonction** : Contrôle la quantité d'informations envoyées dans l'en-tête `Referer` lors de la navigation entre sites.
   - **Effet** :
     - Si la navigation reste sur le même site (**même origine**), l'URL complète est envoyée.
     - Pour des demandes vers un site externe (**cross-origin**), seule l'origine (par exemple, `https://example.com`) est incluse.
     - Protège la confidentialité en évitant de révéler des informations sensibles contenues dans l'URL (comme des paramètres de session ou des identifiants).

---

### 5. **Content-Security-Policy (CSP)**
   - **Valeur utilisée : `"default-src 'self' http: https: data: blob: 'unsafe-inline';"`**
   - **Fonction** : Définit des règles strictes pour les ressources que le navigateur est autorisé à charger (scripts, images, styles, etc.).
   - **Explication des règles utilisées** :
     - **`default-src 'self'`** : Par défaut, les ressources ne peuvent être chargées que depuis la même origine.
     - **`http:` et `https:`** : Autorise également le chargement des ressources via HTTP ou HTTPS.
     - **`data:` et `blob:`** : Permet l'utilisation de ressources intégrées ou générées dynamiquement (comme des fichiers Blob ou Data URI).
     - **`'unsafe-inline'`** : Autorise les scripts ou styles en ligne (attention, cela peut affaiblir la sécurité).

---

### Pourquoi ces en-têtes sont importants ?
Ces en-têtes renforcent la sécurité de votre application web en réduisant les vecteurs d'attaques courants. Leur configuration dans le fichier Nginx (ou Apache) aide à protéger les utilisateurs sans nécessiter de modifications au niveau du code de l'application.

---

### Étape pour les ajouter :
Dans votre fichier de configuration **Nginx**, vous ajoutez ces directives dans le bloc `server {}` correspondant à votre site :

```nginx
server {
    listen 80;
    server_name hrehouma.com www.hrehouma.com;

    # En-têtes de sécurité
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";
    add_header Referrer-Policy "strict-origin-when-cross-origin";
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'";
    
    # Autres configurations...
    root /var/www/hrehouma.com;
    index index.html;
}
```

Après modification, redémarrez le service Nginx pour appliquer les changements :

```bash
sudo systemctl restart nginx
```

---

### Validation
Pour vérifier que les en-têtes sont bien appliqués, utilisez un outil comme **curl** ou inspectez les réponses HTTP dans les outils de développement de votre navigateur :

```bash
curl -I https://hrehouma.com
```






---
# Partie 2 - Annexe de vulgarisation
---


![image](https://github.com/user-attachments/assets/4423bbe3-8814-4685-9a9f-40bcf7223495)

- Ce rapport est directement lié aux en-têtes de sécurité ! 

---

### **1. Strict-Transport-Security (HSTS)**
- **Problème détecté :** Cet en-tête manque sur ton site.
- **Qu’est-ce que c’est ?**  
  L’en-tête HTTP Strict-Transport-Security (HSTS) force les navigateurs à n'accéder à ton site que via HTTPS (connexion sécurisée). Cela empêche les attaques de type "downgrade" où un pirate essaie de rediriger un utilisateur vers une version non sécurisée de ton site.
- **Valeur recommandée :**  
  ```nginx
  add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
  ```
  - **`max-age=31536000` :** Garde la politique active pendant 1 an (31536000 secondes).
  - **`includeSubDomains` :** Applique aussi cette règle aux sous-domaines.

---

### **2. Content-Security-Policy (CSP)**
- **Problème détecté :** L'en-tête manque.
- **Qu’est-ce que c’est ?**  
  Cet en-tête empêche le chargement de scripts, images ou ressources malveillantes sur ton site. Il limite strictement ce que le navigateur est autorisé à charger.
- **Valeur recommandée (exemple basique) :**  
  ```nginx
  add_header Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self'";
  ```
  - **`default-src 'self'` :** Autorise seulement les ressources de ton propre site.
  - **`script-src 'self'` :** Charge uniquement les scripts locaux.
  - **`style-src 'self'` :** Charge uniquement les fichiers CSS locaux.

---

### **3. X-Frame-Options**
- **Problème détecté :** L'en-tête manque.
- **Qu’est-ce que c’est ?**  
  Cet en-tête empêche d'intégrer ton site dans des "frames" ou "iframes", ce qui peut éviter les attaques de type **clickjacking**.
- **Valeur recommandée :**  
  ```nginx
  add_header X-Frame-Options "SAMEORIGIN";
  ```

---

### **4. X-Content-Type-Options**
- **Problème détecté :** L'en-tête manque.
- **Qu’est-ce que c’est ?**  
  Empêche le navigateur de deviner le type des fichiers (par exemple, un fichier malveillant déguisé en image).
- **Valeur recommandée :**  
  ```nginx
  add_header X-Content-Type-Options "nosniff";
  ```

---

### **5. Referrer-Policy**
- **Problème détecté :** L'en-tête manque.
- **Qu’est-ce que c’est ?**  
  Contrôle la quantité d'informations que ton site partage lorsqu'un utilisateur clique sur un lien vers une autre page ou un autre site.
- **Valeur recommandée :**  
  ```nginx
  add_header Referrer-Policy "strict-origin-when-cross-origin";
  ```

---

### **6. Permissions-Policy**
- **Problème détecté :** L'en-tête manque.
- **Qu’est-ce que c’est ?**  
  Cet en-tête contrôle les fonctionnalités et les API accessibles sur ton site (comme la caméra, le micro, ou le GPS). Par exemple, tu peux limiter l’accès à ces fonctionnalités aux utilisateurs autorisés.
- **Valeur recommandée :**  
  ```nginx
  add_header Permissions-Policy "geolocation=(), microphone=(), camera=()";
  ```
  - **`geolocation=()` :** Désactive la géolocalisation.
  - **`microphone=()` :** Désactive l'accès au microphone.
  - **`camera=()` :** Désactive l'accès à la caméra.

---

### **Comment corriger cela ?**
1. **Ouvre le fichier de configuration Nginx de ton site :**
   ```bash
   sudo nano /etc/nginx/sites-available/ai-insighter.com
   ```

2. **Ajoute les en-têtes de sécurité dans le bloc `server {}` :**
   ```nginx
   server {
       listen 80;
       server_name ai-insighter.com www.ai-insighter.com;

       # En-têtes de sécurité
       add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";
       add_header Content-Security-Policy "default-src 'self'; script-src 'self'; style-src 'self'";
       add_header X-Frame-Options "SAMEORIGIN";
       add_header X-Content-Type-Options "nosniff";
       add_header Referrer-Policy "strict-origin-when-cross-origin";
       add_header Permissions-Policy "geolocation=(), microphone=(), camera=()";

       root /var/www/ai-insighter.com;
       index index.html;
   }
   ```

3. **Redémarre le serveur Nginx pour appliquer les changements :**
   ```bash
   sudo systemctl restart nginx
   ```

---

### **Validation**
Après avoir ajouté ces en-têtes, utilise à nouveau l'outil de vérification ou la commande suivante pour confirmer qu'ils sont actifs :
```bash
curl -I https://www.ai-insighter.com
```

Tu devrais maintenant voir tous les en-têtes de sécurité appliqués et ton score de sécurité devrait s’améliorer considérablement ! 🎉



---
# Partie 3 - Annexe 2
---


![image](https://github.com/user-attachments/assets/b13e3e80-af16-4b13-bf96-405fbd7a19fb)


### 1. **Score de sécurité : F**
Le site a obtenu la note **F** (très faible) car plusieurs en-têtes HTTP de sécurité essentiels manquent. Ces en-têtes sont nécessaires pour protéger les utilisateurs contre des attaques comme le cross-site scripting (XSS), le clickjacking, et les fuites d'informations.

---

### 2. **En-têtes manquants**
Voici les en-têtes spécifiques manquants et leur rôle :

#### a) **Strict-Transport-Security (HSTS)**  
- **Description** : Ce header force les navigateurs à utiliser exclusivement le protocole HTTPS (crypté) pour accéder au site. Cela empêche les attaques de type **downgrade** (forçage de passer à HTTP) ou **man-in-the-middle**.
- **Recommandation** : Ajouter l’en-tête suivant dans la configuration du serveur :
  ```
  Strict-Transport-Security: max-age=31536000; includeSubDomains
  ```
  Cela force HTTPS pour un an (31 536 000 secondes).

---

#### b) **Content-Security-Policy (CSP)**  
- **Description** : Ce header limite les sources autorisées pour charger des scripts, images, styles, etc., afin d'éviter les attaques **XSS** (cross-site scripting).
- **Exemple** : 
  ```
  Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted-source.com
  ```
  Cela autorise uniquement les scripts provenant du domaine du site ou de sources de confiance.

---

#### c) **X-Frame-Options**  
- **Description** : Empêche d'intégrer le site dans un **iframe**, ce qui protège contre les attaques de type **clickjacking**.
- **Recommandation** : Ajouter l’en-tête suivant :
  ```
  X-Frame-Options: SAMEORIGIN
  ```
  Cela interdit l'affichage du site dans un iframe sauf s'il provient du même domaine.

---

#### d) **X-Content-Type-Options**  
- **Description** : Ce header empêche les navigateurs d'interpréter les fichiers de manière incorrecte (MIME sniffing). Cela peut réduire les attaques basées sur des fichiers malicieux.
- **Recommandation** :
  ```
  X-Content-Type-Options: nosniff
  ```

---

#### e) **Referrer-Policy**  
- **Description** : Ce header contrôle les informations que votre site transmet à d'autres sites via les en-têtes HTTP "Referer". Cela protège la vie privée des utilisateurs.
- **Recommandation** : Par exemple :
  ```
  Referrer-Policy: no-referrer-when-downgrade
  ```

---

#### f) **Permissions-Policy** (anciennement Feature-Policy)  
- **Description** : Ce header contrôle quelles fonctionnalités (comme la caméra, le micro, la géolocalisation) peuvent être utilisées sur le site.
- **Recommandation** : Exemple :
  ```
  Permissions-Policy: geolocation=(), microphone=()
  ```

---

### 3. **Étapes à suivre pour améliorer la sécurité**
1. **Configurer les headers manquants dans le serveur Web** (Nginx, Apache, etc.). Voici un exemple pour Nginx :
   ```nginx
   add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
   add_header Content-Security-Policy "default-src 'self'; script-src 'self';";
   add_header X-Frame-Options "SAMEORIGIN";
   add_header X-Content-Type-Options "nosniff";
   add_header Referrer-Policy "no-referrer-when-downgrade";
   add_header Permissions-Policy "geolocation=(), microphone=()";
   ```

2. **Tester à nouveau le site après la configuration** en utilisant des outils de sécurité en ligne (comme celui utilisé ici).

3. **Mettre à jour régulièrement la configuration** en fonction des dernières recommandations de sécurité.

---

### Conclusion
Ce rapport montre que le site est vulnérable à plusieurs types d’attaques en raison de l’absence d’en-têtes de sécurité critiques. En ajoutant ces headers, vous renforcerez considérablement la sécurité et la confidentialité des utilisateurs. Si vous avez besoin d'aide pour les implémenter, je peux vous guider pas à pas !



