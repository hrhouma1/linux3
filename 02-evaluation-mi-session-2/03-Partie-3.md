---
# Partie 3
---

**Trouvez l'erreur !** 

- Ce **playbook Ansible** contient **10 erreurs** qui pourraient empêcher son exécution correcte ou entraîner des comportements inattendus. 
- Votre mission est de les identifier une par une, d'expliquer pourquoi elles posent problème et de proposer une correction adaptée. 


Votre mission :  

-  Identifier chaque erreur.
-  Expliquer pourquoi c'est une erreur.
-  Proposer une correction.  

**Barème :** 2 points par erreur corrigée. **Total : 20 points**.  




### **Fichier `playbook-erreurs.yml` (avec erreurs)**  
```yaml
---
- name: Playbook avec erreurs
  hosts: all
  become: true
  
  tasks:
  
    - name: Installer Apache
      apt:
      name: apache2
      state: present

    - name: Démarrer Apache
      service_gp_update:
        name: apache2
        state: started

    - name: Exécuter une commande shell
      shell: echo "Déploiement terminé

    - name: Copier un fichier 
      copy:
        src: "{{ chemin_source }}"
        dest: "/etc/config.conf"

    - name: Créer un utilisateur
      user:
        name:
          - "toto"
        state: present

    - name: Obtenir l'adresse IP
      command: ip a | grep inet
      register: ip_result
    - debug:
        msg: "L'adresse IP est {{ ip_result }}"

    - name: Installer plusieurs paquets
      apt:
        name: "{{ item }}"
        state: present
      loop:
        [nginx, git, unzip]

    - name: Vérifier la version d'Ubuntu
      debug:
        msg: "Vous êtes sur Ubuntu 20.04"
      when: ansible_distribution_version == 20.04

    - name: Exécuter une commande en root
      command: echo "Hello World"
      become: yes
      become_user: user

    - name: Supprimer un fichier
      file:
        path: /tmp/fichier.txt
      state: absent
```





