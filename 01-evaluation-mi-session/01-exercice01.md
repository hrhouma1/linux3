# Exercice #1 : Création et Gestion de Conteneurs Docker

# Objectif :
Évaluer votre capacité  à créer plusieurs conteneurs Docker à partir d'images spécifiées, à configurer les ports, à créer des réseaux Docker, et à tester la connectivité entre les conteneurs.

#### Durée :
1 heures

# Instructions :

1. **Préparation :**
   - Assurez-vous que Docker est installé et fonctionne correctement sur votre machine.
   - Créez un répertoire nommé `exam-pratique-1` pour stocker vos fichiers et configurations.

2. **Exercice 1 : Création de Conteneurs**

   Utilisez les informations du tableau ci-dessous pour créer les conteneurs spécifiés.

   | Conteneur | Image        | Tag  | Nom              | Port Hôte | Port Conteneur |
   |-----------|--------------|------|------------------|-----------|----------------|
   | web       | nginx        | latest | web-container    | 8080      | 80             |
   | db        | mysql        | 5.7  | db-container     | 3306      | 3306           |
   | app       | node         | 14   | app-container    | 3000      | 3000           |

   - Créez et lancez les conteneurs en utilisant les commandes appropriées.


3. **Exercice 2 : Création de Réseaux Docker**

   Utilisez les informations du tableau ci-dessous pour créer et configurer les réseaux Docker.

   | Réseau          | Nom                | Sous-réseau        |
   |-----------------|--------------------|--------------------|
   | Frontend        | frontend-network   | 172.18.0.0/16      |
   | Backend         | backend-network    | 172.19.0.0/16      |

   - Créez les réseaux Docker.
   - Connectez les conteneurs aux réseaux correspondants.


| Conteneur       | Réseau            | Connexion                                  |
|-----------------|-------------------|--------------------------------------------|
| web-container   | frontend-network  |                                            |
| db-container    | backend-network   |                                            |
| app-container   | frontend-network  |                                            |
| app-container   | backend-network   |                                            |

# Tests de Connectivité

- Vérifiez la connectivité entre les conteneurs en utilisant des commandes `ping` à partir des conteneurs.



4. **Exercice 3 : Tests de Connectivité**

   - Vérifiez la connectivité entre les conteneurs en utilisant des commandes `ping` à partir des conteneurs.
   - Les pings doivent réussir, indiquant que les conteneurs peuvent communiquer entre eux sur les réseaux configurés.

5. **Exercice 4 : Réécrire les configurations dans un docker-compose.yml**

   - Écrire un fichier `docker-compose.yml` qui inclut les configurations pour tous les conteneurs créés dans les exercices précédents.
   - Le fichier `docker-compose.yml` doit inclure la configuration des réseaux, des volumes, et des conteneurs.
   - Le fichier doit permettre de recréer l'intégralité de l'infrastructure en une seule commande.


6. **Critères d'Évaluation :**
   - **Création de Conteneurs :** Les conteneurs `web-container`, `db-container`, et `app-container` sont créés correctement et fonctionnent comme prévu.
   - **Configuration des Ports :** Les ports sont configurés correctement, permettant l'accès aux services sur les ports spécifiés.
   - **Création et Utilisation de Réseaux Docker :** Les réseaux Docker `frontend-network` et `backend-network` sont créés et les conteneurs y sont connectés correctement.
   - **Tests de Connectivité :** Les conteneurs peuvent communiquer entre eux via les réseaux configurés.
   - **Documentation :** Fournir un fichier README.md ou un document dans un format WORD ou PDF décrivant les étapes suivies pour chaque exercice, incluant les commandes utilisées et les vérifications effectuées.

# Bonnes pratiques à suivre :
- Utilisez des commandes claires et précises.
- Vérifiez chaque étape pour vous assurer que les conteneurs fonctionnent comme attendu.
- Documentez votre travail de manière détaillée.
