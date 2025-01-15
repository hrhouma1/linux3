---
title: "Chapitre 10 - Glossaire des Facts Ansible"
description: "Un guide complet des facts Ansible pour mieux comprendre et utiliser les informations système"
emoji: "📚"
---

<a name="table-des-matières"></a>
# Table des matières

- [01 - Introduction](#introduction)
- [02 - Comment accéder aux Facts](#comment-accéder-aux-facts)
- [03 - Utilisation des Facts dans les Playbooks](#utilisation-des-facts-dans-les-playbooks)
- [04 - Conclusion](#conclusion)

---
<a name="introduction"></a>
# 1 - Introduction

Les "facts" dans Ansible sont des variables qui contiennent des informations détaillées sur les systèmes cibles. Ils sont automatiquement collectés par Ansible lors de l'exécution d'un playbook.

#### [Retour à la table des matières](#table-des-matières)

---

<a name="comment-accéder-aux-facts"></a>
# 2 - Comment accéder aux Facts

Les facts sont accessibles dans les templates Jinja2 via la variable `ansible_facts`.

## Exemples de Facts

- `ansible_fqdn` : Le nom de domaine complet du système.
- `ansible_hostname` : Le nom d'hôte du système.
- `ansible_os_family` : La famille d'OS du système (par exemple, `RedHat`, `Debian`, etc.).
- `ansible_architecture` : L'architecture du processeur du système (par exemple, `x86_64`, `aarch64`, etc.).

#### [Retour à la table des matières](#table-des-matières)

<a name="utilisation-des-facts-dans-les-playbooks"></a>
# 3 - Utilisation des Facts dans les Playbooks

Les facts peuvent être utilisés pour personnaliser les tâches dans un playbook. Par exemple, vous pouvez utiliser `ansible_fqdn` pour définir un nom de fichier unique pour chaque système.


#### [Retour à la table des matières](#table-des-matières)


# 4 - **Glossaire des Facts Ansible**

Ce glossaire présente les principaux **facts** qu'Ansible collecte automatiquement sur les machines cibles. Ces facts sont utiles pour rendre vos playbooks dynamiques, en récupérant des informations sur le système, le réseau, les ressources matérielles, etc.

#### [Retour à la table des matières](#table-des-matières)

---


#### **4.1 - Facts sur le système d'exploitation**

```ini

| Fact                     | Description                                         | Exemple                      |
|--------------------------|-----------------------------------------------------|------------------------------|
| `ansible_os_family`       | Famille du système d'exploitation.                  | Debian, RedHat, Windows       |
| `ansible_distribution`    | Nom de la distribution Linux/Windows.                | Ubuntu, CentOS, Windows       |
| `ansible_distribution_version` | Version de la distribution.                   | 20.04, 7, 2019               |
| `ansible_kernel`          | Version du kernel du système d'exploitation.         | 5.15.0-58-generic             |
| `ansible_machine`         | Architecture de la machine cible.                   | x86_64, armv7l                |
| `ansible_virtualization_type` | Type de virtualisation.                         | kvm, vmware, physical         |
| `ansible_virtualization_role` | Rôle dans la virtualisation (hôte ou invité).    | host, guest                   |

```

#### [Retour à la table des matières](#table-des-matières)

---

#### **4.2 - Facts sur le matériel**

```ini
| Fact                     | Description                                         | Exemple                      |
|--------------------------|-----------------------------------------------------|------------------------------|
| `ansible_processor`       | Liste des processeurs (CPU).                        | [Intel(R) Core(TM) i7]        |
| `ansible_processor_vcpus` | Nombre de CPU virtuels.                             | 8                            |
| `ansible_memtotal_mb`     | Taille totale de la mémoire RAM en MB.              | 8192 MB                      |
| `ansible_memfree_mb`      | Mémoire RAM libre en MB.                            | 4096 MB                      |
| `ansible_swapfree_mb`     | Espace de swap libre en MB.                         | 2048 MB                      |
| `ansible_swaptotal_mb`    | Taille totale de l’espace swap en MB.               | 4096 MB                      |
| `ansible_product_name`    | Modèle de la machine.                               | "ThinkPad X1 Carbon Gen 8"    |
| `ansible_system_vendor`   | Fabricant du système.                               | "Lenovo"                     |

```

#### [Retour à la table des matières](#table-des-matières)

---

#### **4.3 - Facts sur le réseau**


```ini
| Fact                     | Description                                         | Exemple                      |
|--------------------------|-----------------------------------------------------|------------------------------|
| `ansible_default_ipv4`    | Informations sur l'interface réseau par défaut.     | {"address": "192.168.1.100"}  |
| `ansible_default_ipv4.address` | Adresse IP par défaut.                        | 192.168.1.100                 |
| `ansible_default_ipv4.gateway` | Passerelle par défaut.                        | 192.168.1.1                   |
| `ansible_all_ipv4_addresses` | Liste de toutes les adresses IP IPv4.            | ["192.168.1.100"]             |
| `ansible_all_ipv6_addresses` | Liste de toutes les adresses IP IPv6.            | ["fe80::1"]                   |
| `ansible_interfaces`      | Liste des interfaces réseau de la machine.          | ["eth0", "lo"]                |
| `ansible_hostname`        | Nom d'hôte de la machine.                           | web-server-01                 |
| `ansible_fqdn`            | Nom de domaine complet (Fully Qualified Domain Name). | web-server-01.example.com |
| `ansible_domain`          | Nom de domaine de la machine (si configuré).        | example.com                   |
| `ansible_macaddress`      | Adresse MAC de l'interface principale.              | "00:11:22:33:44:55"           |

```

#### [Retour à la table des matières](#table-des-matières)

---

#### **4.4 - Facts sur le stockage**

```ini

| Fact                     | Description                                         | Exemple                      |
|--------------------------|-----------------------------------------------------|------------------------------|
| `ansible_mounts`          | Liste des points de montage.                        | [{"mount": "/", "size_total": 500000000000}] |
| `ansible_mounts[0].mount` | Point de montage principal.                         | "/"                          |
| `ansible_mounts[0].size_total` | Taille totale du volume.                       | 500000000000 bytes            |
| `ansible_mounts[0].size_available` | Espace libre du volume.                    | 300000000000 bytes            |

```

#### [Retour à la table des matières](#table-des-matières)

---

#### **4.5 - Facts sur les utilisateurs et la sécurité**


```ini
| Fact                     | Description                                         | Exemple                      |
|--------------------------|-----------------------------------------------------|------------------------------|
| `ansible_user_id`         | ID de l’utilisateur connecté.                       | ubuntu                       |
| `ansible_effective_user_id` | ID de l’utilisateur effectif utilisé par Ansible.  | root                         |
| `ansible_env.HOME`        | Répertoire `HOME` de l’utilisateur.                  | /home/ubuntu                  |
| `ansible_env.USER`        | Nom d’utilisateur du système.                       | ubuntu                       |

```

#### [Retour à la table des matières](#table-des-matières)

---

#### **4.6 - Facts sur les dates et horaires**


```ini
| Fact                     | Description                                         | Exemple                      |
|--------------------------|-----------------------------------------------------|------------------------------|
| `ansible_date_time.date`  | Date actuelle.                                       | "2025-01-06"                 |
| `ansible_date_time.time`  | Heure actuelle.                                      | "12:45:00"                   |
| `ansible_date_time.tz`    | Fuseau horaire configuré.                            | "UTC"                        |
| `ansible_date_time.iso8601` | Date/heure au format ISO 8601.                     | "2025-01-06T12:45:00Z"       |

```

#### [Retour à la table des matières](#table-des-matières)
---

#### **4.7 - Facts sur la version Ansible**

```ini

| Fact                     | Description                                         | Exemple                      |
|--------------------------|-----------------------------------------------------|------------------------------|
| `ansible_version.full`    | Version complète d’Ansible installée.               | "2.14.1"                     |
| `ansible_version.major`   | Numéro de version majeure.                          | "2"                          |
| `ansible_version.minor`   | Numéro de version mineure.                          | "14"                         |

```

#### [Retour à la table des matières](#table-des-matières)

---

#### **4.8 - Commandes pour afficher les facts :**

1. **Afficher tous les facts :**
   ```bash
   ansible all -m ansible.builtin.setup
   ```
2. **Afficher les facts filtrés :**
   ```bash
   ansible all -m ansible.builtin.setup -a "filter=ansible_distribution*"
   ```
3. **Afficher uniquement les adresses IP :**
   ```bash
   ansible all -m ansible.builtin.setup -a "filter=ansible_all_ipv4_addresses"
   ```

#### [Retour à la table des matières](#table-des-matières)

---

### **Exemple d'utilisation dans un playbook :**
```yaml
- name: Affiche des facts sur l'hôte
  hosts: all
  tasks:
    - name: Affiche le système d'exploitation
      debug:
        msg: "OS : {{ ansible_distribution }} {{ ansible_distribution_version }}"
        
    - name: Affiche l'adresse IP principale
      debug:
        msg: "Adresse IP : {{ ansible_default_ipv4.address }}"
```

#### [Retour à la table des matières](#table-des-matières)

---

### **Bonnes pratiques :**
- Utilisez des facts pour **adapter vos tâches** selon le système d'exploitation, la mémoire disponible, etc.
- Si certaines facts sont inutiles, désactivez leur collecte pour **accélérer l'exécution** :
  ```yaml
  gather_facts: false
  ```
- Pour des faits personnalisés, créez vos propres **facts locaux** en utilisant des scripts dans `/etc/ansible/facts.d/`.

---

Avec ce **glossaire des facts**, vous pouvez automatiser vos déploiements de manière plus intelligente en vous basant sur les informations collectées automatiquement par Ansible ! 

#### [Retour à la table des matières](#table-des-matières)

---

<a name="conclusion"></a>
# 5 - Conclusion

Les facts sont une source précieuse d'informations système dans Ansible. Ils permettent de créer des playbooks plus dynamiques et flexibles.