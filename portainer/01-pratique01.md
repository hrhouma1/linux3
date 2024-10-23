
### Définition de Portainer

**Portainer** est un outil open-source qui permet de gérer facilement des environnements Docker et Kubernetes via une interface utilisateur graphique (GUI). Il simplifie la gestion des conteneurs en fournissant une interface intuitive pour la création, l'administration, et la surveillance de conteneurs, volumes, réseaux et autres composants Docker. Plutôt que d'utiliser des lignes de commande complexes pour chaque opération, Portainer permet d'exécuter les tâches administratives plus efficacement et visuellement.

Il est particulièrement utile pour les équipes de développement ou les administrateurs système qui doivent superviser et maintenir des environnements Docker à grande échelle. Parmi ses fonctionnalités principales, on retrouve la gestion des stacks, des volumes, des images Docker, et la possibilité de visualiser l'utilisation des ressources.

### Analyse de l'image (Déploiement de Portainer sur un hôte Docker Linux autonome)

# Capture 1
![image](https://github.com/user-attachments/assets/b68246e5-0b33-4408-a5eb-b3093e0792c5)

Dans cette capture d'écran, on vous montre comment déployer **Portainer Server** sur un hôte Docker Linux autonome ou sur un cluster de nœuds Swarm (ou encore sur un hôte Docker sous Windows 10 fonctionnant en mode conteneurs Linux).

Voici les deux commandes Docker à utiliser pour le déploiement :

1. **Création d'un volume pour les données de Portainer :**

   ```bash
   docker volume create portainer_data
   ```

   - Cette commande crée un volume Docker nommé `portainer_data`. Les volumes Docker sont utilisés pour stocker des données persistantes. Ici, ce volume va stocker toutes les informations de configuration et les données nécessaires au fonctionnement de Portainer.
   - L'utilisation d'un volume permet de garantir que ces données seront préservées même si le conteneur de Portainer est supprimé ou redémarré.

2. **Exécution du conteneur Portainer :**

   ```bash
   docker run -d -p 8000:8000 -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
   ```

   Détaillons cette commande :
   
   - **`docker run`** : Lance un conteneur Docker.
   - **`-d`** : Démarre le conteneur en arrière-plan (mode détaché).
   - **`-p 8000:8000` et `-p 9000:9000`** : Ces options mappent les ports du conteneur aux ports de l'hôte Docker :
     - Le port **8000** est utilisé pour le tunnel Edge de Portainer.
     - Le port **9000** est l'interface principale de Portainer accessible via un navigateur web.
   - **`-v /var/run/docker.sock:/var/run/docker.sock`** : Monte le socket Docker de l'hôte dans le conteneur. Cela permet à Portainer d'interagir avec le moteur Docker local et de gérer les conteneurs directement.
   - **`-v portainer_data:/data`** : Monte le volume `portainer_data` dans le conteneur, sous le répertoire `/data`. Cela permet à Portainer de stocker ses données de manière persistante.
   - **`portainer/portainer`** : Spécifie l'image Docker de Portainer à utiliser.

### Fonctionnement

Une fois ces commandes exécutées, Portainer est déployé sur l'hôte Docker. Vous pouvez ensuite accéder à l'interface Web de Portainer via `http://<votre-ip>:9000` dans un navigateur pour commencer à gérer vos conteneurs Docker.

L'agent n'est pas nécessaire pour un hôte autonome (single-node), mais peut être utilisé dans un environnement distribué (multi-node Swarm) pour des fonctionnalités supplémentaires.



-

-----------

# Capture 2

![image](https://github.com/user-attachments/assets/0419ac85-9211-42b6-9c47-342a77df53f2)

Cette capture d'écran montre l'étape de création de l'utilisateur administrateur initial dans l'interface de Portainer. Une fois que vous avez déployé le conteneur Portainer via les commandes Docker, cette page apparaît pour configurer l'accès administrateur.

Détails de l'image :
1. **Nom d'utilisateur** : Le champ "Username" permet de définir un identifiant pour l'administrateur (par défaut, "admin").
2. **Mot de passe** : Vous devez définir un mot de passe sécurisé d'au moins 8 caractères.
3. **Confirmation du mot de passe** : Il est nécessaire de confirmer le mot de passe.

Cette étape est essentielle pour sécuriser l'accès à l'interface de gestion de Portainer. Une fois l'utilisateur créé, vous pourrez accéder à l'interface Web de Portainer à l'adresse [http://localhost:9000](http://localhost:9000) ou via l'adresse IP de votre serveur Docker. Depuis cette interface, vous aurez un contrôle complet sur vos conteneurs Docker, les volumes, les stacks, et plus encore.

Cela montre la simplicité d'utilisation de Portainer pour administrer visuellement votre environnement Docker.

------------------------
# Captures 3

![image](https://github.com/user-attachments/assets/f09ec36c-07b5-4087-a5e6-576e366bc4da)
![image](https://github.com/user-attachments/assets/6ca697d4-96a1-4dc6-bb40-d081c989b41a)


Ces captures d'écran montrent deux options différentes pour connecter **Portainer** à un environnement Docker : local ou distant.

### Première capture 3-1 : Connexion à un environnement Docker distant

1. **Option "Remote" (Gestion d'un environnement Docker distant)** :
   - Cette option permet de connecter Portainer à un environnement Docker situé sur une machine distante en utilisant l'API Docker via TCP.
   - **Nom de l'environnement** : Vous devez entrer un nom d'identification pour l'environnement Docker distant (par exemple, `docker-prod01`).
   - **URL du point de terminaison** : Entrez l'URL ou l'adresse IP du Docker distant (par exemple, `10.0.0.10:2375` ou `mydocker.mydomain.com:2375`).
   - **TLS** : Vous avez l'option d'activer ou non TLS (Transport Layer Security) pour sécuriser la connexion avec le Docker distant.
   - Une fois ces informations remplies, cliquez sur **Connect** pour lier Portainer à cet environnement.

Cette option est utile pour gérer des environnements Docker situés sur des serveurs distants ou dans le cloud. Il est important de noter que l'API Docker doit être exposée correctement sur le serveur distant pour que la connexion fonctionne.

### Deuxième capture 3-2 : Connexion à un environnement Docker local

1. **Option "Local" (Gestion de l'environnement Docker local)** :
   - Ici, vous gérez l'environnement Docker local où Portainer est déjà en cours d'exécution.
   - **Pré-requis** : Il est nécessaire de monter le socket Docker de l'hôte dans le conteneur Portainer. Cela est fait en spécifiant l'option suivante lors du démarrage du conteneur :
     - Pour Linux : `-v /var/run/docker.sock:/var/run/docker.sock`
     - Pour Windows : `-v \\.\pipe\docker_engine:\\.\pipe\docker_engine`
   - Une fois que le socket est monté correctement, cliquez sur **Connect** pour commencer à gérer l'environnement Docker local.

Cette option permet de gérer directement les conteneurs qui s'exécutent sur la même machine que Portainer. Cela facilite la gestion locale sans avoir besoin de configuration réseau particulière.

En résumé, ces deux options vous offrent la flexibilité de gérer à la fois des environnements Docker locaux et distants depuis la même interface Portainer, avec une configuration simple pour chaque cas.


# Capture 4
![image](https://github.com/user-attachments/assets/452f263b-3ae2-4018-b87d-52b7f4c20c03)

Cette capture d'écran montre l'interface principale de **Portainer** après la connexion à un environnement Docker. Voici ce que l'on peut observer :

### **Page d'accueil de Portainer (Home)**

1. **Endpoints** :
   - La section des points de terminaison (Endpoints) permet de visualiser et de gérer les différents environnements Docker connectés à Portainer.
   - Ici, un environnement Docker local est connecté, comme indiqué par l'étiquette "local".

2. **Statistiques de l'environnement Docker** :
   - **Stacks** : Il n'y a actuellement aucune stack déployée dans cet environnement.
   - **Containers** : Il y a 39 conteneurs au total, dont 20 sont en cours d'exécution (indiqués en vert), et 19 sont arrêtés (indiqués en rouge).
   - **Volumes** : 5 volumes sont disponibles dans cet environnement Docker.
   - **Images** : 21 images Docker sont présentes dans l'environnement.

3. **Informations supplémentaires** :
   - Le Docker est configuré en tant qu'instance autonome ("Standalone 19.03.12"), utilisant le socket `/var/run/docker.sock` pour l'accès.

4. **Groupes et Tags** :
   - Aucune étiquette ou groupe spécifique n'a été assigné à cet environnement Docker ("Group: Unassigned").

### Actions possibles :
- Vous pouvez **actualiser** les données affichées en cliquant sur le bouton **Refresh**.
- Une barre de recherche permet de rechercher des conteneurs, des groupes, ou des volumes par nom, groupe, tag, statut, ou URL.

En résumé, cette interface donne une vue d'ensemble de l'état de l'environnement Docker connecté et permet d'accéder rapidement à la gestion des conteneurs, volumes, et images. C'est ici que vous pouvez commencer à administrer et superviser les ressources Docker via Portainer.

# Capture 5
![image](https://github.com/user-attachments/assets/1ecdeeda-e295-4bf8-af53-4c554daa639d)

Cette capture d'écran présente la **liste des conteneurs** dans l'interface de gestion de Portainer. Elle montre les conteneurs Docker actifs et leur état.

### Détails de la liste des conteneurs :

1. **Tableau des conteneurs** :
   - Chaque ligne représente un conteneur Docker avec des informations clés comme :
     - **Nom** : Le nom unique du conteneur (ex. `nostalgic_roentgen`, `flask-hello`).
     - **État** : L'état actuel du conteneur, qui est ici **running** (en cours d'exécution).
     - **Image** : L'image Docker utilisée pour créer ce conteneur (ex. `portainer/portainer`, `flask-hello-latest`).
     - **Créé** : La date et l'heure de création du conteneur.
     - **Adresse IP** : L'adresse IP attribuée au conteneur par Docker.
   
2. **Actions rapides (Quick actions)** :
   - Pour chaque conteneur, vous avez plusieurs actions rapides disponibles via des icônes :
     - **Console** : Accès à une console directement dans le conteneur.
     - **Statistiques** : Voir les statistiques d'utilisation des ressources du conteneur (CPU, mémoire, etc.).
     - **Logs** : Afficher les journaux de ce conteneur pour le débogage ou la surveillance.
     - **Inspecter** : Voir les détails approfondis du conteneur.

3. **Commandes principales** :
   - En haut de la liste, vous avez des boutons pour gérer l'état des conteneurs sélectionnés :
     - **Start** : Démarrer un conteneur arrêté.
     - **Stop** : Arrêter un conteneur en cours d'exécution.
     - **Kill** : Forcer l'arrêt d'un conteneur.
     - **Restart** : Redémarrer un conteneur.
     - **Pause/Resume** : Suspendre ou reprendre un conteneur.
     - **Remove** : Supprimer un conteneur.
     - **Add container** : Ajouter un nouveau conteneur Docker via une interface utilisateur.

4. **Fonction de recherche** :
   - Une barre de recherche permet de trouver rapidement un conteneur par nom, image, ou autre critère.

### Utilité :
Cette vue est particulièrement pratique pour avoir un aperçu global des conteneurs Docker déployés et de leur état. Vous pouvez facilement gérer, surveiller et effectuer des opérations sur chaque conteneur via cette interface intuitive. Cela simplifie grandement la gestion des conteneurs, surtout dans des environnements où plusieurs conteneurs sont en cours d'exécution.

# Capture 6
![image](https://github.com/user-attachments/assets/39d6cc08-5772-40c4-a251-c5d50baf8731)

Cette capture d'écran montre l'interface de création d'un nouveau conteneur dans **Portainer**.

### Détails de la création d'un conteneur :

1. **Nom du conteneur** :
   - **Name** : Le nom du conteneur est défini comme `flask-hello`.

2. **Configuration de l'image** :
   - **Image** : Le champ image montre que le conteneur sera créé à partir de l'image `pythonincontainers/flask-hello:latest` provenant du registre **DockerHub**.
   - **Always pull the image** : Cette option est activée, ce qui signifie que Portainer va toujours récupérer la dernière version de l'image depuis DockerHub avant de créer le conteneur.

3. **Configuration des ports réseau** :
   - **Publish all exposed network ports to random host ports** : Cette option est activée, ce qui signifie que tous les ports exposés par le conteneur seront mappés aléatoirement sur des ports de l'hôte Docker.
   - **Manual network port publishing** : Cette option permet de spécifier manuellement les ports réseau à publier, mais elle est désactivée ici.

4. **Contrôle d'accès** :
   - **Enable access control** : Cette option est activée, ce qui permet de restreindre l'accès à la gestion du conteneur.
   - Deux choix sont proposés :
     - **Administrators** : Restreindre la gestion de cette ressource aux administrateurs uniquement (option sélectionnée).
     - **Restricted** : Restreindre la gestion à un ensemble d'utilisateurs spécifiques.

5. **Actions supplémentaires** :
   - **Auto remove** : Cette option, lorsqu'elle est activée, permet de supprimer automatiquement le conteneur après son arrêt.
   - **Deploy the container** : Ce bouton permet de lancer le déploiement du conteneur avec les paramètres spécifiés.

### Fonctionnement :
Après avoir configuré tous les paramètres souhaités, vous pouvez cliquer sur **Deploy the container** pour créer et démarrer le conteneur basé sur l'image sélectionnée. Vous aurez alors accès à ce conteneur via Portainer pour le gérer, le surveiller, et interagir avec lui, notamment pour consulter les journaux ou accéder à la console du conteneur.

Cet écran montre à quel point Portainer simplifie la création de conteneurs Docker via une interface intuitive, rendant la configuration des ports, des images, et des règles de sécurité plus accessible et facile à gérer sans avoir recours à la ligne de commande.

# Capture 7
![image](https://github.com/user-attachments/assets/47a3030a-ca2f-49a2-be36-24e97928c915)

Cette capture d'écran montre les **paramètres avancés** de configuration réseau pour un conteneur dans Portainer.

### Détails des paramètres avancés du conteneur (section **Network**) :

1. **Network (Réseau)** :
   - Vous pouvez choisir le type de réseau Docker à utiliser pour le conteneur. Ici, le réseau sélectionné est `bridge`, qui est le réseau par défaut pour les conteneurs Docker.

2. **Hostname** :
   - Le nom d'hôte du conteneur peut être spécifié ici. Par exemple, vous pouvez entrer `web01` pour assigner un nom d'hôte spécifique au conteneur.

3. **Domain Name** :
   - Vous pouvez également définir un domaine, comme `example.com`, si le conteneur fait partie d'une infrastructure plus large nécessitant une gestion de domaine.

4. **Mac Address** :
   - Il est possible d'attribuer manuellement une adresse MAC au conteneur. Cela est généralement utilisé pour des configurations réseau spécifiques où le conteneur doit avoir une adresse MAC prédéfinie.

5. **IPv4 Address** :
   - Si vous voulez attribuer une adresse IPv4 statique à votre conteneur sur un réseau spécifique, vous pouvez la définir ici (par exemple, `172.20.0.7`).

6. **IPv6 Address** :
   - De même, vous pouvez attribuer une adresse IPv6 statique si votre infrastructure le nécessite.

7. **Hosts file entries** :
   - Vous avez la possibilité d'ajouter manuellement des entrées supplémentaires au fichier `hosts` du conteneur, ce qui peut être utile pour la résolution de noms personnalisés à l'intérieur du conteneur.

### Fonctionnement :
Ces paramètres avancés vous permettent de configurer la façon dont votre conteneur interagit avec le réseau. Si vous avez besoin d'une gestion fine des réseaux Docker, comme le choix d'un réseau spécifique, l'attribution d'adresses IP statiques, ou des ajustements au fichier `hosts`, cette interface vous permet de le faire facilement via Portainer.

Une fois ces configurations définies, vous pouvez cliquer sur **Deploy the container** pour démarrer votre conteneur avec les paramètres réseau spécifiques que vous avez configurés. Ces options sont particulièrement utiles dans des environnements complexes où des configurations réseau précises sont requises.

# Capture 8
![image](https://github.com/user-attachments/assets/9735e0ce-f220-48aa-a6ba-f3fcdebf4ec1)

Cette capture d'écran montre l'onglet **Env** dans les **paramètres avancés** lors de la création d'un conteneur dans Portainer.

### Détails des paramètres avancés du conteneur (section **Env**) :

1. **Environment variables (Variables d'environnement)** :
   - Cette section vous permet d'ajouter des variables d'environnement spécifiques au conteneur.
   - Les variables d'environnement sont souvent utilisées pour configurer le comportement d'une application à l'intérieur d'un conteneur sans avoir besoin de modifier l'image ou le code de l'application.
   - Vous pouvez ajouter une nouvelle variable en cliquant sur **add environment variable**. Par exemple, vous pourriez définir des variables comme `ENV=production` ou configurer une clé d'API avec `API_KEY=xxxxxx`.

### Fonctionnement :
L'ajout de variables d'environnement est un moyen flexible de configurer dynamiquement des applications dans des conteneurs Docker. Cela vous permet de contrôler les paramètres de l'application directement depuis l'interface Portainer, sans avoir à reconstruire ou à modifier l'image Docker elle-même.

Une fois les variables ajoutées, elles seront appliquées lors du démarrage du conteneur. Comme pour les autres paramètres avancés, vous pouvez cliquer sur **Deploy the container** une fois que toutes vos configurations sont complètes, afin de lancer le conteneur avec les variables d'environnement et les autres paramètres définis.

Cette section est particulièrement utile dans les environnements où vous devez déployer la même image avec différentes configurations selon les environnements (développement, production, etc.).

# Capture 9
![image](https://github.com/user-attachments/assets/8f8549d2-f3ff-4391-ad5f-2a6c5202ff5d)

Cette capture d'écran montre l'onglet **Restart policy** dans les **paramètres avancés** lors de la création d'un conteneur dans Portainer.

### Détails des paramètres avancés du conteneur (section **Restart policy**) :

1. **Restart policy (Politique de redémarrage)** :
   - Ce paramètre détermine comment Docker gère les redémarrages du conteneur dans différentes situations. Plusieurs options sont disponibles :
     - **Never** : Le conteneur ne sera jamais redémarré automatiquement, même s'il plante.
     - **Always** : Le conteneur sera toujours redémarré automatiquement, quelle que soit la raison de l'arrêt (crash ou arrêt manuel).
     - **On failure** : Le conteneur sera redémarré uniquement s'il plante. C'est utile pour éviter un redémarrage en boucle après un arrêt manuel.
     - **Unless stopped** : Le conteneur sera redémarré automatiquement à chaque fois que Docker démarre, sauf si vous l'avez manuellement arrêté.

### Fonctionnement :
En définissant une politique de redémarrage appropriée, vous pouvez contrôler le comportement de votre conteneur après un échec ou un redémarrage du système. Par exemple, dans un environnement de production, il est souvent recommandé d'utiliser la politique **On failure** pour s'assurer que les conteneurs redémarrent automatiquement en cas de crash.

Une fois la politique de redémarrage sélectionnée et les autres configurations du conteneur définies, vous pouvez cliquer sur **Deploy the container** pour démarrer le conteneur avec les paramètres spécifiés.

Cette option est essentielle pour garantir la résilience et la continuité des services, en particulier dans les environnements critiques où un arrêt inattendu ne doit pas interrompre les opérations du conteneur.

# Capture 10-1
![image](https://github.com/user-attachments/assets/9239f7d2-c386-4aff-8a59-4d17f597de8e)

Cette capture d'écran montre l'onglet **Runtime & Resources** dans les **paramètres avancés** lors de la création d'un conteneur dans Portainer, spécifiquement la section des **capacités du conteneur**.

### Détails des paramètres avancés du conteneur (section **Container capabilities**) :

1. **Container capabilities (Capacités du conteneur)** :
   - Ces options permettent de définir les capacités du conteneur en termes de permissions pour accéder à certaines fonctionnalités du noyau et des ressources système. Il s'agit de capacités qui déterminent ce que le conteneur est autorisé à faire.
   - Voici quelques capacités que vous pouvez activer ou désactiver :
     - **AUDIT_CONTROL** : Contrôle des paramètres du système d'audit.
     - **CHOWN** : Permet de changer les propriétaires des fichiers.
     - **KILL** : Permet au processus de tuer d'autres processus.
     - **NET_RAW** : Permet l'utilisation de sockets RAW et d'autres opérations réseau de bas niveau.
     - **SYS_ADMIN** : Autorise certaines opérations administratives du système.
     - **SYS_TIME** : Permet de modifier l'heure du système.

   - Les capacités activées sont représentées par des boutons bleus, et celles désactivées sont en gris. Vous pouvez activer ou désactiver chaque capacité individuellement, selon les besoins spécifiques de votre application et du niveau de sécurité requis.

### Fonctionnement :
Ces capacités permettent d'affiner les permissions et les accès que votre conteneur aura sur le système hôte. Par défaut, Docker applique une politique de sécurité qui désactive certaines capacités sensibles pour éviter que des conteneurs n'interagissent directement avec des composants critiques du noyau. Cependant, vous pouvez ajuster ces capacités pour donner plus de flexibilité à votre conteneur, si nécessaire pour des cas d'utilisation particuliers (par exemple, des conteneurs qui nécessitent un accès aux configurations réseau ou à la gestion des fichiers système).

Une fois que vous avez configuré les capacités du conteneur, vous pouvez cliquer sur **Deploy the container** pour lancer le conteneur avec ces permissions spécifiques.

Cette section est essentielle pour ajuster les autorisations d'un conteneur en fonction de son rôle dans l'infrastructure, tout en respectant les bonnes pratiques de sécurité.


# Capture 10-2
![image](https://github.com/user-attachments/assets/cd593414-e614-45db-87c1-f785fcbfde7c)

Cette capture d'écran présente à nouveau la **liste des conteneurs** dans Portainer, similaire à celle que vous avez déjà fournie plus tôt, mais avec des détails supplémentaires ou mis à jour.

### Détails de la liste des conteneurs :

1. **Tableau des conteneurs** :
   - Les conteneurs Docker actuellement en cours d'exécution sont listés ici avec des informations importantes comme :
     - **Nom** : Le nom unique de chaque conteneur (par exemple, `flask-hello`, `nostalgic_roentgen`).
     - **État** : L'état actuel de chaque conteneur. Tous sont en état **running** (indiqué par le libellé vert).
     - **Image** : L'image Docker utilisée pour démarrer chaque conteneur (ex. `pythonincontainers/flask-hello:latest` et `portainer/portainer`).
     - **Créé** : La date et l'heure de création des conteneurs.
     - **Adresse IP** : Les adresses IP attribuées à chaque conteneur par Docker.

2. **Actions rapides (Quick actions)** :
   - Pour chaque conteneur, plusieurs actions rapides sont disponibles via les icônes :
     - **Console** : Ouvrir une console à l'intérieur du conteneur.
     - **Statistiques** : Afficher les statistiques d'utilisation (CPU, mémoire, etc.) du conteneur.
     - **Logs** : Accéder aux journaux pour surveiller l'activité ou diagnostiquer des problèmes.
     - **Inspecter** : Voir les détails techniques et la configuration du conteneur.

3. **Commandes principales** :
   - En haut de la liste, plusieurs boutons permettent de gérer l'état des conteneurs sélectionnés :
     - **Start** : Démarrer les conteneurs arrêtés.
     - **Stop** : Arrêter un ou plusieurs conteneurs en cours d'exécution.
     - **Kill** : Forcer l'arrêt d'un conteneur immédiatement.
     - **Restart** : Redémarrer un conteneur.
     - **Pause/Resume** : Mettre en pause ou reprendre l'exécution d'un conteneur.
     - **Remove** : Supprimer un conteneur arrêté.
     - **Add container** : Créer un nouveau conteneur.

4. **Barre de recherche** :
   - Une barre de recherche permet de trouver rapidement un conteneur en filtrant par nom, stack, ou autres critères.

### Utilité :
Cette interface permet d'avoir un aperçu rapide et global des conteneurs déployés et de leur état de fonctionnement. Vous pouvez facilement démarrer, arrêter, surveiller ou gérer les conteneurs directement à partir de cette vue. Cela rend la gestion des conteneurs Docker plus intuitive et accessible, en particulier pour ceux qui préfèrent ne pas utiliser la ligne de commande.

# Capture 11
![image](https://github.com/user-attachments/assets/e5f77ae6-3658-4593-90f9-5e4d9fe8573a)

Cette capture d'écran montre les détails d'un conteneur Docker dans **Portainer**, une fois déployé. Elle fournit des informations clés sur la configuration du conteneur, notamment l'image utilisée, les variables d'environnement, la configuration des ports, et les réseaux auxquels le conteneur est connecté.

### Détails du conteneur :

1. **Image** :
   - Le conteneur utilise l'image `pythonincontainers/flask-hello:latest`. Cette image est identifiée de manière unique avec un hash SHA256, qui garantit l'intégrité de l'image.

2. **Configuration des ports** :
   - Le conteneur expose le port **5000/tcp** sur l'IP **0.0.0.0** avec un port hôte mappé sur **32772**. Cela signifie que l'application Flask à l'intérieur du conteneur est accessible sur le port 5000 et est mappée au port 32772 de l'hôte Docker.

3. **CMD (Commande)** :
   - La commande exécutée lors du démarrage du conteneur est `/bin/sh -c python flask-hello.py`. Cette commande lance le serveur Flask.

4. **Variables d'environnement (ENV)** :
   - Plusieurs variables d'environnement sont définies pour configurer le comportement de l'application à l'intérieur du conteneur :
     - **PATH** : Spécifie les répertoires où les binaires système sont recherchés.
     - **LANG** : Définit la langue du conteneur en UTF-8.
     - **GPG_KEY** : Une clé GPG utilisée pour des vérifications.
     - **PYTHON_VERSION** : Version de Python (3.7.4).
     - **PYTHON_PIP_VERSION** : Version de `pip` (19.2.2).
     - **PYTHON_GET_PIP_URL** : URL pour obtenir `pip`.
     - **FLASK_DEBUG** : Débogage activé (`True`), ce qui peut être utile lors du développement pour avoir plus de logs détaillés.

5. **Politiques de redémarrage (Restart policies)** :
   - Ici, aucune politique de redémarrage n'a été définie. Vous pouvez en choisir une si nécessaire pour gérer le comportement du conteneur en cas de plantage ou d'arrêt.

6. **Réseaux connectés** :
   - Le conteneur est connecté au réseau **bridge** avec l'adresse IP **172.17.0.2** et la passerelle **172.17.0.1**.
   - Vous avez également la possibilité de rejoindre un autre réseau en le sélectionnant dans la liste déroulante et en cliquant sur **Join network**.

### Fonctionnement :
Cette vue vous permet de consulter les détails du conteneur une fois qu'il est en cours d'exécution. Vous pouvez facilement voir comment le conteneur est configuré, quelles variables d'environnement sont utilisées, et à quel réseau il est connecté. Vous pouvez également quitter un réseau en cliquant sur **Leave network** si le conteneur n'a plus besoin d'y être connecté.

Ces informations sont cruciales pour comprendre le fonctionnement du conteneur et effectuer des ajustements si nécessaire.

# Capture 12-1
![image](https://github.com/user-attachments/assets/49486a67-cdae-494a-b29e-f8b6107b821c)

Cette capture d'écran montre les **statistiques en temps réel** d'un conteneur Docker dans Portainer, plus spécifiquement pour le conteneur nommé **flask-hello**.

### Détails des statistiques du conteneur :

1. **Taux de rafraîchissement (Refresh rate)** :
   - La fréquence de mise à jour des statistiques est définie ici à **5 secondes**. Cela signifie que les données seront automatiquement actualisées toutes les 5 secondes pour refléter les ressources utilisées par le conteneur.

2. **Utilisation de la mémoire (Memory usage)** :
   - Ce graphique montre la quantité de mémoire utilisée par le conteneur, divisée en **Memory** (mémoire utilisée activement) et **Cache** (mémoire mise en cache).
   - Ici, le conteneur utilise environ **40 MB** de mémoire, avec une distinction claire entre la mémoire active et celle en cache.

3. **Utilisation du CPU (CPU usage)** :
   - Ce graphique affiche le pourcentage de **CPU** utilisé par le conteneur. Dans cette capture, l'utilisation du CPU est relativement faible, oscillant autour de **2%**.

4. **Utilisation du réseau (Network usage)** :
   - Le graphique **Network usage** montre les données entrantes et sortantes (en réception **RX** sur l'interface réseau **eth0**) pour le conteneur.
   - Ici, le trafic réseau semble faible avec moins de **1 KB** utilisé, ce qui est typique pour un conteneur simple comme une petite application Flask.

5. **Liste des processus (Processes)** :
   - La section **Processes** liste les processus actifs à l'intérieur du conteneur. Chaque processus est identifié par :
     - **PID** : L'identifiant du processus (ex. `61026`).
     - **USER** : L'utilisateur sous lequel le processus s'exécute (ex. `root`).
     - **TIME** : Le temps CPU utilisé par chaque processus.
     - **COMMAND** : La commande ou le script exécuté. Ici, les processus exécutent des commandes liées à l'application Flask (par exemple, `/bin/sh -c python flask-hello.py` et `python flask-hello.py`).

### Fonctionnement :
Cette interface vous permet de surveiller en temps réel les ressources consommées par le conteneur, telles que la mémoire, le CPU et le réseau. Cela est particulièrement utile pour identifier les goulets d'étranglement ou comprendre comment l'application utilise les ressources du système.

En bas, la liste des processus vous permet d'obtenir un aperçu des processus en cours à l'intérieur du conteneur, ce qui est utile pour déboguer et diagnostiquer des problèmes de performance ou des processus inattendus.

Ces informations sont essentielles pour la gestion efficace d'un conteneur en production, permettant de surveiller la santé du conteneur et d'optimiser l'utilisation des ressources.

# Capture 12-2
![image](https://github.com/user-attachments/assets/c38e4cce-66c7-47dc-b59e-17f55f4417cc)

Cette capture d'écran montre la section des **logs du conteneur** dans Portainer, où vous pouvez consulter les journaux générés par le conteneur en temps réel.

### Détails des paramètres de visualisation des logs :

1. **Auto-refresh logs (Rafraîchissement automatique des logs)** :
   - Cette option est activée, ce qui signifie que les journaux sont automatiquement mis à jour pour afficher les nouvelles entrées au fur et à mesure qu'elles sont générées par le conteneur.

2. **Wrap lines (Retour à la ligne)** :
   - L'option est activée, ce qui permet d'afficher les lignes longues sur plusieurs lignes afin qu'elles soient lisibles sans avoir à faire défiler horizontalement.

3. **Display timestamps (Afficher les horodatages)** :
   - Cette option est désactivée ici, mais elle permettrait d'ajouter des horodatages à chaque ligne des journaux pour savoir à quel moment chaque entrée a été générée.

4. **Fetch (Récupération des logs)** :
   - Vous pouvez choisir de récupérer **All logs** (tous les journaux) ou filtrer selon des critères spécifiques pour afficher seulement une partie des journaux générés.

5. **Search (Rechercher)** :
   - Un champ de recherche permet de filtrer les journaux en fonction de mots-clés ou de chaînes spécifiques, facilitant ainsi l'exploration de grandes quantités de données de logs.

6. **Lines (Nombre de lignes)** :
   - Vous pouvez spécifier combien de lignes de journaux vous souhaitez voir à la fois. Ici, il est défini à **100** lignes.

7. **Actions** :
   - **Copy** : Permet de copier l'intégralité des journaux visibles.
   - **Copy selected lines** : Vous pouvez sélectionner des lignes spécifiques et les copier.
   - **Unselect** : Permet de désélectionner toutes les lignes.

### Contenu des logs affichés :
Dans la partie inférieure de l'écran, vous voyez un extrait des journaux du conteneur Flask, qui inclut des informations sur le serveur Flask en cours d'exécution :
- **Serving Flask app "flask-hello"** : Le conteneur exécute l'application Flask.
- **Environment: production** : L'application est exécutée en mode production.
- **WARNING** : Avertissement indiquant que ce serveur de développement ne doit pas être utilisé en production.
- **Debug mode: off** : Le mode de débogage est désactivé.
- **Running on http://0.0.0.0:5000/** : L'application est accessible sur le port 5000.

### Utilité :
Cette interface est essentielle pour surveiller l'activité du conteneur en temps réel, identifier les erreurs, et comprendre comment l'application fonctionne à l'intérieur du conteneur. Vous pouvez facilement copier les informations importantes ou rechercher des erreurs spécifiques grâce aux options de filtrage et de recherche.

Ces journaux sont particulièrement utiles pour déboguer les applications, diagnostiquer des problèmes de performance ou de connectivité, et assurer une bonne gestion des services dans un environnement de production ou de développement.

# Capture 13
![image](https://github.com/user-attachments/assets/1df16e76-651e-458e-9eed-6abc38db57e6)

Cette capture d'écran montre à nouveau la section des **logs du conteneur** dans Portainer, similaire à la capture précédente. Voici les détails de ce que l'on peut observer :

### Paramètres de visualisation des logs :

1. **Auto-refresh logs (Rafraîchissement automatique des logs)** :
   - Cette option est activée, ce qui signifie que les journaux sont continuellement mis à jour pour refléter les nouvelles entrées en temps réel.

2. **Wrap lines (Retour à la ligne)** :
   - L'option de retour à la ligne est désactivée, donc les lignes longues ne sont pas automatiquement découpées pour s'adapter à la largeur de l'affichage. Si une ligne dépasse la largeur de la fenêtre, il faudra faire défiler horizontalement pour la lire.

3. **Display timestamps (Afficher les horodatages)** :
   - Cette option est désactivée, donc les journaux ne montrent pas d'horodatages pour chaque entrée.

4. **Fetch (Récupération des logs)** :
   - Cette option est réglée sur **All logs**, ce qui signifie que tous les journaux disponibles sont récupérés sans filtrage.

5. **Search (Rechercher)** :
   - Un champ de recherche permet de filtrer les journaux en fonction de mots-clés ou de chaînes spécifiques.

6. **Lines (Nombre de lignes)** :
   - Ce paramètre est défini à **100**, ce qui signifie que les 100 premières lignes de journaux sont affichées.

7. **Actions** :
   - **Copy** : Vous pouvez copier l'intégralité des journaux visibles.
   - **Copy selected lines** : Vous pouvez sélectionner des lignes spécifiques à copier.
   - **Unselect** : Permet de désélectionner toutes les lignes sélectionnées.

### Contenu des logs :
Dans cette section, vous voyez les journaux de démarrage de l'application Flask :
- **Serving Flask app "flask-hello"** : L'application Flask est en cours de service.
- **Environment: production** : L'application est en mode production.
- **WARNING** : Il y a un avertissement indiquant que ce serveur de développement ne doit pas être utilisé en production. Un serveur WSGI de production est recommandé.
- **Debug mode: off** : Le mode de débogage est désactivé.
- **Running on http://0.0.0.0:5000/** : L'application est accessible à cette adresse et sur le port 5000.

### Utilité :
Cette interface est un outil essentiel pour surveiller les activités du conteneur, en particulier lorsque vous devez diagnostiquer des erreurs ou surveiller des événements spécifiques dans l'application. Les options de copie et de filtrage facilitent également le travail des administrateurs en leur permettant de rapidement identifier et extraire les informations pertinentes des journaux.

Vous pouvez utiliser cette interface pour observer l'activité en temps réel ou examiner les journaux historiques pour mieux comprendre les performances de l'application.

# Capture 14
![image](https://github.com/user-attachments/assets/abfd7faa-fcf8-4dd6-8fd7-2c49e49cd13d)

Cette capture d'écran montre les détails d'inspection d'un conteneur Docker dans Portainer, offrant une vue détaillée de la configuration du conteneur **flask-hello**.

### Détails de l'inspection du conteneur :

1. **AppArmorProfile** :
   - Le conteneur utilise un profil **AppArmor**, une solution de sécurité qui permet de restreindre les actions qu'un programme peut exécuter, mais ici, aucun profil spécifique n'est assigné (valeur vide).

2. **Args** :
   - La commande exécutée lors du démarrage du conteneur est `/bin/sh -c python flask-hello.py`. Cela lance le serveur Flask.

3. **Config** :
   - **AttachStdin**, **AttachStdout**, et **AttachStderr** sont désactivés, ce qui signifie que les flux d'entrée/sortie/erreur ne sont pas attachés à la console.
   - **Cmd** : La commande principale exécutée par le conteneur est `/bin/sh -c python flask-hello.py`.
   - Le conteneur a été créé le **2019-08-19** à **11:42:58** UTC.

4. **Driver** :
   - Le conteneur utilise le pilote de stockage **overlay2**, qui est le pilote par défaut pour Docker et est optimisé pour les performances.

5. **GraphDriver** :
   - Les informations ici détaillent le type de système de fichiers et le backend de stockage utilisé pour les fichiers du conteneur.

6. **HostConfig** :
   - Cela montre comment le conteneur est configuré par rapport à son hôte. Des informations sur les chemins comme :
     - **HostnamePath**, **HostsPath**, **ResolvConfPath** : Spécifient où se trouvent les fichiers système tels que le nom d'hôte, le fichier hosts et la configuration DNS à l'intérieur du conteneur.
     - **LogPath** : Chemin vers les fichiers de logs du conteneur.
   - La configuration des ports montre que le conteneur est lié à l'hôte sur un port spécifique.

7. **Mounts** :
   - Cette section décrit les volumes montés dans le conteneur. Ici, il semble qu'aucun volume persistant externe ne soit monté dans ce conteneur particulier.

8. **Networks** :
   - Le conteneur est connecté au réseau **bridge**, le réseau par défaut utilisé par Docker. Le conteneur exécute la commande dans l'environnement `/bin/sh`.

9. **Portainer** :
   - Les détails incluent le chemin de résolution DNS et l'état actuel du conteneur.

10. **State** :
   - Montre l'état actuel du conteneur. Ici, il est précisé si le conteneur est en cours d'exécution, s'il est arrêté ou s'il y a eu des erreurs.
   - Le conteneur est dans un état "Running" avec des informations sur le dernier redémarrage et sur les erreurs éventuelles qui ont pu survenir.

### Utilité :
Cette page d'inspection est cruciale pour obtenir une vue complète de la configuration et du statut d'un conteneur Docker. Elle fournit des informations détaillées sur les commandes exécutées, les chemins de fichiers critiques, les volumes, les réseaux, et l'état du conteneur. C'est un excellent outil pour le diagnostic et la résolution des problèmes, permettant d'inspecter chaque aspect technique du conteneur.

En cas de problème de performance ou d'anomalies dans le comportement du conteneur, ces informations permettent de comprendre ce qui pourrait être mal configuré ou nécessiter une modification.

# Capture 15
![image](https://github.com/user-attachments/assets/29557d79-5448-4b0e-a828-068b4a7ae8c7)

Cette capture d'écran montre la **liste des conteneurs** dans l'interface Portainer, où plusieurs conteneurs Docker sont en cours d'exécution.

### Détails de la liste des conteneurs :

1. **Tableau des conteneurs** :
   - Chaque conteneur est répertorié avec des informations clés :
     - **Name** : Le nom unique du conteneur (ex. `flask-hello`, `nostalgic_roentgen`).
     - **State** : L'état actuel du conteneur est affiché comme **running** (en cours d'exécution), représenté en vert.
     - **Quick actions** : Des actions rapides comme :
       - **Console** : Accès à une console shell à l'intérieur du conteneur.
       - **Stats** : Voir les statistiques d'utilisation des ressources du conteneur (CPU, mémoire, etc.).
       - **Logs** : Afficher les journaux du conteneur pour suivre l'activité.
       - **Inspect** : Obtenir des détails approfondis sur le conteneur (configuration, réseau, volumes, etc.).
     - **Stack** : Si le conteneur fait partie d'une stack Docker, celle-ci sera affichée ici.
     - **Image** : L'image Docker utilisée pour créer le conteneur (ex. `pythonincontainers/flask-hello:latest`).
     - **Created** : La date et l'heure de création du conteneur.

2. **Actions principales** :
   - Au-dessus de la liste des conteneurs, des boutons permettent de gérer l'état des conteneurs sélectionnés :
     - **Start** : Démarrer un ou plusieurs conteneurs arrêtés.
     - **Stop** : Arrêter un conteneur en cours d'exécution.
     - **Kill** : Forcer l'arrêt immédiat d'un conteneur.
     - **Restart** : Redémarrer un conteneur.
     - **Pause** et **Resume** : Suspendre ou reprendre un conteneur.
     - **Remove** : Supprimer un conteneur.
     - **Add container** : Ajouter un nouveau conteneur Docker.

3. **Barre de recherche** :
   - Un champ de recherche est disponible pour filtrer rapidement les conteneurs par nom ou d'autres critères.

### Utilité :
Cette vue offre une gestion simple et rapide de tous les conteneurs Docker actifs dans l'environnement. Vous pouvez voir en un coup d'œil l'état de chaque conteneur, accéder à ses logs, consulter les statistiques ou entrer dans la console du conteneur pour des tâches spécifiques. Cela permet une administration efficace des conteneurs directement via l'interface Portainer sans avoir besoin de passer par la ligne de commande Docker.

En utilisant les actions rapides ou en filtrant les conteneurs, il est facile de gérer plusieurs conteneurs dans des environnements complexes avec un minimum d'effort.

# Capture 16
![image](https://github.com/user-attachments/assets/7613e32d-7904-4d86-a10f-b9e66087fe4e)

Cette capture d'écran montre une erreur de connexion à l'adresse IP **0.0.0.0:32772**, avec un message d'erreur **ERR_ADDRESS_INVALID**, indiquant que l'adresse spécifiée est invalide ou que le site ne peut pas être atteint.

### Solution proposée :
Le texte suggère de remplacer l'adresse **0.0.0.0** par **localhost** dans la barre d'adresse du navigateur.

### Détails de l'erreur :

- **Problème** : L'adresse **0.0.0.0** n'est pas utilisable directement dans un navigateur web. Cette adresse est utilisée dans la configuration réseau pour signifier "toutes les adresses IP disponibles", mais elle n'est pas résoluble en une adresse réseau spécifique.
- **Cause** : Dans ce cas, le service Docker exposé sur le port **32772** est disponible sur l'adresse IP de la machine hôte, mais pas sur **0.0.0.0**.

### Résolution :

- Il est suggéré de changer l'adresse **0.0.0.0** en **localhost**. Ainsi, la nouvelle URL serait :
  ```
  http://localhost:32772
  ```
  Cela permet d'accéder au service local sur la machine hôte via son adresse **localhost**, qui est l'adresse IP **127.0.0.1** utilisée pour représenter la machine locale.

### Conclusion :
Ce message d'erreur est un rappel que **0.0.0.0** n'est pas une adresse valide pour accéder à un service dans un navigateur. En remplaçant par **localhost**, vous pouvez accéder correctement au service web qui fonctionne sur la machine locale.

# Capture 17
![image](https://github.com/user-attachments/assets/1ed5e458-b919-4f29-b0d2-8ca354e2cd41)

# Capture 18
![image](https://github.com/user-attachments/assets/72e9d499-524d-495b-9edd-c4fec555a00d)

# Capture 19
![image](https://github.com/user-attachments/assets/758b64db-b479-4129-9162-369338157eda)

# Capture 20
![image](https://github.com/user-attachments/assets/44469646-3761-4e07-939f-c83f6cb809b4)


# Capture 21 - barre de recherche 
![image](https://github.com/user-attachments/assets/73c0cc9d-f0d0-4727-b7ff-97928bd383aa)

# Capture 22
![image](https://github.com/user-attachments/assets/715039db-899b-4fde-9191-4ea4b9908f04)

# Capture 23
![image](https://github.com/user-attachments/assets/acd7fb65-4ff3-4ad2-9200-6047e0655228)

# Capture 24
![image](https://github.com/user-attachments/assets/ec5c2380-d6d0-4de4-be4b-e0eba4b8a012)

# Capture 25
![image](https://github.com/user-attachments/assets/5cd58d1a-f32c-4204-9ba5-cd24af3fa50a)

# Capture 26
![image](https://github.com/user-attachments/assets/b0406f86-2a90-40b5-a16a-98dde7b1cae0)

# Capture 27
![image](https://github.com/user-attachments/assets/66f69e0f-4e46-482f-9f47-37ea470f6c54)

# Capture 28
![image](https://github.com/user-attachments/assets/6573eed2-fe19-4652-b45b-8311688d9009)

# Capture 29
![image](https://github.com/user-attachments/assets/140028bd-5cec-4cf2-a254-cd85c10a68ea)

# Capture 30
![image](https://github.com/user-attachments/assets/3d087bb1-8dfb-47ad-93ff-0231ac1af445)

# Capture 31
![image](https://github.com/user-attachments/assets/1e58e113-9067-4da3-b6d7-b0530ec4b390)

# Capture 32
![image](https://github.com/user-attachments/assets/1028c255-8bf5-4e1d-ae19-4e21ffc42687)

# Capture 33
![image](https://github.com/user-attachments/assets/13e039ef-f986-44e5-aa6b-a9098314681c)

# Capture 34
![image](https://github.com/user-attachments/assets/23266b49-ea82-47e4-8a35-5d653a43127c)















































