# Troubleshooting

- Pour voir les logs de ton fichier `docker-compose`, tu peux utiliser la commande suivante dans ton terminal :

```bash
docker-compose logs
```

Cette commande affichera les logs de tous les services définis dans ton fichier `docker-compose.yml`.

### Options utiles pour `docker-compose logs`

1. **Voir les logs d’un service spécifique :**

   Si tu souhaites uniquement voir les logs d’un service particulier, ajoute son nom après `logs` :

   ```bash
   docker-compose logs <nom_du_service>
   ```

2. **Suivre les logs en temps réel :**

   Pour suivre les logs en temps réel, ajoute l’option `-f` :

   ```bash
   docker-compose logs -f
   ```

3. **Afficher plus ou moins de lignes :**

   Tu peux ajuster le nombre de lignes de log affichées au démarrage avec `--tail` suivi du nombre de lignes souhaitées :

   ```bash
   docker-compose logs --tail=50
   ```
