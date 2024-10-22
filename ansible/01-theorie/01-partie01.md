

# Table des matières
1. [Définition de Ansible](#definition-ansible)
2. [Installer Ansible](#installer-ansible)
3. [Ansible Inventory](#ansible-inventory)
4. [Qu'est-ce que YAML?](#yaml)
5. [Ansible Playbook](#ansible-playbook)
6. [Ansible Modules (fichier de commandes)](#ansible-modules)
7. [Condition dans Ansible](#ansible-condition)
8. [Boucles dans Ansible](#ansible-loop)

---

## 1. Définition de Ansible <a id="definition-ansible"></a>
Ansible est un outil de gestion de configuration et d'automatisation open-source. Il permet d'automatiser les tâches de déploiement, la gestion des configurations, et l'orchestration d'infrastructure. Sa simplicité repose sur l'utilisation de fichiers texte au format YAML appelés *playbooks* qui décrivent les actions à exécuter sur des serveurs distants.

### 1.1 Pourquoi anssible <a id="pk-ansible"></a>

![image](https://github.com/user-attachments/assets/9652e23a-ef47-460b-8d50-2c3121385755)

### 1.2  Script vs Ansible playbook 

![image](https://github.com/user-attachments/assets/5cd25c80-60f3-4734-b0fb-bc82e7c2c53e)



[Retour à la table des matières](#table-des-matières)

---

## 2. Installer Ansible <a id="installer-ansible"></a>
L'installation d'Ansible varie selon le système d'exploitation. Voici un exemple pour un environnement Linux (Ubuntu) :

```bash
sudo apt update
sudo apt install ansible
```

Pour vérifier l'installation, utilisez la commande suivante :

```bash
ansible --version
```

[Retour à la table des matières](#table-des-matières)

---

## 3. Ansible Inventory <a id="ansible-inventory"></a>
L'inventaire Ansible est un fichier texte qui contient la liste des hôtes sur lesquels les commandes Ansible vont être exécutées. Un inventaire basique ressemble à ceci :

```ini
[webservers]
192.168.1.10
192.168.1.11
```

Les hôtes peuvent être regroupés pour simplifier la gestion. Ansible interagit avec les hôtes spécifiés dans ce fichier lors de l'exécution des *playbooks*.

[Retour à la table des matières](#table-des-matières)

---

## 4. Qu'est-ce que YAML ? <a id="yaml"></a>
YAML (YAML Ain't Markup Language) est un langage de sérialisation de données qui est lisible par les humains. Ansible utilise YAML pour définir ses *playbooks*. Un fichier YAML est structuré avec des indentations pour définir des hiérarchies.

Exemple simple de structure YAML :

```yaml
- nom: Installation d'Apache
  hôtes: webservers
  tâches:
    - nom: Installer Apache
      apt:
        nom: apache2
        état: installé
```

[Retour à la table des matières](#table-des-matières)

---

## 5. Ansible Playbook <a id="ansible-playbook"></a>
Un *playbook* est un fichier YAML qui décrit une série de tâches à exécuter sur des hôtes. Il contient les instructions nécessaires pour configurer des serveurs, installer des logiciels, ou automatiser des processus. 

Exemple de playbook :

```yaml
- nom: Déployer une application web
  hôtes: webservers
  tâches:
    - nom: Installer nginx
      apt:
        nom: nginx
        état: présent
```

[Retour à la table des matières](#table-des-matières)

---

## 6. Ansible Modules (fichier de commandes) <a id="ansible-modules"></a>
Les modules dans Ansible sont les composants qui exécutent les tâches. Ils sont comme des scripts pré-écrits que Ansible utilise pour interagir avec les systèmes cibles. Il existe des modules pour presque toutes les tâches : gestion des paquets, gestion des utilisateurs, déploiement d'applications, etc.

Exemple de module pour gérer les utilisateurs :

```yaml
- nom: Ajouter un utilisateur
  user:
    nom: "utilisateur1"
    état: "présent"
```

[Retour à la table des matières](#table-des-matières)

---

## 7. Condition dans Ansible <a id="ansible-condition"></a>
Dans Ansible, on peut exécuter des tâches en fonction de conditions spécifiques grâce aux directives `when`. Cela permet d'exécuter une tâche uniquement si certaines conditions sont remplies.

Exemple :

```yaml
- nom: Redémarrer Apache si la configuration a changé
  service:
    nom: apache2
    état: redémarrer
  when: apache_config_changed
```

[Retour à la table des matières](#table-des-matières)

---

## 8. Boucles dans Ansible <a id="ansible-loop"></a>
Les boucles permettent d'exécuter une tâche plusieurs fois avec des paramètres différents. Pour cela, Ansible utilise la directive `with_items`.

Exemple de boucle :

```yaml
- nom: Installer plusieurs paquets
  apt:
    nom: "{{ item }}"
    état: présent
  with_items:
    - nginx
    - apache2
```

[Retour à la table des matières](#table-des-matières)

