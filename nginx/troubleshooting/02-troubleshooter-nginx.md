### **1. Commandes de base pour Nginx**
1. **Vérifier si Nginx est installé :**
   ```bash
   nginx -v
   ```

2. **Démarrer Nginx :**
   ```bash
   sudo systemctl start nginx
   ```

3. **Arrêter Nginx :**
   ```bash
   sudo systemctl stop nginx
   ```

4. **Redémarrer Nginx :**
   ```bash
   sudo systemctl restart nginx
   ```

5. **Recharger la configuration sans redémarrer Nginx :**
   ```bash
   sudo systemctl reload nginx
   ```

6. **Vérifier le statut du service Nginx :**
   ```bash
   sudo systemctl status nginx
   ```

7. **Activer Nginx au démarrage du système :**
   ```bash
   sudo systemctl enable nginx
   ```

8. **Désactiver Nginx au démarrage du système :**
   ```bash
   sudo systemctl disable nginx
   ```

---

### **2. Commandes pour vérifier la configuration**
1. **Tester la syntaxe de la configuration Nginx :**
   ```bash
   sudo nginx -t
   ```

2. **Afficher le fichier de configuration principal :**
   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

3. **Lister tous les sites disponibles :**
   ```bash
   ls /etc/nginx/sites-available/
   ```

4. **Lister les sites activés :**
   ```bash
   ls /etc/nginx/sites-enabled/
   ```

5. **Afficher les paramètres de compilation Nginx :**
   ```bash
   nginx -V
   ```

---

### **3. Commandes pour surveiller Nginx**
1. **Afficher les connexions actives et les performances :**
   ```bash
   curl http://localhost/nginx_status
   ```
   *(Vous devrez activer le module `stub_status` dans la configuration de Nginx.)*

2. **Afficher les journaux d'accès Nginx :**
   ```bash
   sudo tail -f /var/log/nginx/access.log
   ```

3. **Afficher les journaux d'erreurs Nginx :**
   ```bash
   sudo tail -f /var/log/nginx/error.log
   ```

4. **Afficher les 100 dernières lignes du journal d'erreurs :**
   ```bash
   sudo tail -n 100 /var/log/nginx/error.log
   ```

---

### **4. Dépannage de Nginx (troubleshooting)**

#### **4.1. Problèmes liés à la configuration**
1. **Vérifier la syntaxe des fichiers de configuration :**
   ```bash
   sudo nginx -t
   ```

2. **Corriger les permissions des fichiers de configuration :**
   ```bash
   sudo chown -R root:root /etc/nginx/
   sudo chmod -R 644 /etc/nginx/*
   ```

3. **Regénérer un lien symbolique pour un site activé :**
   ```bash
   sudo ln -s /etc/nginx/sites-available/mon-site /etc/nginx/sites-enabled/
   ```

4. **Désactiver un site problématique en supprimant le lien symbolique :**
   ```bash
   sudo rm /etc/nginx/sites-enabled/mon-site
   ```

---

#### **4.2. Problèmes liés aux ports**
1. **Vérifier si Nginx utilise le port 80 ou 443 :**
   ```bash
   sudo netstat -tuln | grep 80
   ```

2. **Vérifier si un autre service utilise le port 80 ou 443 :**
   ```bash
   sudo lsof -i :80
   sudo lsof -i :443
   ```

3. **Arrêter le service en conflit (par exemple, Apache) :**
   ```bash
   sudo systemctl stop apache2
   sudo systemctl disable apache2
   ```

---

#### **4.3. Problèmes liés aux permissions**
1. **Corriger les permissions des répertoires web :**
   ```bash
   sudo chown -R www-data:www-data /var/www/
   sudo chmod -R 755 /var/www/
   ```

---

#### **4.4. Vérifier les journaux Nginx**
1. **Afficher les erreurs spécifiques :**
   ```bash
   sudo tail -f /var/log/nginx/error.log
   ```

2. **Rechercher des erreurs spécifiques dans les journaux :**
   ```bash
   sudo grep "error" /var/log/nginx/error.log
   ```

---

#### **4.5. Problèmes liés aux modules**
1. **Assurez-vous que PHP-FPM est actif si vous utilisez PHP :**
   ```bash
   sudo systemctl status php8.1-fpm
   sudo systemctl start php8.1-fpm
   ```

2. **Corrigez la configuration si PHP-FPM n'est pas détecté :**
   Vérifiez que le fichier de configuration du site contient :
   ```nginx
   location ~ \.php$ {
       include snippets/fastcgi-php.conf;
       fastcgi_pass unix:/run/php/php8.1-fpm.sock;
   }
   ```

---

#### **4.6. Problèmes liés au DNS local**
1. **Vérifiez votre fichier `/etc/hosts` pour les domaines locaux :**
   ```bash
   sudo nano /etc/hosts
   ```
   Assurez-vous que vos domaines locaux sont configurés comme suit :
   ```plaintext
   127.0.0.1 elastic.local
   127.0.0.1 dev.elastic.local
   ```

2. **Nettoyez le cache DNS si les changements ne s'appliquent pas :**
   ```bash
   sudo systemctl restart systemd-resolved
   ```

---

### **5. Réinstallation complète de Nginx (si nécessaire)**

1. **Supprimer complètement Nginx :**
   ```bash
   sudo apt remove --purge nginx nginx-common -y
   ```

2. **Nettoyer les fichiers de configuration résiduels :**
   ```bash
   sudo rm -rf /etc/nginx
   sudo rm -rf /var/log/nginx
   ```

3. **Réinstaller Nginx :**
   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```

---

### **6. Conseils pour une résolution rapide**
- **Utilisez toujours `nginx -t` avant de redémarrer le service.**
- **Consultez les journaux d'erreurs pour identifier le problème.**
- **Vérifiez les conflits avec d'autres services (comme Apache).**
- **Assurez-vous que les permissions des répertoires et fichiers sont correctes.**

Avec ces commandes et astuces, vous devriez pouvoir dépanner et gérer efficacement Nginx. 😊
