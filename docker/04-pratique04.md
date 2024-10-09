
### **Manipulations # 4 - SUPPRIMER DES IMAGES ET DES CONTAINERS & CRÉER UNE IMAGE NGINX**

# **Objectif** :
- Cet exercice permet de comprendre comment gérer des conteneurs Docker en les lançant, les arrêtant, et les supprimant, ainsi que comment gérer les images Docker. 
- Il montre également l’importance d’arrêter un conteneur avant de le supprimer, et comment utiliser des commandes comme `docker ps -q` pour manipuler plusieurs conteneurs ou images à la fois.

#### Étape 1 : Créer des conteneurs Nginx avec différents ports
1. Lancez trois conteneurs Nginx, chacun sur un port différent de votre machine hôte :
   ```bash
   docker run -it -d -p 8080:80 --name c1 nginx   # Conteneur c1 sur le port 8080
   docker run -it -d -p 8081:80 --name c2 nginx   # Conteneur c2 sur le port 8081
   docker run -it -d -p 8082:80 --name c3 nginx   # Conteneur c3 sur le port 8082
   ```
   **Testez chaque conteneur** en visitant `http://localhost:8080`, `http://localhost:8081`, et `http://localhost:8082` pour vérifier qu'ils fonctionnent.

2. Vérifiez que les conteneurs sont bien en cours d'exécution avec la commande :
   ```bash
   docker ps
   ```

#### Étape 2 : Gérer les conteneurs en cours d’exécution

3. Listez uniquement les IDs des conteneurs en cours d’exécution :
   ```bash
   docker ps -q
   ```

4. Stoppez les trois conteneurs en utilisant soit leurs IDs, soit leurs noms :
   ```bash
   docker stop c1   # Ou docker stop <ID_c1>
   docker stop c2   # Ou docker stop <ID_c2>
   docker stop c3   # Ou docker stop <ID_c3>
   ```

5. Confirmez que tous les conteneurs sont arrêtés :
   ```bash
   docker ps
   ```

#### Étape 3 : Redémarrer et arrêter les conteneurs par leur nom

6. Redémarrez les conteneurs en utilisant leur nom :
   ```bash
   docker start c1
   docker start c2
   docker start c3
   ```

7. Arrêtez à nouveau les conteneurs par leur nom :
   ```bash
   docker stop c1
   docker stop c2
   docker stop c3
   ```

#### Étape 4 : Supprimer les conteneurs

8. Supprimez un conteneur en utilisant son ID ou son nom :
   ```bash
   docker rm c1   # Ou docker rm <ID_c1>
   ```

9. Pour supprimer tous les conteneurs arrêtés, utilisez la commande suivante :
   ```bash
   docker stop $(docker ps -a -q)   # Stoppe tous les conteneurs
   docker rm $(docker ps -a -q)     # Supprime tous les conteneurs
   ```

10. **Astuce** : Exécutez cette commande pour voir les IDs des conteneurs arrêtés avant la suppression :
    ```bash
    docker ps -a -q
    ```

#### Étape 5 : Supprimer les images Docker

11. Même après avoir supprimé les conteneurs, les images Docker restent sur votre machine. Pour voir les images disponibles :
    ```bash
    docker images
    ```

12. Listez uniquement les IDs des images :
    ```bash
    docker images -q
    ```

13. Supprimez toutes les images Docker :
    ```bash
    docker rmi $(docker images -q)
    ```

14. Confirmez que toutes les images ont été supprimées :
    ```bash
    docker images
    ```




