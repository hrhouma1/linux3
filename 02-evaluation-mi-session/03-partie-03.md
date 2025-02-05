# Trouvez les erreurs

## **1️⃣ Niveau facile**
```yaml
---
- name: Installer un package sur Ubuntu
  hosts: all
  become: yes
  tasks:
    - name: Installer Apache
    apt:
      name: apache2
      state: present
```

---

## **2️⃣ Niveau facile**
```yaml
---
- name: Mettre à jour le système
  hosts: all
  become: yes
  tasks:
    - name: Mise à jour du système
      yum:
        update_cache: yes
```

---

## **3️⃣ Niveau facile**
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
        name: "{{ utilisateur }}"
        shell: /bin/bash
```

---

## **4️⃣ Niveau intermédiaire**
```yaml
---
- name: Installer Docker
  hosts: all
  become: yes
  tasks:
    - name: Installer Docker sur Ubuntu et CentOS
      yum:
        name: docker
        state: present
```

---

## **5️⃣ Niveau intermédiaire**
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
      notify:
      - Restart Nginx

    - name: Ouvrir le port 80
      firewalld:
        service: http
        permanent: true
        state: enabled

  handlers:
  - name: Restart Nginx
    service:
    name: nginx
    state: restarted
```

---

## **6️⃣ Niveau intermédiaire**
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
        name: ssh
        state: restarted
```

---

## **7️⃣ Niveau avancé**
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
      apt:
        name: httpd
        state: latest
```

---

## **8️⃣ Niveau avancé**
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
      when: ansible_os_family == "Debian"
      loop: apache2, nginx, mysql-server
```

---

## **9️⃣ Niveau difficile**
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
        dest: "/home/{{ newuser }}/.ssh/authorized_keys"
        owner: "{{ newuser }}"
        mode: "0600"
```

---

## **🔟 Niveau très difficile**
```yaml
---
- name: Configurer et sécuriser un serveur
  hosts: all
  become: yes
  tasks:
    - name: Installer des outils de base
      apt:
        update_cache: yes
        name:
          - vim
          - git
          - curl
          - wget
          - unzip

    - name: Ajouter un utilisateur
      user:
        name: "{{ deploy_user }}"
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
        service: ssh
        permanent: true
        state: started

  handlers:
  - name: Restart SSH
    service:
    name: sshd
    state: restarted
```

