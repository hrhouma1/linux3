# **Cours Ansible**

## **Table des Matières**
1. [Qu'est-ce qu'Ansible ?](#section1)
2. [Pourquoi utiliser Ansible ?](#section2)
3. [Comment fonctionne Ansible ?](#section3)
4. [SSH](#section4)
5. [Inventaire (Inventory)](#section5)
6. [Playbook](#section6)
7. [Modules](#section7)
8. [Roles](#section8)
9. [Écrire des scripts](#section9)
10. [Ansible Galaxy](#section10)

---

## <a name="section1"></a> 1. Qu'est-ce qu'Ansible ?

Ansible est un outil open-source conçu pour l'automatisation des tâches IT telles que la gestion de configurations, le déploiement d'applications, et la gestion de serveurs.

- **Sans agent** : Aucun logiciel client n'est requis sur les machines cibles.
- **Efficace et extensible** : Utilisé pour des infrastructures de toutes tailles.

[Retour à la Table des Matières](#)

---

## <a name="section2"></a> 2. Pourquoi utiliser Ansible ?

- **Facilité d’utilisation** : Syntaxe simple en YAML, pas besoin d'écrire du code complexe.
- **Sans agent** : Utilisation de SSH pour interagir avec les machines, sans configuration complexe.
- **Modularité** : Les rôles et les playbooks rendent le code réutilisable et bien structuré.

[Retour à la Table des Matières](#)

---

## <a name="section3"></a> 3. Comment fonctionne Ansible ?

Ansible fonctionne en se connectant aux machines via SSH et en exécutant des modules prédéfinis. Il utilise des playbooks pour orchestrer une série de tâches sur plusieurs hôtes.

- **Playbook** : Un fichier YAML qui définit les étapes à exécuter.
- **Modules** : Scripts prédéfinis pour accomplir des tâches spécifiques.

[Retour à la Table des Matières](#)

---

## <a name="section4"></a> 4. SSH

Ansible utilise SSH pour se connecter aux machines distantes et exécuter des commandes.

- **Clés SSH** : Authentification sans mot de passe pour des connexions sécurisées.
- **Exemple** :
  ```bash
  ssh-keygen -t rsa
  ssh-copy-id user@host
  ```

[Retour à la Table des Matières](#)

---

## <a name="section5"></a> 5. Inventaire (Inventory)

L'inventaire est une liste de machines que vous souhaitez gérer avec Ansible.

- **Fichier d'inventaire statique** :
  ```ini
  [webservers]
  192.168.1.1
  192.168.1.2

  [dbservers]
  192.168.1.10
  ```

- **Inventaire dynamique** : Permet d'intégrer des systèmes de gestion cloud pour découvrir les machines automatiquement.

[Retour à la Table des Matières](#)

---

## <a name="section6"></a> 6. Playbook

Un playbook est un fichier YAML qui contient une liste de tâches à exécuter sur des machines cibles.

- **Exemple de playbook simple** :
  ```yaml
  - hosts: webservers
    tasks:
      - name: Installer Apache
        apt:
          name: apache2
          state: present
  ```

[Retour à la Table des Matières](#)

---

## <a name="section7"></a> 7. Modules

Les modules dans Ansible sont des scripts qui exécutent des actions spécifiques.

- **Types de modules** :
  - **Modules système** : Gestion des paquets, des utilisateurs, etc.
  - **Modules cloud** : AWS, Azure, GCP.
  - **Modules réseau** : Configurations d'équipements réseau.

- **Exemple de module** :
  ```yaml
  - name: Créer un fichier
    file:
      path: /etc/config.cfg
      state: touch
  ```

[Retour à la Table des Matières](#)

---

## <a name="section8"></a> 8. Roles

Les rôles permettent d'organiser et de réutiliser le code Ansible de manière modulaire.

- **Structure d'un rôle** :
  - `tasks/`: Contient les tâches.
  - `handlers/`: Actions déclenchées après les modifications.
  - `templates/`: Fichiers de modèles Jinja2.

- **Exemple** : Créer un rôle pour installer et configurer un serveur Nginx.

[Retour à la Table des Matières](#)

---

## <a name="section9"></a> 9. Écrire des scripts

- **Exercice pratique** : Écrire un playbook pour installer un serveur Apache sur un groupe de machines.
- **Extension** : Ajouter des rôles pour configurer la sécurité SSH.

[Retour à la Table des Matières](#)

---

## <a name="section10"></a> 10. Ansible Galaxy

Ansible Galaxy est une plateforme où les utilisateurs peuvent partager des rôles et les télécharger.

- **Rechercher un rôle** :
  ```bash
  ansible-galaxy search role_name
  ```

- **Installer un rôle** :
  ```bash
  ansible-galaxy install role_name
  ```

[Retour à la Table des Matières](#)

