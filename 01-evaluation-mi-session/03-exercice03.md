# Exercice 3 : Utilisation de Dépôts Docker Hub Publics et Privés

# Objectif :
Évaluer votre capacité à créer, taguer, et pousser des images Docker vers des dépôts Docker Hub publics et privés, ainsi que vers un registre privé local. Comprendre la différence entre les registres publics et privés.

#### Durée :
1 heure

### Instructions :

1. **Préparation :**
   - Assurez-vous que Docker est installé et fonctionne correctement sur votre machine.
   - Connectez-vous à Docker Hub via la ligne de commande en utilisant `docker login`.

2. **Exercice 1 : Création de Conteneurs**

   Utilisez les informations du tableau ci-dessous pour créer les conteneurs spécifiés.

   | Conteneur       | Image        | Tag   | Nom              | Port Hôte | Port Conteneur |
   |-----------------|--------------|-------|------------------|-----------|----------------|
   | web             | nginx        | latest| web-container    | 8080      | 80             |
   | db              | mysql        | 5.7   | db-container     | 3306      | 3306           |
   | app             | almalinux    | latest| app-container    | 3000      | 80             |

   - Créez et lancez les conteneurs en utilisant les commandes appropriées.

3. **Exercice 2 : Taguer et Pousser des Images vers Docker Hub Public**

   - Créez un dépôt public sur Docker Hub pour chaque image (`nginx`, `mysql`, `almalinux`).
   - Taguez les images locales et poussez-les vers Docker Hub.

4. **Exercice 3 : Créer et Utiliser un Registre Privé Local**

   - Créez un registre privé local en utilisant l'image `registry`.
   - Taguez les images locales et poussez-les vers le registre privé local.

5. **Exercice 4 : Utilisation des Images depuis Docker Hub Public**

   - Utilisez les images poussées vers Docker Hub public pour créer de nouveaux conteneurs. Utilisez les informations du tableau ci-dessous pour créer les conteneurs spécifiés.

   | Conteneur       | Image                        | Tag   | Nom                      | Port Hôte | Port Conteneur |
   |-----------------|------------------------------|-------|--------------------------|-----------|----------------|
   | web-new-public  | <public-repo>/nginx-public   | latest| web-container-new-public | 8081      | 80             |
   | db-new-public   | <public-repo>/mysql-public   | 5.7   | db-container-new-public  | 3307      | 3306           |
   | app-new-public  | <public-repo>/almalinux-public | latest| app-container-new-public | 3001      | 80             |

   - Créez et lancez les conteneurs en utilisant les commandes appropriées.

6. **Exercice 5 : Utilisation des Images depuis le Registre Privé Local**

   - Utilisez les images poussées vers le registre privé local pour créer de nouveaux conteneurs. Utilisez les informations du tableau ci-dessous pour créer les conteneurs spécifiés.

   | Conteneur       | Image                              | Tag   | Nom                      | Port Hôte | Port Conteneur |
   |-----------------|------------------------------------|-------|--------------------------|-----------|----------------|
   | web-new-private | localhost:5000/nginx-private       | latest| web-container-new-private | 8082      | 80             |
   | db-new-private  | localhost:5000/mysql-private       | 5.7   | db-container-new-private | 3308      | 3306           |
   | app-new-private | localhost:5000/almalinux-private   | latest| app-container-new-private | 3002      | 80             |

   - Créez et lancez les conteneurs en utilisant les commandes appropriées.

7. **Exercice 6 : Conversion en Docker Compose**

   - Réécrivez la configuration des conteneurs, des réseaux et des volumes dans un fichier `docker-compose.yml`.

### Critères d'Évaluation :
   - **Création de Conteneurs :** Les conteneurs sont créés correctement et fonctionnent comme prévu.
   - **Taguer et Pousser des Images :** Les images sont correctement taguées et poussées vers Docker Hub et le registre privé local.
   - **Utilisation des Images depuis Docker Hub Public et le Registre Privé Local :** Les conteneurs créés à partir des images publiques et privées fonctionnent correctement.
   - **Conversion en Docker Compose :** La configuration Docker Compose est correcte et fonctionne comme attendu.
   - **Documentation :** Fournir un fichier README.md ou un document dans un format WORD ou PDF décrivant les étapes suivies pour chaque exercice, incluant les commandes utilisées et les vérifications effectuées.

### Bonnes pratiques à suivre :
- Utilisez des commandes claires et précises.
- Vérifiez chaque étape pour vous assurer que les conteneurs fonctionnent comme attendu.
- Documentez votre travail de manière détaillée.
