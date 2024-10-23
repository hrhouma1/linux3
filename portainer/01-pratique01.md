# Installation de Portainer

*Pour installer et configurer Portainer avec la ligne de commande Linux, voici les étapes à suivre :*

### Prérequis :
- Docker doit être installé sur votre machine.

### Étapes :


1. **Lancer Portainer en tant que container Docker :**
   Exécutez les commandes suivantes pour déployer Portainer en mode `standalone` (pour un usage unique ou sur une petite installation Docker) :

   a. **Créer un volume Docker pour Portainer :**
   ```bash
   docker volume create portainer_data
   ```

   b. **Déployer le conteneur Portainer :**
   ```bash
   docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always \
   -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
   ```

   - `-d` : lance le conteneur en mode détaché (en arrière-plan).
   - `-p 9000:9000` : mappe le port 9000 de votre machine hôte au port 9000 du conteneur (port d'accès à l'interface web de Portainer).
   - `-p 8000:8000` : optionnel, utilisé pour la gestion des agents Docker (si vous utilisez l'agent Portainer sur plusieurs hôtes).
   - `--restart=always` : permet au conteneur de redémarrer automatiquement en cas de redémarrage de votre machine.

2. **Accéder à l'interface web de Portainer :**
   Ouvrez votre navigateur et allez à l'adresse suivante :

   ```
   http://<IP-de-votre-machine>:9000
   ```

   Lors de la première connexion, vous serez invité à créer un utilisateur administrateur.

### Commande complète en une seule étape :
Pour plus de simplicité, voici la commande complète pour déployer Portainer :

```bash
docker volume create portainer_data && docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

Après cela, vous pourrez utiliser Portainer pour gérer vos conteneurs Docker à travers une interface graphique intuitive.



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

Cette capture d'écran montre la section **Container console** dans Portainer, où vous pouvez accéder à une console à l'intérieur du conteneur Docker en cours d'exécution.

### Détails de la **Container console** :

1. **Command** :
   - Par défaut, la commande à exécuter est `/bin/bash`. Cela ouvre une session bash dans le conteneur, vous permettant d'exécuter des commandes comme si vous étiez à l'intérieur du conteneur.

2. **Use custom command** :
   - Cette option, lorsqu'elle est activée, vous permet de définir une commande personnalisée autre que `/bin/bash` si vous avez besoin d'exécuter autre chose à l'intérieur du conteneur. Ici, l'option est désactivée, donc la commande par défaut sera utilisée.

3. **User** :
   - L'utilisateur avec lequel vous allez exécuter la commande est défini ici à **root**. Cela signifie que vous avez un accès complet et des privilèges d'administrateur pour exécuter des commandes dans le conteneur.

4. **Connect** :
   - En cliquant sur **Connect**, vous accédez à la console interactive du conteneur et pouvez commencer à entrer des commandes.

### Fonctionnement :
Cette interface vous permet d'accéder à une **shell interactive** à l'intérieur du conteneur sans avoir à sortir de l'interface web Portainer. C'est une fonctionnalité très utile pour les administrateurs qui souhaitent rapidement déboguer ou modifier un conteneur sans avoir à se connecter via SSH ou utiliser des outils de ligne de commande Docker externes.

### Utilité :
Vous pouvez utiliser cette console pour :
- Exécuter des commandes Linux à l'intérieur du conteneur.
- Déboguer des problèmes en accédant directement aux fichiers ou processus internes.
- Modifier des fichiers de configuration ou examiner les journaux sans quitter l'interface web.

Cela simplifie grandement l'administration des conteneurs et permet de réagir rapidement à des problèmes ou à des changements nécessaires en temps réel.

# Capture 18
![image](https://github.com/user-attachments/assets/72e9d499-524d-495b-9edd-c4fec555a00d)

Cette capture d'écran montre une session de **console** dans un conteneur Docker via Portainer, où vous avez réussi à accéder au conteneur **flask-hello**.

### Détails de la console du conteneur :

1. **Message d'exécution** :
   - Vous êtes connecté au conteneur en tant qu'utilisateur **default** (qui est ici défini comme **root**). La commande par défaut exécutée est **bash**, ce qui ouvre une session Bash à l'intérieur du conteneur.

2. **Prompt de commande** :
   - Le prompt indique que vous êtes connecté en tant que **root** dans le répertoire **/myproject** à l'intérieur du conteneur. Le conteneur a pour identifiant **6c67dc4b...**, qui est un raccourci de l'identifiant complet du conteneur.

3. **Bouton "Disconnect"** :
   - Vous pouvez quitter cette session interactive en cliquant sur le bouton **Disconnect**, ce qui fermera la console et mettra fin à l'accès direct à ce conteneur.

### Utilité :
Cette console vous permet d'exécuter des commandes directement à l'intérieur du conteneur en utilisant l'utilisateur root. Cela peut être utile pour :
- Naviguer dans le système de fichiers du conteneur.
- Exécuter des scripts ou des commandes de maintenance.
- Modifier ou vérifier des fichiers de configuration.
- Déboguer des problèmes en direct sans avoir à arrêter ou redémarrer le conteneur.

Vous pouvez ainsi interagir directement avec l'environnement du conteneur de manière rapide et efficace, sans quitter l'interface Portainer. Cela simplifie la gestion des conteneurs et permet une intervention en temps réel pour les opérations courantes ou d'urgence.

# Capture 19
![image](https://github.com/user-attachments/assets/758b64db-b479-4129-9162-369338157eda)

Cette capture d'écran montre une session active de **console** à l'intérieur d'un conteneur Docker dans Portainer, où la commande **ps -ef** a été exécutée pour afficher les processus en cours dans le conteneur.

### Détails de la console du conteneur :

1. **Commande exécutée** : 
   - La commande **ps -ef** a été utilisée pour lister tous les processus en cours d'exécution dans le conteneur avec des détails tels que l'UID (utilisateur), le PID (identifiant du processus), le PPID (identifiant du processus parent), l'heure de démarrage (STIME), et la commande en cours d'exécution (CMD).

2. **Processus affichés** :
   - **Processus 1** : Le processus de démarrage (`/bin/sh -c "python flask-hello.py"`) avec l'UID **root**. Ce processus a été lancé à **11:14** et exécute la commande de démarrage du serveur Flask.
   - **Processus 10** : Le processus Python qui exécute directement le serveur Flask (`python flask-hello.py`), également démarré à **11:14**.
   - **Processus 9** : Une session Bash active, démarrée à **11:42**, ce qui correspond probablement à la session interactive ouverte via la console dans Portainer.
   - **Processus 14** : La commande **ps -ef** elle-même, exécutée à **11:45**, pour afficher cette liste de processus.

3. **Utilisateur** : 
   - Tous les processus sont exécutés sous l'utilisateur **root**, qui dispose de tous les privilèges dans le conteneur.

4. **Bouton "Disconnect"** :
   - Un bouton **Disconnect** est disponible pour quitter la session et arrêter l'accès à la console du conteneur.

### Utilité :
L'exécution de la commande **ps -ef** permet de voir tous les processus en cours à l'intérieur du conteneur, ce qui est essentiel pour le débogage ou la surveillance des applications. Vous pouvez ainsi vérifier quels processus sont actifs, et si des processus inattendus ou non désirés tournent dans le conteneur.

Cela vous permet également de confirmer que l'application Flask est en cours d'exécution avec le processus Python approprié, ce qui est crucial pour diagnostiquer des problèmes de performance ou des comportements inattendus dans le conteneur.

Cette interface interactive est très utile pour administrer des conteneurs en temps réel et réagir rapidement aux anomalies ou aux besoins de maintenance.

# Capture 20
![image](https://github.com/user-attachments/assets/44469646-3761-4e07-939f-c83f6cb809b4)

Cette capture d'écran montre la section **Image list** dans Portainer, où vous pouvez gérer les images Docker stockées localement sur votre hôte Docker.

### Détails de la gestion des images Docker :

1. **Pull image** :
   - **Image** : Vous pouvez entrer le nom d'une image (par exemple `myimage:myTag`) dans ce champ pour télécharger une nouvelle image depuis un registre Docker. Si aucun tag n'est spécifié, Portainer va récupérer la version **latest** par défaut.
   - **Registry** : Le registre par défaut est **DockerHub**, mais vous pouvez spécifier un autre registre si nécessaire.
   - **Pull the Image** : En cliquant sur ce bouton, vous téléchargez l'image Docker spécifiée à partir du registre choisi.

2. **Liste des images** :
   - La section **Images** montre les images Docker qui sont déjà présentes sur l'hôte Docker.
   - **Id** : L'identifiant unique de chaque image est affiché sous forme de hash SHA256.
   - **Tags** : Les tags associés à chaque image (par exemple `alpine:latest`, `centos:latest`, etc.). Les tags permettent de savoir quelle version de l'image est disponible.
   - **Unused** : Les images non utilisées par les conteneurs actifs sont marquées comme **Unused**, ce qui signifie qu'elles sont disponibles mais ne sont pas actuellement utilisées dans un conteneur en cours d'exécution.

3. **Actions disponibles** :
   - **Remove** : Supprimer les images sélectionnées de l'hôte Docker.
   - **Build a new Image** : Vous pouvez construire une nouvelle image Docker en spécifiant un Dockerfile ou d'autres méthodes.
   - **Import** : Importer une image Docker depuis un fichier local.
   - **Export** : Exporter une image Docker vers un fichier.

4. **Barre de recherche** :
   - Un champ de recherche vous permet de filtrer les images en fonction de leur ID, de leurs tags ou d'autres critères, ce qui facilite la gestion d'un grand nombre d'images.

### Utilité :
Cette interface vous permet de gérer efficacement les images Docker sur votre hôte sans avoir à utiliser la ligne de commande. Vous pouvez télécharger de nouvelles images, supprimer celles qui ne sont plus nécessaires, ou encore importer et exporter des images pour les déplacer d'un environnement à un autre.

Les images marquées comme **Unused** peuvent être candidates à la suppression pour libérer de l'espace sur le disque, mais il est aussi possible de garder des images en réserve pour des déploiements futurs. Grâce à cette interface, vous pouvez facilement administrer toutes les images présentes et maintenir un environnement Docker propre et optimisé.

# Capture 21 - barre de recherche 
![image](https://github.com/user-attachments/assets/73c0cc9d-f0d0-4727-b7ff-97928bd383aa)


Cette capture d'écran montre la section **Image list** dans Portainer avec un exemple de recherche utilisant la barre de recherche.

### Détails de la capture (barre de recherche) :

1. **Barre de recherche** :
   - La recherche a été effectuée avec le terme **flask-hello**, ce qui permet de filtrer et d'afficher uniquement les images Docker correspondant à ce nom.
   - La barre de recherche se trouve sous la section des actions (Remove, Build a new Image, Import, Export) et permet de filtrer rapidement les images par leur nom, leur ID ou leurs tags.

2. **Résultats de la recherche** :
   - Deux images sont listées dans les résultats, toutes deux liées à **flask-hello** :
     - **Image 1** : Taguée `flask-hello:1.0`, `pythonincontainers/flask-hello:1.0`, marquée comme **Unused** et de taille **927.6 MB**.
     - **Image 2** : Taguée `flask-hello:2.0`, `pythonincontainers/flask-hello:2.0`, ainsi que `pythonincontainers/flask-hello:latest`, également de **927.6 MB**.

3. **Actions disponibles** :
   - Vous pouvez toujours supprimer (`Remove`) les images sélectionnées, construire une nouvelle image (`Build a new Image`), importer ou exporter des images.

### Utilité :
La barre de recherche dans cette interface est essentielle pour filtrer rapidement les images Docker lorsque vous en avez beaucoup stockées localement. Cela vous permet de trouver immédiatement les images pertinentes sans avoir à parcourir manuellement une longue liste.

Dans cet exemple, la recherche de **flask-hello** a permis de retrouver plusieurs versions de l'image (1.0, 2.0, latest), facilitant ainsi la gestion des différentes versions de l'image Docker utilisée pour le projet Flask. Cette fonctionnalité permet une gestion efficace des images Docker dans un environnement multi-projets ou multi-versions.

# Capture 22
![image](https://github.com/user-attachments/assets/715039db-899b-4fde-9191-4ea4b9908f04)

Cette capture d'écran montre les **détails d'une image Docker** dans Portainer.

### Détails de l'image Docker :

1. **Image details** :
   - **ID** : Identifiant unique de l'image, ici en SHA256 (ex. `sha256:6d8ca6b...`).
   - **Parent** : Référence à l'image parente à partir de laquelle celle-ci a été dérivée (ex. `sha256:bc899...`).
   - **Size** : La taille de l'image, qui est de **927.6 MB**.
   - **Created** : La date et l'heure de création de l'image, ici le **19 août 2019** à **08:36:41**.
   - **Build** : Informations sur la version de Docker utilisée pour créer l'image (Docker 19.03.1 sur Linux, amd64).

2. **Dockerfile details** :
   - **CMD** : La commande exécutée par défaut dans l'image lors du lancement du conteneur (ici `/bin/sh -c python flask-hello.py`).
   - **EXPOSE** : Le port **5000/tcp** est exposé, ce qui signifie que l'application à l'intérieur du conteneur sera accessible sur ce port.
   - **ENV** : Les variables d'environnement définies dans l'image Docker incluent :
     - **PATH** : Répertoires de chemin pour la recherche de binaires.
     - **LANG** : Définit la langue en UTF-8.
     - **GPG_KEY** : Clé GPG utilisée pour des vérifications.
     - **PYTHON_VERSION** : Version de Python (3.7.4).
     - **PYTHON_PIP_VERSION** : Version de pip (19.2.2).
     - **PYTHON_GET_PIP_URL** : L'URL pour obtenir pip.
     - **FLASK_DEBUG** : Le mode debug de Flask est activé (`True`), utile pour le développement.

3. **Actions disponibles** :
   - **Delete this image** : Supprimer cette image Docker de l'hôte.
   - **Export this image** : Exporter cette image vers un fichier pour l'utiliser ailleurs ou la sauvegarder.

### Utilité :
Cette section permet de visualiser les informations critiques sur une image Docker stockée localement, incluant les commandes par défaut, les variables d'environnement, et les ports exposés. C'est un excellent outil pour vérifier le contenu et la configuration d'une image avant de l'utiliser ou de la partager.

Les options **Delete** et **Export** permettent de gérer les images, soit pour les supprimer si elles ne sont plus nécessaires, soit pour les sauvegarder ou les transférer dans d'autres environnements Docker. Cela simplifie la gestion des images directement depuis l'interface graphique de Portainer sans avoir à recourir aux commandes Docker en ligne de commande.

# Capture 23
![image](https://github.com/user-attachments/assets/acd7fb65-4ff3-4ad2-9200-6047e0655228)


Cette capture d'écran montre les **détails des couches (layers)** d'une image Docker dans Portainer, décrivant les différentes étapes de construction de cette image.

### Détails des couches de l'image Docker (layers) :

1. **Order** : 
   - Cela représente l'ordre dans lequel chaque commande a été exécutée dans le Dockerfile. Chaque commande du Dockerfile crée une nouvelle couche dans l'image Docker.

2. **Size** :
   - La taille de chaque couche est indiquée. Certaines couches sont très petites (par exemple, 0 B) tandis que d'autres peuvent être assez grandes, comme les 510.2 MB utilisés pour installer des paquets système.

3. **Layer** :
   - Voici un résumé des commandes exécutées pour chaque couche :
     - **ADD** : Ajouter un fichier au conteneur (ici un fichier de 114 MB).
     - **CMD** : La commande par défaut exécutée à l'intérieur du conteneur est `bash`.
     - **RUN** : Exécute des commandes pour installer des paquets ou configurer l'environnement. Par exemple, `apt-get update && apt-get install` pour installer divers outils et dépendances.
     - **ENV** : Définit des variables d'environnement comme **PATH**, **LANG**, **PYTHON_VERSION**, **PYTHON_PIP_VERSION**, etc.
     - **WORKDIR** : Définit le répertoire de travail du conteneur, ici `/myproject`.
     - **COPY** : Copie un fichier du système hôte vers l'image Docker.

4. **Exemple de commandes exécutées** :
   - **apt-get update && apt-get install** : Cela installe divers outils comme `ca-certificates`, `gnupg`, `mercurial`, `python`, etc.
   - **ENV PYTHON_VERSION=3.7.4** : Définit la version de Python à utiliser dans l'image.
   - **RUN wget** : Télécharge Python à partir d'une URL spécifique, le compile et l'installe.

### Utilité :
Cette vue permet d'avoir un aperçu clair des différentes étapes ayant conduit à la construction de l'image Docker. Cela inclut l'installation des dépendances, la configuration de l'environnement et les fichiers ajoutés au conteneur.

Grâce à ces détails, vous pouvez comprendre les composants et les étapes impliquées dans la création de l'image. Cela facilite également le débogage si une couche particulière pose problème ou si vous souhaitez optimiser une image en réduisant sa taille ou en modifiant des étapes.

### Gestion des images :
Les utilisateurs peuvent visualiser ces informations pour optimiser leurs Dockerfiles, réduire la taille des images, ou simplement mieux comprendre comment l'image a été construite. Portainer fournit ici une vue claire et simple de chaque commande exécutée lors de la création de l'image.

# Capture 24
![image](https://github.com/user-attachments/assets/ec5c2380-d6d0-4de4-be4b-e0eba4b8a012)

Cette capture d'écran montre l'interface **Build image** dans Portainer, qui permet de créer une nouvelle image Docker à partir d'un Dockerfile.

### Détails de l'interface Build image :

1. **Naming** :
   - Vous pouvez spécifier un nom pour l'image à créer. Dans cet exemple, l'image est nommée **my-test**.
   - Vous pouvez ajouter plusieurs noms pour la même image si nécessaire. Les formats acceptés pour les noms sont indiqués (ex. `name:tag`, `repository/name:tag`, etc.).

2. **Build method** :
   - Vous avez deux options pour créer l'image :
     - **Web editor** : Utiliser l'éditeur intégré de Portainer pour écrire ou modifier directement un Dockerfile.
     - **Upload** : Importer un fichier Dockerfile depuis votre ordinateur ou un fichier d'archive (tarball) contenant les instructions de construction.

3. **Web editor** :
   - Dans l'exemple, l'éditeur Web est utilisé pour définir un Dockerfile simple :
     - **FROM python:3** : Utilise une image de base Python 3.
     - **ENV TEST hello** : Définit une variable d'environnement appelée **TEST** avec la valeur **hello**.
     - **CMD echo 'print("Hello from my-test")' | python** : Exécute une commande Python qui affiche le message "Hello from my-test".

4. **Construction de l'image** :
   - Une fois que le Dockerfile est prêt, vous pouvez lancer la construction de l'image Docker directement depuis l'interface.

### Utilité :
Cette interface simplifie la création d'images Docker pour les utilisateurs de Portainer. Vous pouvez rédiger ou importer un Dockerfile, spécifier un nom pour l'image, et démarrer le processus de construction, tout cela sans quitter l'interface graphique.

Le **Web editor** est particulièrement utile pour tester rapidement des modifications ou créer des images simples à la volée, tandis que l'option **Upload** permet de gérer des Dockerfiles plus complexes ou de travailler sur des projets déjà structurés. Cette flexibilité rend la gestion des images Docker accessible même pour des utilisateurs qui ne maîtrisent pas forcément la ligne de commande Docker.

# Capture 25
![image](https://github.com/user-attachments/assets/5cd58d1a-f32c-4204-9ba5-cd24af3fa50a)

Cette capture d'écran montre une vue rapprochée du **Web editor** dans Portainer, utilisée pour créer ou modifier un **Dockerfile** pour construire une image Docker.

### Détails du Dockerfile :

1. **FROM python:3** :
   - Cette ligne spécifie l'image de base utilisée pour construire l'image Docker. Ici, l'image **python:3** est utilisée, ce qui signifie que l'environnement Python 3 sera inclus dans l'image.

2. **ENV TEST hello** :
   - Cette ligne définit une variable d'environnement nommée **TEST** avec la valeur **hello**. Les variables d'environnement sont souvent utilisées pour passer des informations dans un conteneur sans avoir à les coder en dur.

3. **CMD echo 'print("Hello from my-test")' | python** :
   - Cette ligne définit la commande par défaut qui sera exécutée lorsque le conteneur sera lancé. Ici, la commande `echo 'print("Hello from my-test")' | python` imprime la phrase **Hello from my-test** en utilisant Python.

### Utilité :
Cette interface simplifiée permet de créer et d'éditer rapidement des Dockerfiles directement depuis Portainer, sans avoir à passer par un éditeur de texte externe ou un terminal. Ce Dockerfile simple montre comment définir une image de base Python, ajouter une variable d'environnement et exécuter une commande Python.

Cette fonctionnalité est utile pour des déploiements rapides ou pour tester de petites configurations avant de créer des environnements plus complexes. Portainer rend la gestion des images Docker plus accessible, même pour les utilisateurs qui ne sont pas familiers avec les lignes de commande Docker.

# Capture 26
![image](https://github.com/user-attachments/assets/b0406f86-2a90-40b5-a16a-98dde7b1cae0)

Cette capture d'écran montre le résultat du processus de création d'une image Docker via l'interface **Build image** dans Portainer.

### Détails de la sortie (Output) de la construction de l'image :

1. **Étapes de construction** :
   - Le processus de construction de l'image suit les étapes définies dans le Dockerfile :
     - **Step 1/3** : Utilisation de l'image de base **python:3**.
     - **Step 2/3** : Définition de la variable d'environnement **TEST** avec la valeur **hello**.
     - **Step 3/3** : Exécution de la commande **CMD** pour imprimer "Hello from my-test" en utilisant Python.

2. **Conteneurs intermédiaires** :
   - Pendant la construction, Docker crée et supprime des **conteneurs intermédiaires** pour chaque étape (ex. `9be02a9c7a46`, `453a15f94867`). Ces conteneurs sont utilisés temporairement pour exécuter chaque commande du Dockerfile.

3. **Image créée** :
   - La construction est réussie avec un identifiant d'image **6a357220d333**.
   - L'image est également étiquetée sous le nom **my-test:latest**, ce qui signifie que l'image est maintenant disponible localement avec ce tag.

### Utilité :
Cette interface dans Portainer permet de suivre en temps réel la progression de la construction d'une image Docker et de voir chaque étape s'exécuter. Cela vous permet de vérifier rapidement que tout se passe comme prévu, et que l'image est correctement construite et taguée.

Les logs détaillés des conteneurs intermédiaires et des étapes permettent de diagnostiquer facilement les erreurs si le processus échoue, et d'optimiser le Dockerfile si nécessaire. Cela simplifie grandement la gestion des images Docker pour les utilisateurs, sans qu'ils aient besoin de passer par des commandes Docker en ligne de commande.

# Capture 27
![image](https://github.com/user-attachments/assets/66f69e0f-4e46-482f-9f47-37ea470f6c54)

Cette capture d'écran montre la section **Image list** dans Portainer après avoir construit et tagué une image Docker nommée **my-test**.

### Détails de la liste des images Docker :

1. **Barre de recherche** :
   - Le terme **my-test** a été saisi dans la barre de recherche, permettant de filtrer les images affichées et de n'afficher que l'image nommée **my-test**.

2. **Images filtrées** :
   - L'image Docker créée plus tôt, **my-test:latest**, est maintenant visible dans la liste des images.
   - Elle est identifiée par un hash SHA256 (ex. `sha256:6a357220d333...`).
   - Le tag associé est **my-test:latest**.
   - L'image est marquée comme **Unused**, ce qui signifie qu'elle n'est actuellement utilisée par aucun conteneur.

3. **Actions disponibles** :
   - Vous pouvez :
     - **Remove** : Supprimer l'image.
     - **Build a new image** : Construire une nouvelle image Docker.
     - **Import** : Importer une image Docker à partir d'un fichier.
     - **Export** : Exporter l'image Docker dans un fichier pour la sauvegarder ou l'utiliser ailleurs.

### Utilité :
Cette interface vous permet de gérer les images Docker de manière simple et intuitive. Vous pouvez voir immédiatement l'image que vous avez créée (avec son tag `latest`), et décider si vous souhaitez la déployer, la supprimer ou l'exporter.

La fonctionnalité de filtre est particulièrement utile dans les environnements où plusieurs images Docker sont présentes, car elle permet de trouver rapidement l'image spécifique dont vous avez besoin.

# Capture 28
![image](https://github.com/user-attachments/assets/6573eed2-fe19-4652-b45b-8311688d9009)

Cette capture d'écran montre la section **Container logs** dans Portainer, affichant les journaux d'un conteneur nommé **my-test**.

### Détails de la section Container logs :

1. **Log viewer settings** :
   - **Auto-refresh logs** : Option activée pour rafraîchir automatiquement les journaux à mesure que de nouvelles entrées sont générées.
   - **Wrap lines** : Option activée pour permettre l'affichage des lignes longues en les coupant sur plusieurs lignes afin de les rendre lisibles.
   - **Display timestamps** : Désactivé ici, mais cette option permettrait d'afficher l'heure de chaque ligne de log.
   - **Fetch** : Vous pouvez sélectionner d'afficher tous les journaux ou filtrer par des journaux spécifiques.
   - **Search** : Un champ de recherche permet de filtrer les journaux pour trouver des informations spécifiques.
   - **Lines** : Le nombre de lignes affichées est ici limité à 100.
   - **Actions** : Des boutons permettent de copier les journaux, soit en entier (`Copy`), soit seulement les lignes sélectionnées (`Copy selected lines`).

2. **Contenu des logs** :
   - Le message "Hello from my-test" est visible dans la partie inférieure des journaux, ce qui correspond à la commande Python définie dans le Dockerfile de ce conteneur, qui affiche ce message lors de l'exécution.

### Utilité :
Cette section permet de consulter les journaux d'un conteneur pour vérifier son comportement. Ici, le message de test "Hello from my-test" confirme que le conteneur fonctionne comme prévu et exécute la commande Python définie.

Les fonctionnalités de recherche et de copie sont utiles pour extraire des informations spécifiques des logs, ce qui est pratique pour le débogage et la surveillance des conteneurs en production ou en développement. Grâce à la fonctionnalité d'auto-refresh, vous pouvez également voir les journaux en temps réel sans avoir besoin de rafraîchir manuellement la page.

# Capture 29
![image](https://github.com/user-attachments/assets/140028bd-5cec-4cf2-a254-cd85c10a68ea)


Cette capture d'écran montre les **détails du réseau** dans Portainer pour un réseau nommé **my-net**.

### Détails du réseau :

1. **Network details** :
   - **Name** : Le nom du réseau est **my-net**.
   - **ID** : L'identifiant unique du réseau est visible (ex. `59e07951...`).
   - **Driver** : Le type de réseau est **bridge**, qui est l'un des types de réseaux Docker par défaut, utilisé pour connecter des conteneurs sur un même hôte.
   - **Scope** : Le scope du réseau est **local**, ce qui signifie qu'il est limité à l'hôte Docker.
   - **Attachable** : Indique si d'autres conteneurs peuvent être attachés à ce réseau (ici, **false**).
   - **Internal** : Définit si le réseau est interne uniquement (ici, **false**).
   - **Subnet** : Le sous-réseau associé au réseau est **172.21.0.0/16**.
   - **Gateway** : La passerelle du réseau est **172.21.0.1**.

2. **Access control** :
   - Le contrôle d'accès est configuré pour permettre uniquement aux **administrators** de gérer ce réseau.
   - Il y a une option pour **Change ownership**, ce qui permet de modifier la propriété du réseau.

3. **Containers in network** :
   - Un conteneur nommé **proxy-server** est connecté à ce réseau.
   - Son adresse IPv4 est **172.21.0.2/16**.
   - Son adresse MAC est **02:42:ac:15:00:02**.
   - Il est possible de cliquer sur **Leave Network** pour déconnecter le conteneur du réseau.

### Utilité :
Cette section permet de visualiser et gérer les réseaux Docker dans Portainer. Vous pouvez voir quels conteneurs sont connectés au réseau, leurs adresses IP et MAC, ainsi que les paramètres du réseau (type, sous-réseau, passerelle).

Cela simplifie la gestion des réseaux Docker, permettant aux utilisateurs d’ajouter ou de retirer des conteneurs, de configurer les propriétés du réseau, ou de supprimer des réseaux inutilisés via l'interface graphique.

# Capture 30
![image](https://github.com/user-attachments/assets/3d087bb1-8dfb-47ad-93ff-0231ac1af445)

Cette capture d'écran montre à nouveau les **détails du réseau** pour un réseau Docker nommé **my-net** dans Portainer.

### Détails du réseau :

1. **Network details** :
   - **Name** : Le réseau s'appelle **my-net**.
   - **ID** : Un identifiant unique est attribué à ce réseau (`59e07951dc4...`).
   - **Driver** : Le type de réseau est défini comme **bridge**, un réseau par défaut dans Docker.
   - **Scope** : Le réseau est de portée **local**, c'est-à-dire qu'il est limité à l'hôte Docker.
   - **Attachable** : Ce paramètre est **false**, signifiant que d'autres conteneurs ne peuvent pas être ajoutés facilement à ce réseau.
   - **Internal** : Le réseau n'est pas interne (paramètre **false**).
   - **Subnet** : Le sous-réseau est défini avec l'adresse **172.21.0.0/16**.
   - **Gateway** : La passerelle pour ce réseau est **172.21.0.1**.

2. **Access control** :
   - Le contrôle d'accès est limité aux **administrators**, qui peuvent gérer ce réseau.
   - Il est possible de changer la propriété du réseau via l'option **Change ownership**.

3. **Containers in network** :
   - Un conteneur nommé **proxy-server** est connecté à ce réseau.
   - Son adresse IPv4 est **172.21.0.2/16**.
   - Son adresse MAC est **02:42:ac:15:00:02**.
   - Une action possible pour ce conteneur est de le retirer du réseau en cliquant sur **Leave Network**.

4. **Action sur le réseau** :
   - Un bouton **Delete this network** est disponible pour supprimer ce réseau Docker, ce qui supprimera aussi les connexions des conteneurs liés à ce réseau.

### Utilité :
Cette interface permet de visualiser et de gérer les réseaux Docker et les conteneurs qui y sont connectés. Elle permet de contrôler l'accès au réseau, de voir les informations sur le sous-réseau et la passerelle, et d'ajouter ou retirer des conteneurs du réseau. Cela facilite la gestion de l'infrastructure réseau des conteneurs directement via une interface graphique intuitive, sans avoir besoin d'utiliser les commandes Docker.

# Capture 31
![image](https://github.com/user-attachments/assets/1e58e113-9067-4da3-b6d7-b0530ec4b390)

Cette capture d'écran montre la **Network list** (liste des réseaux) dans Portainer, qui permet de visualiser et gérer les réseaux Docker configurés sur un hôte.

### Détails de la liste des réseaux :

1. **Liste des réseaux** :
   - Les réseaux sont listés avec leurs détails :
     - **bridge** : Réseau par défaut avec le driver de type **bridge**. Ce type de réseau permet de connecter des conteneurs entre eux sur le même hôte.
     - **host** : Utilise le réseau de l'hôte directement, sans isolation.
     - **int-net** : Un réseau personnalisé avec le sous-réseau **172.24.0.0/16**.
     - **my-addr** et **my-net** : Réseaux créés par l'utilisateur avec les sous-réseaux respectifs **10.10.0.0/16** et **172.21.0.0/16**.
     - **none** : Réseau Docker spécial qui n'attribue pas de connectivité réseau aux conteneurs qui l'utilisent.

2. **Colonnes affichées** :
   - **Name** : Le nom du réseau.
   - **Stack** : Les stacks associées (ici non utilisées, colonne vide).
   - **Scope** : Portée du réseau (ici tous les réseaux sont **local**).
   - **Driver** : Le driver réseau utilisé (ex. **bridge**, **host**, ou **null** pour le réseau **none**).
   - **Attachable** : Indique si des conteneurs peuvent être attachés au réseau (seul le réseau **int-net** a cette option activée).
   - **Internal** : Indique si le réseau est interne uniquement (aucun réseau dans cette liste n'est interne).
   - **IPAM Driver** : Le driver IPAM utilisé pour la gestion des adresses IP (ici tous utilisent **default**).
   - **IPAM Subnet** : Le sous-réseau IP configuré pour chaque réseau (ex. **172.17.0.0/16** pour le réseau **bridge**).
   - **IPAM Gateway** : La passerelle associée au sous-réseau (ex. **172.17.0.1** pour le réseau **bridge**).

3. **Actions disponibles** :
   - **Remove** : Permet de supprimer les réseaux sélectionnés.
   - **Add network** : Permet d'ajouter un nouveau réseau Docker via une interface graphique.

### Utilité :
Cette vue est utile pour visualiser et gérer tous les réseaux Docker configurés sur un hôte. Elle permet de vérifier rapidement les configurations réseau, comme les sous-réseaux et les passerelles, et de créer ou supprimer des réseaux. Portainer simplifie la gestion réseau pour les utilisateurs de Docker, en particulier pour ceux qui préfèrent utiliser une interface graphique plutôt que des commandes en ligne.

# Capture 32
![image](https://github.com/user-attachments/assets/1028c255-8bf5-4e1d-ae19-4e21ffc42687)

Cette capture d'écran montre la section **Volume list** dans Portainer, qui répertorie les volumes Docker configurés sur l'hôte.

### Détails de la liste des volumes :

1. **Liste des volumes** :
   - La liste affiche les volumes Docker créés, avec les colonnes suivantes :
     - **Name** : Le nom unique généré pour chaque volume.
     - **Stack** : La stack associée (ici vide, car les volumes ne sont pas liés à des stacks spécifiques).
     - **Driver** : Le driver utilisé pour gérer les volumes (ici, tous utilisent le driver **local**).
     - **Mount point** : Le point de montage du volume sur l'hôte Docker (ex. `/var/lib/docker/volumes/471e97.../_data`).
     - **Created** : La date de création de chaque volume.
     - **State** : Les volumes affichés sont marqués comme **Unused**, ce qui signifie qu'ils ne sont actuellement pas utilisés par des conteneurs.

2. **Actions disponibles** :
   - **Remove** : Vous pouvez supprimer les volumes sélectionnés à l'aide de ce bouton.
   - **Add volume** : Permet d'ajouter un nouveau volume Docker via une interface graphique.

3. **Exemple de volume utilisé** :
   - **portainer_data** est un volume utilisé pour stocker les données persistantes de Portainer. Il est monté sous `/var/lib/docker/volumes/portainer_data/_data`.

### Utilité :
Cette interface permet de visualiser, ajouter ou supprimer des volumes Docker directement depuis l'interface de Portainer. Les volumes sont essentiels pour stocker des données persistantes, notamment pour les bases de données ou d'autres services nécessitant de la rétention de données au-delà du cycle de vie d'un conteneur. L'interface fournit un moyen simple d'identifier les volumes inutilisés et de gérer l'espace disque en supprimant les volumes qui ne sont plus nécessaires.

# Capture 33
![image](https://github.com/user-attachments/assets/13e039ef-f986-44e5-aa6b-a9098314681c)

Cette capture d'écran montre la section **Event list** dans Portainer, qui répertorie les événements liés aux conteneurs.

### Détails de la liste des événements :

1. **Liste des événements** :
   - Les événements sont classés par date et heure.
   - **Date** : Affiche la date et l'heure exactes de chaque événement (ex. 2019-08-19 14:21:42).
   - **Category** : La catégorie d'événement, qui dans ce cas est **container**. Cela indique que tous ces événements sont liés à des actions effectuées sur des conteneurs Docker.
   - **Details** : Les détails de l'événement, tels que :
     - **Unsupported event** : Signifie que Portainer ne prend pas en charge la visualisation de l'événement en question.
     - **Exec instance created** : Indique qu'une instance d'exécution (un shell ou une commande) a été créée dans un conteneur.
     - **Exec instance started** : Indique qu'une instance d'exécution a été démarrée dans un conteneur.

2. **Actions disponibles** :
   - Il est possible de rechercher des événements spécifiques à l'aide de la barre de recherche.
   - Les événements peuvent être actualisés avec l'icône de rafraîchissement en haut de la page.

### Utilité :
La **Event list** permet de suivre les différentes actions et événements qui se produisent au niveau des conteneurs Docker. Cette fonctionnalité est utile pour surveiller les activités, diagnostiquer les problèmes et suivre les événements exécutés sur les conteneurs, comme la création et le démarrage d'instances d'exécution. Elle permet également aux administrateurs de vérifier les logs d'événements pour résoudre les problèmes de conteneurs Docker de manière proactive.

# Capture 34
![image](https://github.com/user-attachments/assets/23266b49-ea82-47e4-8a35-5d653a43127c)

Cette capture d'écran montre la section **Host Overview** dans Portainer, qui donne un aperçu des détails de l'hôte Docker sur lequel Portainer est déployé.

### Détails de l'hôte :

1. **Host Details** (Détails de l'hôte) :
   - **Hostname** : Nom de l'hôte Docker (ici, `docker-desktop`).
   - **OS Information** : Informations sur le système d'exploitation (ici, `linux x86_64 Docker Desktop`).
   - **Kernel Version** : Version du noyau Linux utilisée (ici, `4.9.184-linuxkit`).
   - **Total CPU** : Le nombre total de cœurs de processeur (ici, 2 cœurs).
   - **Total Memory** : Quantité totale de mémoire (ici, 2.1 Go).

2. **Engine Details** (Détails du moteur) :
   - **Version** : Version du moteur Docker (ici, `19.03.1` avec l'API `1.40`).
   - **Root Directory** : Répertoire racine utilisé par Docker pour stocker les données (ici, `/var/lib/docker`).
   - **Storage Driver** : Le driver de stockage utilisé (ici, `overlay2`).
   - **Logging Driver** : Le driver utilisé pour gérer les journaux (ici, `json-file`).
   - **Volume Plugins** : Plugins de gestion des volumes activés (ici, `local`).
   - **Network Plugins** : Plugins réseau activés (ici, `bridge`, `host`, `ipvlan`, `macvlan`, `null`, `overlay`).

### Utilité :
Cette section permet aux utilisateurs d'obtenir rapidement des informations sur la configuration matérielle et logicielle de l'hôte Docker. Les administrateurs peuvent utiliser ces informations pour vérifier la version du moteur Docker, les capacités CPU et mémoire de l'hôte, ainsi que la configuration du stockage et des plugins réseau. Cela facilite la gestion et le dépannage des environnements Docker.









# Conclusion du cours sur Portainer

Portainer est un outil puissant pour la gestion simplifiée des environnements Docker, offrant une interface graphique intuitive permettant de superviser, configurer et administrer des conteneurs, volumes, réseaux et stacks Docker sans avoir à maîtriser les lignes de commande complexes. Grâce à ses fonctionnalités comme la gestion des logs, la surveillance des statistiques en temps réel, et l’inspection des conteneurs, il offre aux administrateurs et aux équipes de développement un contrôle total sur leurs environnements Docker. Portainer est donc idéal pour les environnements de production ou de développement où l'efficacité, la visibilité, et la simplicité sont essentielles.

Portainer permet également de créer et de déployer facilement des images Docker à partir de Dockerfiles, de gérer les réseaux et volumes, et d'administrer les ressources des hôtes Docker avec des options de configuration avancées. Il simplifie grandement les tâches administratives, rendant Docker plus accessible même pour ceux qui ne sont pas à l'aise avec la ligne de commande.

### Commandes équivalentes en Linux pour Docker

Voici un résumé des commandes Docker Linux équivalentes aux actions possibles dans Portainer :

1. **Créer un volume Docker :**
   ```bash
   docker volume create <nom_du_volume>
   ```

2. **Créer un réseau Docker :**
   ```bash
   docker network create --driver bridge <nom_du_réseau>
   ```

3. **Lister les conteneurs en cours d’exécution :**
   ```bash
   docker ps
   ```

4. **Inspecter un conteneur :**
   ```bash
   docker inspect <nom_du_conteneur>
   ```

5. **Visualiser les logs d’un conteneur :**
   ```bash
   docker logs <nom_du_conteneur>
   ```

6. **Démarrer un conteneur :**
   ```bash
   docker start <nom_du_conteneur>
   ```

7. **Arrêter un conteneur :**
   ```bash
   docker stop <nom_du_conteneur>
   ```

8. **Supprimer un conteneur :**
   ```bash
   docker rm <nom_du_conteneur>
   ```

9. **Supprimer une image Docker :**
   ```bash
   docker rmi <nom_de_l_image>
   ```

10. **Construire une image à partir d’un Dockerfile :**
    ```bash
    docker build -t <nom_image>:<tag> -f <chemin_vers_dockerfile> .
    ```

11. **Lancer un conteneur avec des options de port et de volume :**
    ```bash
    docker run -d -p <port_hôte>:<port_conteneur> -v <volume_hôte>:<volume_conteneur> <nom_image>
    ```

12. **Lister les volumes Docker :**
    ```bash
    docker volume ls
    ```

13. **Supprimer un volume Docker :**
    ```bash
    docker volume rm <nom_du_volume>
    ```

Ces commandes offrent la possibilité de gérer Docker de manière exhaustive via la ligne de commande, et sont complétées par les fonctionnalités graphiques de Portainer pour une gestion encore plus intuitive et visuelle.







# Annexe 2 - autres commandes

Je vous propose un guide exhaustif (ou rappel) avec **toutes les commandes Docker nécessaires** pour gérer les conteneurs, réseaux, volumes, images, ainsi que des exemples spécifiques pour entrer dans un conteneur, démarrer Flask, et déployer Portainer.

### Commandes Docker exhaustives

#### 1. **Commandes de base pour la gestion des conteneurs :**

- **Lister les conteneurs en cours d'exécution :**
  ```bash
  docker ps
  ```
  
- **Lister tous les conteneurs (inclus ceux qui sont arrêtés) :**
  ```bash
  docker ps -a
  ```

- **Démarrer un conteneur arrêté :**
  ```bash
  docker start <nom_du_conteneur> ou <ID_du_conteneur>
  ```

- **Arrêter un conteneur en cours d'exécution :**
  ```bash
  docker stop <nom_du_conteneur> ou <ID_du_conteneur>
  ```

- **Redémarrer un conteneur :**
  ```bash
  docker restart <nom_du_conteneur> ou <ID_du_conteneur>
  ```

- **Supprimer un conteneur :**
  ```bash
  docker rm <nom_du_conteneur> ou <ID_du_conteneur>
  ```

- **Supprimer un conteneur arrêté :**
  ```bash
  docker rm $(docker ps -a -q)
  ```

- **Créer et exécuter un nouveau conteneur :**
  ```bash
  docker run -d -p <port_hôte>:<port_conteneur> --name <nom_du_conteneur> <image_docker>
  ```

#### 2. **Accéder à un conteneur en cours d'exécution :**

- **Ouvrir un shell dans un conteneur :**
  ```bash
  docker exec -it <nom_du_conteneur> /bin/bash
  ```
  ou pour un conteneur léger (comme Alpine) :
  ```bash
  docker exec -it <nom_du_conteneur> /bin/sh
  ```

- **Ouvrir un terminal dans un conteneur déjà démarré :**
  ```bash
  docker attach <nom_du_conteneur> ou <ID_du_conteneur>
  ```

#### 3. **Logs et statistiques d’un conteneur :**

- **Afficher les logs d'un conteneur :**
  ```bash
  docker logs <nom_du_conteneur>
  ```

- **Suivre les logs en temps réel :**
  ```bash
  docker logs -f <nom_du_conteneur>
  ```

- **Obtenir les statistiques d'un conteneur (CPU, RAM, etc.) :**
  ```bash
  docker stats <nom_du_conteneur>
  ```

#### 4. **Gestion des images Docker :**

- **Lister toutes les images disponibles :**
  ```bash
  docker images
  ```

- **Télécharger une image Docker depuis DockerHub :**
  ```bash
  docker pull <nom_image>:<tag>
  ```

- **Supprimer une image Docker :**
  ```bash
  docker rmi <nom_image>
  ```

- **Construire une nouvelle image Docker à partir d'un Dockerfile :**
  ```bash
  docker build -t <nom_image>:<tag> .
  ```

#### 5. **Gestion des volumes Docker :**

- **Lister les volumes Docker :**
  ```bash
  docker volume ls
  ```

- **Créer un volume Docker :**
  ```bash
  docker volume create <nom_du_volume>
  ```

- **Supprimer un volume Docker :**
  ```bash
  docker volume rm <nom_du_volume>
  ```

- **Monter un volume dans un conteneur :**
  ```bash
  docker run -d -v <nom_du_volume>:/<chemin_dans_le_conteneur> <image>
  ```

#### 6. **Gestion des réseaux Docker :**

- **Lister les réseaux Docker :**
  ```bash
  docker network ls
  ```

- **Créer un réseau Docker :**
  ```bash
  docker network create <nom_du_réseau>
  ```

- **Supprimer un réseau Docker :**
  ```bash
  docker network rm <nom_du_réseau>
  ```

- **Connecter un conteneur à un réseau spécifique :**
  ```bash
  docker network connect <nom_du_réseau> <nom_du_conteneur>
  ```

- **Déconnecter un conteneur d'un réseau :**
  ```bash
  docker network disconnect <nom_du_réseau> <nom_du_conteneur>
  ```

#### 7. **Démarrer une application Flask dans Docker :**

Si tu veux démarrer une application Flask dans un conteneur Docker, voici comment procéder :

1. **Créer un fichier `Dockerfile` pour Flask :**
   ```Dockerfile
   FROM python:3.8-slim
   WORKDIR /app
   COPY requirements.txt requirements.txt
   RUN pip install -r requirements.txt
   COPY . .
   CMD ["python", "app.py"]
   ```

2. **Créer un fichier `requirements.txt` pour les dépendances :**
   ```text
   Flask
   ```

3. **Construire l'image Docker :**
   ```bash
   docker build -t flask-app .
   ```

4. **Exécuter le conteneur Flask :**
   ```bash
   docker run -d -p 5000:5000 --name flask-container flask-app
   ```

5. **Accéder à Flask :**  
   Ouvre ton navigateur et accède à [http://localhost:5000](http://localhost:5000).

#### 8. **Déployer Portainer :**

Pour installer et déployer Portainer sur Docker, voici les étapes :

1. **Créer un volume pour les données persistantes de Portainer :**
   ```bash
   docker volume create portainer_data
   ```

2. **Déployer Portainer :**
   ```bash
   docker run -d -p 8000:8000 -p 9000:9000 \
   --name portainer --restart=always \
   -v /var/run/docker.sock:/var/run/docker.sock \
   -v portainer_data:/data portainer/portainer-ce
   ```

3. **Accéder à l'interface de Portainer :**  
   Ouvre ton navigateur et va à [http://localhost:9000](http://localhost:9000) pour configurer l'interface et créer l'utilisateur administrateur.

#### 9. **Commandes avancées pour Docker :**

- **Inspecter les détails d'un conteneur (configuration, réseau, etc.) :**
  ```bash
  docker inspect <nom_du_conteneur>
  ```

- **Exécuter une commande spécifique à l'intérieur d'un conteneur sans entrer dans le shell :**
  ```bash
  docker exec <nom_du_conteneur> <commande>
  ```

- **Entrer dans un conteneur arrêté (à partir de son point de sauvegarde) :**
  ```bash
  docker commit <nom_du_conteneur>
  docker start <nouveau_conteneur>
  ```

#### 10. **Sauvegarder et charger des images :**

- **Exporter une image Docker dans un fichier :**
  ```bash
  docker save -o <fichier_tar> <nom_image>
  ```

- **Charger une image Docker depuis un fichier :**
  ```bash
  docker load -i <fichier_tar>
  ```

### Conclusion

Avec cet ensemble complet de commandes Docker, tu es en mesure de :
- Gérer des conteneurs (création, démarrage, arrêt, logs, statistiques, accès aux shells).
- Gérer les réseaux et volumes Docker.
- Démarrer des applications comme Flask ou déployer Portainer.
- Construire, exporter et importer des images Docker.

Ces commandes te permettent de couvrir tous les aspects essentiels et avancés de la gestion Docker dans n'importe quel environnement, que ce soit pour le développement ou la production.



































