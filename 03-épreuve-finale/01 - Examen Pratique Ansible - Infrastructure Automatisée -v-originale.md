# Examen Pratique Ansible - Infrastructure Automatisée

- **Durée**: 3 heures
- **Points**: 100 points

# Partie 1: Configuration Initiale 

1. Créez une infrastructure Docker avec les conteneurs suivants:
- 2 conteneurs Ubuntu (web1, web2)
- 2 conteneurs Debian (db1, db2)
- 2 conteneurs AlmaLinux (mail1, mail2)

2. Configurez l'accès SSH et assurez la connectivité Ansible vers tous les conteneurs.

# Partie 2: Gestion des Groupes et Inventaire 

Créez un fichier d'inventaire Ansible structuré avec:
- Groupe `webservers` pour web1 et web2
- Groupe `databases` pour db1 et db2
- Groupe `mailservers` pour mail1 et mail2
- Variables spécifiques pour chaque groupe

# Partie 3: Automatisation Multi-Plateforme 

Écrivez un playbook qui:
1. Installe Apache sur les serveurs web
2. Installe MariaDB sur les serveurs de base de données
3. Installe Postfix sur les serveurs mail
4. Gère correctement les différences entre distributions (apt vs yum)
5. Utilise des variables pour les configurations

# Partie 4: Manipulation de Données 

Créez un playbook qui:
1. Crée 5 utilisateurs avec des mots de passe hashés
2. Configure des droits sudo spécifiques pour chaque utilisateur
3. Utilise des boucles pour gérer les fichiers de configuration
4. Implémente une gestion d'erreurs appropriée

# Partie 5: Monitoring et Rapports 

Développez un playbook qui:
1. Collecte des informations système de tous les serveurs
2. Crée un rapport HTML avec les informations collectées
3. Utilise des conditions basées sur les facts Ansible
4. Implémente des handlers pour les services

**Critères d'Évaluation:**
- Fonctionnalité 
- Organisation du code 
- Bonnes pratiques Ansible 
- Gestion des erreurs 
- Documentation 

**Livrables:**
- Tous les fichiers de configuration Docker
- Inventaire Ansible
- Playbooks
- Documentation expliquant l'approche

**Bonus (10 points):**
- Implémentation de rôles Ansible personnalisés
- Utilisation d'Ansible Vault pour les données sensibles
- Tests automatisés des playbooks




# Grille d'Évaluation - Examen Pratique Ansible : Infrastructure Automatisée

| **Critères**                                      | **Détails**                                                                                           | **Points**  |
|---------------------------------------------------|-------------------------------------------------------------------------------------------------------|-------------|
| **1. Commentaires et Documentation**             | - Présence de commentaires pertinents et clairs sur chaque partie du code (playbooks, fichiers).      | **60 points** |
|                                                   | - Critique constructive et explication des erreurs ou améliorations possibles dans le code.           |             |
|                                                   | - Descriptions détaillées des étapes et des décisions techniques dans une documentation fournie.      |             |
| **2. Étape 1 : Infrastructure Docker**           | - Fournir des captures d'écran montrant la réussite de la création des conteneurs et de l'accès SSH.  | **10 points** |
|                                                   | - Validation en direct auprès de l’enseignant pour confirmer l’exécution réussie.                     |             |
| **3. Étape 2 : Inventaire Ansible**               | - Création d’un fichier d’inventaire structuré avec des groupes et des variables précises.            | **10 points** |
|                                                   | - Validation en direct pour s’assurer que l’inventaire fonctionne avec les commandes Ansible.         |             |
| **4. Étape 3 : Automatisation Multi-Plateforme**  | - Playbook fonctionnel qui installe et configure Apache, MariaDB et Postfix selon les spécifications. | **10 points** |
|                                                   | - Validation en direct des exécutions réussies et des configurations finales.                         |             |
| **5. Étape 4 : Manipulation de Données**          | - Playbook créant 5 utilisateurs avec mots de passe hashés, utilisant des boucles et droits sudo.     | **5 points** |
|                                                   | - Validation en direct de la création et de la gestion des utilisateurs.                             |             |
| **6. Étape 5 : Monitoring et Rapports**           | - Collecte d'informations système et création d'un rapport HTML automatisé avec Ansible.             | **5 points** |
|                                                   | - Validation en direct de la génération du rapport et de son contenu.                                |             |

---

# **Répartition Totale des Points**

| **Critères Globaux**         | **Points Totaux** |
|------------------------------|-------------------|
| Commentaires et Documentation | **60%**          |
| Étape 1 : Infrastructure      | **10%**          |
| Étape 2 : Inventaire          | **10%**          |
| Étape 3 : Automatisation      | **10%**          |
| Étape 4 : Manipulation        | **5%**           |
| Étape 5 : Monitoring          | **5%**           |

---

# **Instructions**

1. **Commentaires et Documentation (60%)** : 
   - Ajoutez des commentaires clairs dans vos fichiers, playbooks, et inventaires.
   - Critiquez et identifiez les erreurs ou points d'amélioration possibles.
   - Fournissez une documentation expliquant vos choix techniques, les difficultés rencontrées, et les solutions.

2. **Étape 1 : Infrastructure Docker (10%)** :
   - Fournissez des captures d'écran montrant la création des conteneurs et leur connectivité Ansible.
   - Appelez l’enseignant pour valider l’exécution réussie.

3. **Étape 2 : Inventaire Ansible (10%)** :
   - Créez un inventaire structuré avec des groupes et des variables spécifiques.
   - Appelez l’enseignant pour valider son fonctionnement (via commandes comme `ansible-inventory` ou `ping`).

4. **Étape 3 : Automatisation (10%)** :
   - Fournissez un playbook gérant correctement les différences entre distributions et atteignant les objectifs.
   - Appelez l’enseignant pour valider son exécution réussie.

5. **Étape 4 : Manipulation de Données (5%)** :
   - Créez les utilisateurs, attribuez des droits sudo, et utilisez des boucles.
   - Appelez l’enseignant pour valider la gestion correcte des utilisateurs.

6. **Étape 5 : Monitoring et Rapports (5%)** :
   - Collectez des informations système et générez un rapport HTML.
   - Appelez l’enseignant pour vérifier que le rapport est généré et complet.

---

# **Bonus :**
- Implémentez des rôles Ansible personnalisés ou utilisez Ansible Vault pour protéger les données sensibles. (+10 points)

---

# **Note Importante :**
- Aucune étape n’est considérée comme complète sans validation directe auprès de l’enseignant.
- Appelez après chaque étape pour garantir une évaluation juste.


