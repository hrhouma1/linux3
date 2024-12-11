### Transformer un site HTTP en HTTPS avec Nginx sous Ubuntu 22.04 : Guide complet

---

### **Introduction**
Ce guide vous permettra de configurer un site HTTPS sécurisé avec Nginx sur un serveur Ubuntu 22.04. Il couvre l'installation et la configuration du certificat SSL/TLS, la redirection automatique HTTP vers HTTPS, l'optimisation de la sécurité avec des headers HTTP, et les tests finaux pour vérifier la configuration.

---

### **Pré-requis**
- **Nom de domaine actif** : Assurez-vous que votre domaine pointe correctement vers votre serveur.
- **Serveur Nginx** installé et fonctionnel.
- **Accès root ou sudo** sur le serveur Ubuntu.
- **Certificat SSL/TLS** (gratuit via Let's Encrypt ou payant).

---

### **Partie 1 : Installation de Nginx**

#### **1. Mettre à jour le système**
```bash
sudo apt update && sudo apt upgrade -y
```

#### **2. Installer Nginx**
```bash
sudo apt install nginx -y
```

#### **3. Vérifier que Nginx est actif**
```bash
sudo systemctl status nginx
```

#### **4. Autoriser Nginx dans le pare-feu**
```bash
sudo ufw allow 'Nginx Full'
```

---

### **Partie 2 : Installer Certbot et obtenir un certificat SSL/TLS**

#### **1. Installer Certbot et le plugin Nginx**
```bash
sudo apt install certbot python3-certbot-nginx -y
```

#### **2. Obtenir un certificat SSL/TLS**
```bash
sudo certbot --nginx
```
Suivez les instructions :
- Entrez votre nom de domaine (par ex. `example.com`).
- Certbot configurera automatiquement le fichier de configuration Nginx pour activer HTTPS.

#### **3. Tester le renouvellement automatique**
Certbot configure un cron job par défaut pour renouveler automatiquement les certificats. Testez-le :
```bash
sudo certbot renew --dry-run
```

---

### **Partie 3 : Configuration Nginx pour HTTPS**

#### **1. Fichier de configuration Nginx**
Vérifiez ou modifiez le fichier de configuration pour inclure HTTPS. Par exemple :
```bash
sudo nano /etc/nginx/sites-available/example.com
```

##### Exemple de configuration complète :
```conf
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com www.example.com;

    root /var/www/html;
    index index.html index.htm index.php;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;
    ssl_session_tickets off;

    # HTTP Security Headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options DENY;
    add_header X-XSS-Protection "1; mode=block";
    add_header Referrer-Policy "strict-origin-when-cross-origin";
    add_header Permissions-Policy "geolocation=(), microphone=(), camera=()";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; object-src 'none';";

    location / {
        try_files $uri $uri/ =404;
    }

    # Redirect non-HTTPS traffic
    error_page 404 /404.html;
    location = /404.html {
        root /var/www/html;
    }

    # PHP support (if needed)
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

#### **2. Activer la configuration**
Créez un lien symbolique entre `sites-available` et `sites-enabled` :
```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

Désactivez la configuration par défaut si nécessaire :
```bash
sudo unlink /etc/nginx/sites-enabled/default
```

#### **3. Vérifier la configuration**
```bash
sudo nginx -t
```

#### **4. Redémarrer Nginx**
```bash
sudo systemctl reload nginx
```

---

### **Partie 4 : Renforcer la sécurité avec des headers HTTP**

Ajoutez les headers suivants pour renforcer la sécurité :
```conf
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
add_header X-Content-Type-Options nosniff;
add_header X-Frame-Options DENY;
add_header X-XSS-Protection "1; mode=block";
add_header Referrer-Policy "strict-origin-when-cross-origin";
add_header Permissions-Policy "geolocation=(), microphone=(), camera=()";
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; object-src 'none';";
```

---

### **Partie 5 : Configuration avancée**

#### **1. Désactiver les protocoles obsolètes**
Modifiez votre fichier de configuration pour désactiver les anciens protocoles SSL/TLS :
```conf
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers HIGH:!aNULL:!MD5;
```

#### **2. Activer OCSP Stapling**
Ajoutez ces lignes dans la configuration du serveur HTTPS :
```conf
ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 5s;
```

---

### **Partie 6 : Tests et validation**

#### **1. Vérifier l'accès HTTPS**
Visitez votre site avec `https://example.com`. Vous devriez voir un cadenas vert dans la barre d'adresse.

#### **2. Tester la sécurité du certificat SSL**
Utilisez l’outil [SSL Labs](https://www.ssllabs.com/ssltest/) pour analyser votre configuration SSL/TLS.

#### **3. Vérifier les headers HTTP**
Utilisez [Security Headers](https://securityheaders.com/) pour vérifier les headers HTTP.

---

### **Partie 7 : Maintenance**

#### **1. Renouvellement automatique des certificats**
Certbot configure automatiquement le renouvellement. Testez-le régulièrement :
```bash
sudo certbot renew --dry-run
```

#### **2. Sauvegarde des configurations**
Sauvegardez vos fichiers Nginx :
```bash
sudo tar -czvf nginx-backup.tar.gz /etc/nginx/
```

---

### **Conclusion**
Ce guide couvre tous les aspects nécessaires pour transformer un site HTTP en HTTPS sous Ubuntu 22.04 avec Nginx. Une configuration rigoureuse assure la sécurité des communications et renforce la confiance des utilisateurs tout en optimisant le SEO. N'oubliez pas de tester régulièrement votre configuration pour garantir une sécurité optimale.
