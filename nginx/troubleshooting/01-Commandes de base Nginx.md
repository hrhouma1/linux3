## Commandes de base Nginx

- Démarrer Nginx :
  ```
  sudo systemctl start nginx
  ```

- Arrêter Nginx :
  ```
  sudo systemctl stop nginx
  ```

- Redémarrer Nginx :
  ```
  sudo systemctl restart nginx
  ```

- Recharger la configuration sans arrêter Nginx :
  ```
  sudo systemctl reload nginx
  ```

- Vérifier le statut de Nginx :
  ```
  sudo systemctl status nginx
  ```

## Commandes de dépannage

- Tester la configuration Nginx :
  ```
  sudo nginx -t
  ```

- Afficher la version de Nginx :
  ```
  nginx -v
  ```

- Afficher les options de compilation et la version détaillée :
  ```
  nginx -V
  ```

- Vérifier les ports ouverts :
  ```
  sudo netstat -tuln | grep :80
  ```

- Démarrer Nginx en mode debug :
  ```
  sudo nginx -g 'daemon off;'
  ```

- Vérifier les logs d'erreur :
  ```
  sudo tail -f /var/log/nginx/error.log
  ```

- Vérifier les processus Nginx en cours :
  ```
  ps aux | grep nginx
  ```

## Résolution des problèmes courants

- Vérifier les permissions des fichiers de configuration :
  ```
  ls -l /etc/nginx/nginx.conf
  ```

- S'assurer que le répertoire des logs existe et a les bonnes permissions :
  ```
  sudo mkdir -p /var/log/nginx
  sudo chown -R www-data:www-data /var/log/nginx
  ```

- Vérifier si un autre processus utilise le port 80 :
  ```
  sudo lsof -i :80
  ```

- Vérifier la syntaxe du fichier de configuration :
  ```
  sudo nginx -c /etc/nginx/nginx.conf -t
  ```

Si Nginx ne démarre toujours pas après avoir essayé ces commandes, vérifiez les logs système pour plus de détails sur l'erreur :

```
sudo journalctl -u nginx.service
```

N'oubliez pas de redémarrer Nginx après avoir apporté des modifications à la configuration.

