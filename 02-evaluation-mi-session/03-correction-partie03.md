# Correction:

- Vous trouverez la correction avec une ❌ devant chaque erreur et ✅ devant chaque correction.


## **1️⃣ Playbook facile – Erreur d'indentation**
```yaml
---
- name: Installer un package sur Ubuntu
  hosts: all
  become: yes
  tasks:
    - name: Installer Apache
❌    apt:
✅      apt:
        name: apache2
        state: present
```
---

## **2️⃣ Playbook facile – Mauvais module utilisé**
```yaml
---
- name: Mettre à jour le système
  hosts: all
  become: yes
  tasks:
    - name: Mise à jour du système
❌      yum:
✅      apt:
        update_cache: yes
```
---

## **3️⃣ Playbook facile – Variable incorrecte**
```yaml
---
- name: Créer un utilisateur
  hosts: all
  become: yes
  vars:
    user: "deploy"
  tasks:
    - name: Ajouter l'utilisateur
      user:
❌        name: "{{ utilisateur }}"
✅        name: "{{ user }}"
        shell: /bin/bash
```
---

## **4️⃣ Playbook intermédiaire – Mauvais nom de package selon l'OS**
```yaml
---
- name: Installer Docker
  hosts: all
  become: yes
  tasks:
    - name: Installer Docker sur Ubuntu et CentOS
❌      yum:
✅      package:
        name: docker
        state: present
```
---

## **5️⃣ Playbook intermédiaire – Mauvaise indentation et notify mal placé**
```yaml
---
- name: Installer Nginx et ouvrir le port 80
  hosts: all
  become: yes
  tasks:
    - name: Installer Nginx
      apt:
        name: nginx
        state: latest
❌      notify:
✅    notify:
      - Restart Nginx

    - name: Ouvrir le port 80
      firewalld:
        service: http
        permanent: true
        state: enabled

  handlers:
  - name: Restart Nginx
❌    service:
✅    service:
      name: nginx
      state: restarted
```
---

## **6️⃣ Playbook intermédiaire – Erreur sur le service SSH**
```yaml
---
- name: Configurer SSH
  hosts: all
  become: yes
  tasks:
    - name: Modifier la configuration SSH
      lineinfile:
        path: /etc/ssh/sshd_config
        line: "PermitRootLogin no"
        state: present

    - name: Redémarrer le service SSH
      service:
❌        name: ssh
✅        name: sshd
        state: restarted
```
---

## **7️⃣ Playbook avancé – Mauvais module pour AlmaLinux**
```yaml
---
- name: Installation de packages et services
  hosts: all
  become: yes
  tasks:
    - name: Mettre à jour le cache des paquets
      yum:
        update_cache: yes

    - name: Installer Apache
❌      apt:
✅      package:
        name: httpd
        state: latest
```
---

## **8️⃣ Playbook avancé – Erreur de conditionnel et mauvaise boucle**
```yaml
---
- name: Installer plusieurs packages
  hosts: all
  become: yes
  tasks:
    - name: Installer les packages
      package:
        name: "{{ item }}"
        state: present
❌      when: ansible_os_family == "Debian"
✅    when: ansible_os_family == "Debian"
      loop:
        - apache2
        - nginx
        - mysql-server
```
---

## **9️⃣ Playbook difficile – Mauvaise gestion de clé SSH**
```yaml
---
- name: Configurer SSH pour un nouvel utilisateur
  hosts: all
  become: yes
  vars:
    new_user: "deploy"
  tasks:
    - name: Ajouter l'utilisateur
      user:
        name: "{{ new_user }}"
        shell: "/bin/bash"
        groups: sudo

    - name: Ajouter la clé SSH
      copy:
        src: ~/.ssh/id_rsa.pub
        dest: "/home/{{ new_user }}/.ssh/authorized_keys"
❌        owner: "{{ newuser }}"
✅        owner: "{{ new_user }}"
        mode: "0600"
```
---

## **🔟 Playbook très difficile – Multiples erreurs de syntaxe, de variable et de gestion de service**
```yaml
---
- name: Configurer et sécuriser un serveur
  hosts: all
  become: yes
  tasks:
    - name: Installer des outils de base
      apt:
        update_cache: yes
❌        name:
✅        pkg:
          - vim
          - git
          - curl
          - wget
          - unzip

    - name: Ajouter un utilisateur
      user:
❌        name: "{{ deploy_user }}"
✅        name: "deploy"
        shell: /bin/bash
        groups: sudo
        state: present

    - name: Désactiver le root SSH
      lineinfile:
        path: /etc/ssh/sshd_config
        line: "PermitRootLogin no"
        state: present
      notify: Restart SSH

    - name: Activer et démarrer le firewall
      firewalld:
❌        service: ssh
✅        port: 22/tcp
        permanent: true
        state: enabled

  handlers:
  - name: Restart SSH
❌    service:
✅    systemd:
      name: sshd
      state: restarted
```
---

## **Votre Mission 🎯**
1. **Chaque apprenant doit identifier les erreurs marquées avec ❌ et proposer la correction marquée avec ✅.**  
2. **Les apprenants doivent expliquer pourquoi chaque correction est nécessaire.**  
3. **Les apprenants exécutent ensuite les playbooks corrigés pour valider les changements.**  

Difficulté **progressive** pour tester leur compréhension d’Ansible et leur logique de debug !
