

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


### 1.3  Ansible documentation

![image](https://github.com/user-attachments/assets/275ab69e-3203-434e-b567-bbaf627d34ec)



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


sinon 

![image](https://github.com/user-attachments/assets/419efa38-c38d-4a25-9f2a-9616c2b377a2)



[Retour à la table des matières](#table-des-matières)

---

## 3. Ansible Inventory <a id="ansible-inventory"></a>

L'inventaire Ansible est un fichier texte qui contient la liste des hôtes sur lesquels les commandes Ansible vont être exécutées. Un inventaire basique ressemble à ceci :

```ini
[webservers]
192.168.1.10
192.168.1.11
```

ou 

![image](https://github.com/user-attachments/assets/f22d33d3-1832-4b3c-8edf-3e94572681d8)

ou 

![image](https://github.com/user-attachments/assets/5f3a3d12-4a82-4c47-a307-ad6a1b824cab)


Les hôtes peuvent être regroupés pour simplifier la gestion. Ansible interagit avec les hôtes spécifiés dans ce fichier lors de l'exécution des *playbooks*.

![image](https://github.com/user-attachments/assets/7758b6ee-8060-4daf-b85e-e2af644ec3f4)


### 3.1. Ansible inventory

![image](https://github.com/user-attachments/assets/ced018e2-c07e-4e6b-8e17-6d1d44a987f4)

![image](https://github.com/user-attachments/assets/68d6954f-6d22-46d2-a9d3-0397e8334f27)








[Retour à la table des matières](#table-des-matières)

---

## 4. Qu'est-ce que YAML ? <a id="yaml"></a>
YAML (YAML Ain't Markup Language) est un langage de sérialisation de données qui est lisible par les humains. Ansible utilise YAML pour définir ses *playbooks*. Un fichier YAML est structuré avec des indentations pour définir des hiérarchies.

![image](https://github.com/user-attachments/assets/ce84afa6-fa9c-4b8c-903b-189c840d12d1)



### 4.2. exemples

#### Exemples 1

![image](https://github.com/user-attachments/assets/0fc867cf-c102-42f8-93ce-e0d99f74bf1b)

![image](https://github.com/user-attachments/assets/8338d3d5-2772-455f-a62d-6eb21a771295)

![image](https://github.com/user-attachments/assets/dd6c435e-1d58-4879-98e2-071868d7fea5)

![image](https://github.com/user-attachments/assets/3cf3b25a-fe5d-4a07-8dfe-651284f44a51)



####  Exemple 2  de structure YAML :

```yaml
- nom: Installation d'Apache
  hôtes: webservers
  tâches:
    - nom: Installer Apache
      apt:
        nom: apache2
        état: installé
```

# 4.3. Dictionnaires vs Listes en Yaml

![image](https://github.com/user-attachments/assets/0395f277-d75c-46c0-bb45-ab79902b91f2)



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

