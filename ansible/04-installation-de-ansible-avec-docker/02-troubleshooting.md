# Annexe - troubleshooting


Avec cette configuration, le réseau `ansible_network` utilisera le sous-réseau `172.20.0.0/24`, ce qui signifie que Docker assignera des adresses IP dans la plage `172.20.0.1` à `172.20.0.254` aux conteneurs.

### Exemple de Fichier `docker-compose.yml` avec des Adresses IP Fixes

Pour garantir que chaque conteneur conserve la même adresse IP, vous pouvez définir les adresses IP de chaque conteneur directement dans le fichier `docker-compose.yml` :

```yaml
version: '3'
services:
  node1:
    image: ubuntu:latest
    container_name: node1
    networks:
      ansible_network:
        ipv4_address: 172.20.0.2
    volumes:
      - ./ansible_node1:/ansible_node
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

  node2:
    image: ubuntu:latest
    container_name: node2
    networks:
      ansible_network:
        ipv4_address: 172.20.0.3
    volumes:
      - ./ansible_node2:/ansible_node
    command: /bin/bash -c "apt update && apt install -y openssh-server && service ssh start && tail -f /dev/null"
    expose:
      - "22"

networks:
  ansible_network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/24
```

### Configuration du Fichier d'Inventaire Ansible

Avec des adresses IP fixes, vous pouvez désormais configurer le fichier `inventory.ini` comme suit :

```ini
[node_containers]
node1 ansible_host=172.20.0.2 ansible_user=root
node2 ansible_host=172.20.0.3 ansible_user=root

[web]
node1

[database]
node2
```

### Vérification de l'Attribution des Adresses IP

Pour vérifier les adresses IP de chaque conteneur après les avoir démarrés, vous pouvez utiliser la commande suivante :

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node1
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node2
```

### Résumé

1. **Définissez un sous-réseau spécifique** lors de la création du réseau Docker avec `--subnet=172.20.0.0/24`.
2. **Attribuez des adresses IP fixes** aux conteneurs dans le fichier `docker-compose.yml`.
3. **Mettez à jour le fichier `inventory.ini`** d’Ansible en fonction des adresses IP fixes définies.

En utilisant cette méthode, vous contrôlez totalement l'attribution des adresses IP dans le réseau Docker, rendant votre configuration plus prévisible et compatible avec Ansible.
