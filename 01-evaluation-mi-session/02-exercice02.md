# Exercice 2 : Création et Gestion Avancée de Conteneurs Docker avec Registres Privés, Volumes Multiples et Réseaux

# Objectif :
- Évaluer votre capacité à créer plusieurs conteneurs Docker à partir d'images spécifiées, à configurer les ports, à créer des réseaux Docker, à utiliser des registres privés et des volumes Docker, et à tester la connectivité entre les conteneurs.

# Durée :
1 heures

# Instructions :

1. **Préparation :**
   - Assurez-vous que Docker est installé et fonctionne correctement sur votre machine.
   - Créez un répertoire nommé `exam-pratique-2` pour stocker vos fichiers et configurations.

2. **Exercice 1 : Création de Conteneurs**

   Utilisez les informations du tableau ci-dessous pour créer les conteneurs spécifiés.

   | Conteneur       | Image        | Tag   | Nom              | Port Hôte | Port Conteneur |
   |-----------------|--------------|-------|------------------|-----------|----------------|
   | web             | nginx        | latest| web-container    | 8080      | 80             |
   | db              | mysql        | 5.7   | db-container     | 3306      | 3306           |
   | app             | almalinux    | latest| app-container    | 3000      | 80             |
   | priv_registry   | registry     | latest| priv-registry    | 5000      | 5000           |

   - Créez et lancez les conteneurs en utilisant les commandes appropriées.

3. **Exercice 2 : Création de Réseaux Docker**

   Utilisez les informations du tableau ci-dessous pour créer et configurer les réseaux Docker.

   | Réseau    | Nom              | Sous-réseau   |
   |-----------|------------------|---------------|
   | Frontend  | frontend-network | 172.18.0.0/16 |
   | Backend   | backend-network  | 172.19.0.0/16 |

   - Créez les réseaux Docker.
   - Connectez les conteneurs aux réseaux correspondants.

   | Conteneur       | Réseau            | Connexion                                  |
   |-----------------|-------------------|--------------------------------------------|
   | web-container   | frontend-network  |                                            |
   | db-container    | backend-network   |                                            |
   | app-container   | frontend-network  |                                            |
   | app-container   | backend-network   |                                            |
   | priv-registry   | frontend-network  |                                            |

4. **Exercice 3 : Utilisation de Volumes Multiples**

   Utilisez les informations du tableau ci-dessous pour configurer plusieurs volumes Docker pour les conteneurs spécifiés.

   | Conteneur       | Volume Hôte            | Volume Conteneur        |
   |-----------------|------------------------|-------------------------|
   | db-container    | /opt/data              | /var/lib/mysql          |
   | db-container    | /opt/db-logs           | /var/log/mysql          |
   | app-container   | /opt/app-data          | /usr/src/app            |
   | app-container   | /opt/app-config        | /etc/app                |
   | web-container   | /opt/web-content       | /usr/share/nginx/html   |
   | web-container   | /opt/web-logs          | /var/log/nginx          |

   - Configurez les conteneurs pour utiliser les volumes spécifiés.

5. **Exercice 4 : Utilisation d'un Registre Privé**

   - Créez un registre privé Docker en utilisant l'image `registry`.
   - Taguez les images `mysql`, `nginx` et `almalinux`, et poussez-les dans le registre privé `priv-registry`.
   - Utilisez les informations du tableau ci-dessous pour taguer et pousser les images.

   | Image     | Tag   | Registre Privé          |
   |-----------|-------|-------------------------|
   | mysql     | 5.7   | localhost:5000/mysql    |
   | nginx     | latest| localhost:5000/nginx    |
   | almalinux | latest| localhost:5000/almalinux|

   - Fournissez la commande pour que l'examinateur puisse charger les images depuis le registre privé et les utiliser.

6. **Exercice 5 : Tests de Connectivité**

   - Vérifiez la connectivité entre les conteneurs en utilisant des commandes `ping` à partir des conteneurs.
   - Les pings doivent réussir, indiquant que les conteneurs peuvent communiquer entre eux sur les réseaux configurés.

7. **Exercice 6 : Redémarrage des Conteneurs**

   - Arrêtez et redémarrez les conteneurs `db-container`, `app-container` et `web-container`.
   - Vérifiez que les données persistent après le redémarrage en accédant aux volumes configurés.

8. **Exercice 7 : Création de Conteneurs avec des Images depuis le Registre Privé**

   - Utilisez les images poussées dans le registre privé pour créer de nouveaux conteneurs. Utilisez les informations du tableau ci-dessous pour créer les conteneurs spécifiés.

   | Conteneur       | Image                        | Tag   | Nom              | Port Hôte | Port Conteneur |
   |-----------------|------------------------------|-------|------------------|-----------|----------------|
   | web-new         | localhost:5000/nginx         | latest| web-container-new| 8081      | 80             |
   | db-new          | localhost:5000/mysql         | 5.7   | db-container-new | 3307      | 3306           |
   | app-new         | localhost:5000/almalinux     | latest| app-container-new| 3001      | 80             |

   - Configurez les nouveaux conteneurs avec les volumes et les réseaux appropriés.

9. **Exercice 8 : Réécrire les configurations dans un docker-compose.yml**

   - Écrire un fichier `docker-compose.yml` qui inclut les configurations pour tous les conteneurs créés dans les exercices précédents.
   - Le fichier `docker-compose.yml` doit inclure la configuration des réseaux, des volumes, et des conteneurs.
   - Le fichier doit permettre de recréer l'intégralité de l'infrastructure en une seule commande.

### Critères d'Évaluation :
   - **Création de Conteneurs :** Les conteneurs `web-container`, `db-container`, `app-container`, `priv-registry`, `web-container-new`, `db-container-new` et `app-container-new` sont créés correctement et fonctionnent comme prévu.
   - **Configuration des Ports :** Les ports sont configurés correctement, permettant l'accès aux services sur les ports spécifiés.
   - **Création et Utilisation de Réseaux Docker :** Les réseaux Docker `frontend-network` et `backend-network` sont créés et les conteneurs y sont connectés correctement.
   - **Utilisation des Volumes Docker :** Les volumes sont configurés correctement, permettant la persistance des données.
   - **Utilisation d'un Registre Privé :** Les images sont taguées et poussées correctement dans le registre privé.
   - **Tests de Connectivité :** Les conteneurs peuvent communiquer entre eux via les réseaux configurés.
   - **Redémarrage des Conteneurs :** Les données persistent après le redémarrage des conteneurs grâce aux volumes configurés.
   - **Création de Conteneurs depuis le Registre Privé :** Les conteneurs créés à partir des images dans le registre privé fonctionnent correctement.
   - **Documentation :** Fournir un fichier README.md ou un document dans un format WORD ou PDF décrivant les étapes suivies pour chaque exercice, incluant les commandes utilisées et les vérifications effectuées.

### Bonnes pratiques à suivre :
- Utilisez des commandes claires et précises.
- Vérifiez chaque étape pour vous assurer que les conteneurs fonctionnent comme attendu.
- Documentez votre travail de manière détaillée.


